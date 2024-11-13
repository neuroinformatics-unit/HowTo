# Using VSCode with SLURM-Managed jobs on the SWC HPC cluster

This guide explains how to set up and use VSCode within a SLURM-managed job on the SWC HPC cluster, offering a solution for users who require fast access to shared storage or substantial computational resources.

This solution is easy to set up and constrained by the SLURM job's resource limits, ensuring that users can work efficiently without overloading the system.


## Instructions

First, connect to the bastion node by running:

```bash
$ ssh <SWC-USERNAME>@ssh.swc.ucl.ac.uk
$ ssh hpc-gw1
```

Once connected, request an interactive job via SLURM to access a compute node:

```bash
$ srun -p fast -n 4 --mem 8G --pty bash -i
```

In this example, `-p fast` requests the fast partition, with default time settings, though you may adjust this according to your needs.

After connecting to a compute node, initiate VSCode Code Tunnel by typing:

```bash
code tunnel
```

A URL will appear in the terminal, `https://github.com/login/device`. Follow this link, log in with your GitHub credentials, and enter the provided PIN to authorize access. After completing this step, a second link will appear in the terminal (e.g., `https://vscode.dev/tunnel/gpu-XXXX`), which you can follow to access your VSCode session running directly on the HPC compute node.

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

One advantage of using VSCode's code tunnel is that it forwards any HTTP servers launched from the same node, such as MLflow or Jupyter Notebook servers. When these are initiated, VSCode will notify you with a link that you can follow to access the server's UI directly.
