(target-jupyter-lab-swc-cluster)=
# Working with Jupyter Lab on the SWC HPC cluster

This guide explains how to connect to the SWC's HPC cluster via SSH.

```{include} ../_static/swc-wiki-warning.md
```

```{include} ../_static/code-blocks-note.md
```

## Abbreviations
| Acronym                                                         | Meaning                                      |
| --------------------------------------------------------------- | -------------------------------------------- |
| [SWC](https://www.sainsburywellcome.org/web/)                   | Sainsbury Wellcome Centre                    |
| [HPC](https://en.wikipedia.org/wiki/High-performance_computing) | High Performance Computing                   |
| [SLURM](https://slurm.schedmd.com/)                             | Simple Linux Utility for Resource Management |
| [SSH](https://en.wikipedia.org/wiki/Secure_Shell)               | Secure (Socket) Shell protocol               |

## Overview

In this guide, we will cover how to connect to the SWC HPC cluster using Jupyter Lab.
This will allow you to develop and run code interactively on the cluster.
This guide is based on Pierre Glaser's 
[Jupyter SLURM setup instructions](https://github.com/pierreglaser/jupyter-slurm-setup-instructions).

## Prerequisites
- You have an SWC account and know your username and password.
- You have read the [SWC wiki's section on High Performance Computing (HPC)](https://wiki.ucl.ac.uk/display/SSC/High+Performance+Computing), especially the [Logging into the Cluster page](https://wiki.ucl.ac.uk/display/SSC/Logging+into+the+Cluster).
- You know the basics of using the command line, i.e. using the terminal to navigate the file system and run commands.
- You have succesfully implemented our [guide on connecting to the SWC cluster via SSH](ssh-cluster-target).

## Setting up

### Start an interactive SLURM session

Assuming you've already set up your SSH connections as specified in the
[SSH to the SWC cluster guide](ssh-cluster-target), you can connect to the cluster
simply by typing `ssh swc-gateway` in your terminal (replace `swc-gateway` with the
name you've assigned to the gateway node in your SSH configuration).

Before we proceed, let's start an interactive SLURM session on one of the compute nodes.
In your terminal, type:

```{code-block} console
$ srun -p fast -n 4 --mem 16G --pty bash -i
```
This will assign you to a compute node with 4 cores and 16GB of memory,
on the `fast` partition (intended for short jobs).

:::{note}
If no nodes are available on the `fast` partition, you can try `medium` or `cpu`.
:::

Your terminal prompt should change from `<SWC-USERNAME>@hpc-gw1` to something like
`<SWC-USERNAME>@enc1-node14`. The part after the `@` symbol is the name of the compute node.

### Create a conda environment for Jupyter Lab
In general, we assume that you use conda to manage your Python environments,
and you use a separate conda environment for each project.

We will set things up so that you can run Jupyter Lab in its own separate environment.

First, let's load the `miniconda` module, which makes the conda command available:

```{code-block} console
$ module load miniconda
$ which conda
/ceph/apps/ubuntu-20/packages/miniconda/23.10.0/condabin/conda
```

Next, create a new conda environment, named "jupyterlab", and install the 
`jupyterlab` package within it. 

```{code-block} console
$ conda create -n jupyterlab -c conda-forge jupyterlab
```

Activate the environment and run `which python` to check where the environment's
Python executable is located:

```{code-block} console
$ conda activate jupyterlab
$ which python
/nfs/nhome/live/<SWC-USERNAME>/.conda/envs/jupyterlab/bin/python
```
Make a note of the path to the "jupyterlab" environment, i.e. the part of the
output before `/bin/python`. It will be something like 
`/nfs/nhome/live/<SWC-USERNAME>/.conda/envs/jupyterlab`. We will need
this path later.

Next we'll configure Jupyter Lab to allow for password-protected access.

```{code-block} console
$ jupyter lab --generate-config
Writing default config to: /nfs/nhome/live/<SWC-USERNAME>/.jupyter/jupyter_lab_config.py
$ jupyter lab password
```

You will be prompted to enter and confirm a password. You will need this password
when logging into Jupyter Lab from you local machine's browser.

### Connect other conda environments to Jupyter Lab

For each conda environment that you want to use in Jupyter Lab, you need to install
an IPython kernel and point Jupyter Lab to it. This is to ensure that the Jupyter
Lab instance you just installed can access the packages in the other environments.

For example, let's imagine that we have a conda environment named `analysis` that
you want to use for your data analysis work.
We'll activate the `analysis` environment and install the `ipykernel` package:

```{code-block} console
$ conda activate analysis
$ conda install -c conda-forge ipykernel
```

Next, we'll make the IPython kernel of the "analysis" environment available to Jupyter Lab
(which, remember, is running in the "jupyterlab" environment):

```{code-block} console
$ python -m ipykernel install --prefix=/nfs/nhome/live/<SWC-USERNAME>/.conda/envs/jupyterlab --name analysis
```
You may get a `[InstallIPythonKernelSpecApp] WARNING` message after running the above command,
but you can ignore it.

The `--prefix` argument should be the path to the "jupyterlab" environment that we noted
earlier, whereas the `--name` argument should be the name of the conda environment
whose IPython kernel you are installing (in this case, "analysis").

To confirm that the kernel has been installed, run:

```{code-block} console
$ conda activate jupyterlab
$ jupyter kernelspec list
Available kernels:
  analysis      /nfs/nhome/live/<SWC-USERNAME>/.conda/envs/jupyterlab/share/jupyter/kernels/analysis
```

End the interactive SLURM session by typing `exit` in the terminal.

## Using Jupyter Lab

### Start Jupyter Lab server on the cluster

Let's connect to the cluster via SSH and start an interactive SLURM session.

```{code-block} console
$ ssh swc-gateway
$ srun --job-name=jupyter -p cpu -n 8 --mem 32G --pty bash -i
```
Importantly, this time we are explicitly naming the job `jupyter` using the `--job-name` argument.
This will make it easier to identify the job in the SLURM queue later.

Note that here we are using the `cpu` partition (intended for longer jobs) and
requesting 8 cores and 32GB of memory. The exact values will vary depending on
your needs (see the [SLURM arguments guide](slurm-arguments-target) for more information).

In the interactive SLURM session, load the `miniconda` module and activate the `jupyterlab` environment:

```{code-block} console
$ module load miniconda
$ conda activate jupyterlab
```

Next, start Jupyter Lab in headless mode:

```{code-block} console
$ jupyter lab --port=8888 --no-browser
```

:::{dropdown} What's happening here?
:color: info
:icon: info
We are running Jupyter Lab in headless mode because we cannot use a browser on the
compute node. Instead we will use the compute node as a Jupyter server and connect
to it from our local machine's browser.

The `--no-browser` argument tells Jupyter Lab not to open a browser window.

The `--port=8888` argument specifies the port on which Jupyter Lab will run.
Port numbers like 8888, 8889, etc., are commonly used for Jupyter and similar
applications. If you know these aren't in use on your machine, they are generally safe choices.
You can check if a specific port is in use by running `lsof -i :PORT_NUMBER`.
If the output is empty, the port is free.
:::

The output of the `jupyter lab` command will include a URL that you can use to connect
to the Jupyter Lab instance. It will look something like this:

```{code-block} console
http://127.0.0.1:8888/lab
# or equivalently
http://localhost:8888/lab
```

Note that the URL will not be clickable, as the compute node does not have a browser.
We will note down this URL and leave the terminal open.

### Connect to Jupyter Lab from your local machine

Open a new terminal window/tab on your local machine.

Take care not to close the terminal window/tab that is connected to the cluster
(because that would stop the interactive SLURM session and with it the Jupyter Lab server).

Copy and paste the following bash script into a text file on your local machine.
You can use `nano`, `vim`, `gedit`, or any other text editor of your choice

```{code-block} bash
#!/usr/bin/env bash
set -o errexit

# Check if all required arguments are provided
if [ $# -lt 3 ]; then
    echo "Usage: $0 <username> <local_port> <remote_port>"
    exit 1
fi

username=$1
local_port=$2
remote_port=$3

echo "querying current jupyter sessions..."
jupyter_host_name=$(ssh swc-gateway squeue --name=jupyter --user="$username" -o "%R" | grep -v NODELIST || true)

if [[ -z "${jupyter_host_name}" ]]; then
    echo "no jupyter session found, exiting."
    exit
else
    num_lines=$(echo "${jupyter_host_name}" | wc -l)
    if [[ $num_lines -ne 1 ]]; then
        echo "there is more than one jupyter session ($num_lines)"
        echo "${jupyter_host_name}"
        echo "exiting."
        exit
    else
        echo "jupyter session found at ${jupyter_host_name}, setting up port forwarding..."
        ssh -t swc-gateway -L ${local_port}:localhost:${remote_port} ssh -q -N ${jupyter_host_name} -L ${local_port}:localhost:${remote_port}
    fi
fi

```

Replace `swc-gateway` with the name you've assigned to the gateway node in your SSH configuration.
Save the script using a meaningful name e.g. `connect_to_jupyter.sh` and
make it executable with `chmod +x connect_to_jupyter.sh`.

:::{dropdown} What does the script do?
:color: info
:icon: info

This script queries the SLURM queue for the compute node running a job named `jupyter`
that belongs to the specified user. If it finds exactly one such node, it sets
up an SSH tunnel between the local port you specify and the remote port on the
compute node where Jupyter Lab is running.
:::

The script takes three arguments, in the following order:
- `<username>`: your SWC username
- `<local_port>`: the port on your local machine that you want to use to connect to the Jupyter Lab server. We use `8888` in the example below.
- `<remote_port>`: the port on the compute node where Jupyter Lab is running. We use `8888`, as specified in the `jupyter lab` command above.

You can run the script on the new terminal window/tab with the following command:

```{code-block} console
$ ./connect_to_jupyter.sh <SWC-USERNAME> 8888 8888
querying current jupyter sessions...
jupyter session found at enc1-node14, setting up port forwarding...
```

Make sure to replace `<SWC-USERNAME>` with your actual SWC username and `8888` with 
the actual port numbers you are using. The local and remote port numbers do not have to match,
but the remote port number should match the one specified in the `jupyter lab` command.

Now open your browser and navigate to `http://localhost:8888/lab` (the URL you noted down earlier).
You will be prompted to enter the password you set up earlier.
After that you should see the Jupyter Lab interface, with "analysis" as one of the available kernels.








