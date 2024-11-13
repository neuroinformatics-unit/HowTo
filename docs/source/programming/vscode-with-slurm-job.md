# Using VSCode with SLURM-Managed Jobs on the SWC HPC Cluster

This guide explains how to set up and use VSCode within a SLURM-managed job on the SWC HPC cluster, offering a solution for users who require fast access to shared storage or substantial computational resources.

This solution is easy to set up and constrained by the SLURM job's resource limits, ensuring that users can work efficiently without overloading the system.


## Instructions

First, connect to the bastion node by running:

```console
$ ssh <SWC-USERNAME>@ssh.swc.ucl.ac.uk
$ ssh hpc-gw1
```

Once connected, request an interactive job via SLURM to access a compute node:

```console
$ srun -p fast -n 4 --mem 8G --pty bash -i
```

In this example, `-p fast` requests the fast partition, with default time settings, though you may adjust this according to your needs.

After connecting to a compute node, initiate VSCode Code Tunnel by typing:

```console
code tunnel
```

A URL will appear in the terminal, `https://github.com/login/device`. Follow this link, log in with your GitHub credentials, and enter the provided PIN to authorize access. After completing this step, a second link will appear in the terminal (e.g., `https://vscode.dev/tunnel/gpu-XXXX`), which you can follow to access your VSCode session running directly on the HPC compute node.

When you’re finished, simply exit the SLURM session to close the VSCode tunnel and release resources.

### Important Note on Extensions

When using VSCode in this setup, exercise caution with extensions. Some, like the Typescript and Javascript extension, are not recommended by IT due to potential performance issues. Install only essential extensions to maintain system efficiency.

## Additional Benefits of Code Tunnel

One advantage of using VSCode's code tunnel is that it forwards any HTTP servers launched from the same node, such as MLflow or Jupyter Notebook servers. When these are initiated, VSCode will notify you with a link that you can follow to access the server's UI directly.
