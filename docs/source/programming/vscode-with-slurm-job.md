# Using VSCode with SLURM-managed jobs on the SWC HPC cluster

This guide explains how to set up and use VSCode within a SLURM-managed job on the SWC HPC cluster, offering a solution for users who require fast access to shared storage or substantial computational resources.

This solution is easy to set up and constrained by the SLURM job's resource limits, ensuring that users can work efficiently without overloading the system.


## Instructions

First, open a terminal (not the VSCode terminal) and connect to the gateway node by running:

```{code-block} console
$ ssh <SWC-USERNAME>@ssh.swc.ucl.ac.uk
$ ssh hpc-gw1
```

Once connected, request an interactive job via SLURM to access a compute node. For example:

```{code-block} console
$ srun -p fast -n 4 --mem 8G --pty bash -i
```

In this example, `-p fast` requests the fast partition, with default time settings, though you may adjust this according to your needs. For more information, see the [SLURM arguments primer](https://howto.neuroinformatics.dev/programming/SLURM-arguments.html).

After connecting to a compute node, initiate VSCode Code Tunnel by typing:

```{code-block} console
$ code tunnel
```

A URL will appear in the terminal, `https://github.com/login/device`.
Follow this link, log in with your GitHub credentials, and enter the provided PIN to authorize access.

You have two options to run VSCode:

-  **Run VSCode in  the browser:**
    After completing the above step, a second link will appear in the terminal (e.g., `https://vscode.dev/tunnel/gpu-XXXX`), which you can follow to access your VSCode session running directly on the HPC compute node.

- **Run VSCode on your local machine:**
    If you want instead to use your local VSCode, install the "Remote - Tunnels" extension, click on "Open remote window" in the bottom left corner of the VSCode window, and select "Connect to Tunnel". You should see the name "gpu-XXXX" in the list of available tunnels. Click on it to connect to the VSCode session running on the HPC compute node.

NB: the GPU name in the URL provided might not match the actual node name.

If by mistake you close your terminal window, the tunnel will continue to run till you reach the time limit. To rejoin the slurm job, you can use the following command if you know the job ID:

```{code-block} console
$ sattach <JOBID>.0
```

When you’re finished, simply exit the SLURM session to close the VSCode tunnel and release resources.

::: {dropdown} Why do I have to authenticate via GitHub?
:color: info
:icon: info

As explained in [vscode docs](https://code.visualstudio.com/docs/remote/tunnels#:~:text=When%20opening%20a%20vscode.,right%20set%20of%20remote%20machines.) it serves as a secure way to authenticate the user and ensure that only the user who initiated the tunnel can access it:
> Tunneling securely transmits data from one network to another via [Microsoft dev tunnels](https://learn.microsoft.com/azure/developer/dev-tunnels/overview).
>
> Both hosting and connecting to a tunnel requires authentication with the same Github or Microsoft account on each end. In both cases, VS Code will make outbound connections to a service hosted in Azure; no firewall changes are generally necessary, and VS Code doesn't set up any network listeners.
>
>Once you connect from a remote VS Code instance, an SSH connection is created over the tunnel in order to provide end-to-end encryption.
:::

## Additional benefits of code tunnel

One advantage of using VSCode's code tunnel is that it forwards any HTTP servers launched from the same node, such as Dash-Plotly apps or Jupyter Notebook servers. To launch your additional server, request a separate slurm job for the same compute node, e.g.:

```{code-block} console
$ srun -p gpu -w <gpu-XXXX> -n 4 --mem 8G --pty bash -i
```
When these are initiated, VSCode will notify you with a link that you can follow to access the server's UI directly.

In order to do so, can request a separate slurm job for the same node to run your alternative sever
