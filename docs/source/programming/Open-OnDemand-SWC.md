(open-ondemand-target)=
# Use Open OnDemand on the SWC HPC

This guide explains how to use [Open OnDemand](https://openondemand.org/) (OOD)
to access the SWC HPC cluster from your browser. OOD provides a web portal
to the cluster: you can browse files, submit and monitor SLURM jobs, open
a shell, and — most usefully — launch a full Linux **remote desktop** running
on a compute node, all without setting up SSH or X11 locally.

If you prefer the terminal, see {ref}`ssh-cluster-target` instead.

```{include} ../_static/swc-wiki-warning.md
```

```{include} ../_static/code-blocks-note.md
```

## Abbreviations
| Acronym                                                                 | Meaning                                      |
| ----------------------------------------------------------------------- | -------------------------------------------- |
| [OOD](https://openondemand.org/)                                        | Open OnDemand                                |
| [SWC](https://www.sainsburywellcome.org/web/)                           | Sainsbury Wellcome Centre                    |
| [HPC](https://en.wikipedia.org/wiki/High-performance_computing)         | High Performance Computing                   |
| [VPN](https://en.wikipedia.org/wiki/Virtual_private_network)            | Virtual Private Network                      |
| [VNC](https://en.wikipedia.org/wiki/Virtual_Network_Computing)          | Virtual Network Computing                    |
| [GPU](https://en.wikipedia.org/wiki/Graphics_processing_unit)           | Graphics Processing Unit                     |
| [GRES](https://slurm.schedmd.com/gres.html)                             | Generic RESources (SLURM)                    |
| [SLURM](https://slurm.schedmd.com/)                                     | Simple Linux Utility for Resource Management |
| [GUI](https://en.wikipedia.org/wiki/Graphical_user_interface)           | Graphical User Interface                     |

## Prerequisites
- You have an SWC account and know your username and password.
- You are either on the SWC network (e.g. at the SWC offices, or on a managed desktop) **or** connected via the SWC VPN.
- You have read at least the [Log into the cluster](SSH-SWC-cluster.md#log-into-the-cluster) and [Why do we SSH twice?](SSH-SWC-cluster.md#why-do-we-ssh-twice) sections of {ref}`ssh-cluster-target` — the same node terminology (*bastion*, *gateway*, *compute*) is used below.
- For anything beyond browsing files, you should also be familiar with the {ref}`slurm-arguments-target`, since OOD's interactive apps are just SLURM jobs with a pre-filled form.

## Log in to OOD

Open the following URL in any modern browser:

> <https://hpc-gw2.hpc.swc.ucl.ac.uk/pun/sys/dashboard/>

Sign in with your SWC username and password. You'll land on the OOD dashboard.

![](../_static/ood_dashboard.png)

::: {note}
If the page won't load at all, the most common cause is that you are not on the SWC network. Connect to the SWC VPN and try again.
:::

## A tour of the dashboard

The top menu bar gives you access to all of OOD's features:

- **Files** — browse, edit, upload and download files in your home directory and on `/ceph`.
- **Jobs** — *Active Jobs* shows the SLURM queue; *Job Composer* lets you build and submit batch jobs from templates.
- **Clusters** — *_swc Shell Access* opens a terminal connected to the *gateway* node, right inside the browser.
- **Interactive Apps** — launch GUI applications on a *compute* node. The most important one is **Desktop** (a full Linux remote desktop); other apps may be enabled depending on demand (e.g. Jupyter, RStudio, MATLAB).
- **My Interactive Sessions** — list of your running and recently-finished interactive sessions, with reconnect and delete buttons.

The next sections walk through each area in turn.

## Files

The Files menu opens a web-based file browser rooted at any path you choose.
Common entry points are your home directory (`/nfs/nhome/live/<SWC-USERNAME>`)
and your project folders on `/ceph`.

![](../_static/ood_files.png)

From here you can:
- Navigate, create, rename, delete files and folders.
- **View** and **Edit** small text files directly in the browser (there is a built-in editor with syntax highlighting).
- **Upload** files by drag-and-drop, and **Download** individual files or whole folders as zip archives.

:::{warning}
The browser uploader is fine for small files (scripts, configs, a few MB of data), but **do not use it to move large datasets**. For anything heavy, use `rsync` or `scp` from a terminal — it is faster, resumable, and won't tie up your browser tab.
:::

## Jobs

### Active Jobs

*Jobs → Active Jobs* shows the current SLURM queue, similar to running `squeue`
from a terminal. You can filter by user (default: *Your Jobs*), cluster and state,
and expand a row to see the full job details.

![](../_static/ood_active_jobs.png)

This is a quick way to check whether your job is running, queued, or stuck —
without having to SSH in.

### Job Composer

*Jobs → Job Composer* lets you build a SLURM job from a template, edit the
script in the browser, and submit it. It is convenient for one-off jobs, but
the templates available are limited. For anything non-trivial, prefer writing
your own `sbatch` script — see {ref}`slurm-arguments-target` for the directives
you'll need.

## Shell access

*Clusters → _swc Shell Access* opens a fully-featured bash terminal in a browser
tab, already connected to the *gateway* node (`hpc-gw2`).

![](../_static/ood_shell.png)

The same rules as a regular SSH session apply: the *gateway* is a shared,
low-power node, so don't run heavy work there. Request an interactive job
on a *compute* node with `srun` first (see {ref}`ssh-cluster-target` for an
example).

## The Desktop app

This is the headline feature: a full Linux desktop, running on a *compute* node
of your choice, accessed through the browser via VNC. You get a window manager,
a terminal, a file manager and a web browser, with the cluster's filesystems
already mounted.

### Launching a Desktop session

From the top menu, go to **Interactive Apps → Desktop**. You'll see a form
where you choose the resources for your session:

![](../_static/ood_desktop_form.png)

Fill in:

- **Partition** — the SLURM partition. Use `cpu` if you don't need a GPU, or one of the GPU partitions (`gpu`, `gpu_branco`, `gpu_lowp`, …) if you do.
- **Number of CPUs** — start with `4` for light interactive work; bump it up if you'll be running parallel code.
- **Memory (GB)** — `8`–`16` is plenty for browsing data and editing code. Increase if you'll load large arrays.
- **Wall time** — how long the session may run before SLURM kills it. Pick the shortest time that comfortably covers your task; shorter requests start sooner.
- **GPU type** and **Number of GPUs** — only relevant on a GPU partition.
- **Node** *(optional)* — leave blank to let SLURM pick. Set a specific hostname (e.g. `gpu-sr675-34`) only when you have a reason to.

:::{dropdown} Which partition should I pick?
:color: info
:icon: info

The partitions map roughly to: `cpu` for CPU-only work, `gpu` for the main GPU pool, `gpu_branco` for the Branco-lab nodes, `gpu_lowp` for short, lower-priority jobs on idle GPU nodes, and a few others. The exact list and the hardware in each evolves over time — the authoritative reference is the SWC wiki page on the cluster. See also {ref}`slurm-arguments-target` for a longer discussion of how to choose.
:::

:::{dropdown} GPU type labels and the underlying SLURM names
:color: warning
:icon: alert

Since October 2025, SLURM detects the GPUs automatically via the `gpu-nvml`
plugin. As a side effect, the GRES names for three card types changed:

| Old label  | What SLURM now sees           |
|------------|-------------------------------|
| `rtx5000`  | `quadro_rtx_5000`             |
| `rtx4000`  | `nvidia_rtx_4000_ada_generation` |
| `rtx2080`  | `nvidia_geforce_rtx_2080`     |

The OOD Desktop form still shows the short labels in its dropdown, but it
passes the correct (new) names to SLURM, so the selection still works.
You only need to know the new names if you write your own `sbatch` script
with `--gres=gpu:<type>:N`.
:::

Click **Launch**. The session is now a SLURM job: it appears in
**My Interactive Sessions** as *Queued*, and switches to *Running* once
SLURM allocates a node.

### Connecting and using the desktop

When the session card turns green and shows *Running*, click **Launch Desktop**
(or the noVNC link). A new tab opens with your remote desktop.

![](../_static/ood_desktop_running.png)

From here you have a normal Linux desktop on the compute node. Open the
terminal and you can immediately use `module`, `conda`, etc.:

```{code-block} console
$ module avail
$ module load miniconda
$ nvidia-smi  # on a GPU node
```

🎉 You now have an interactive GPU/CPU session on the cluster with no local SSH or X11 setup.

:::{dropdown} Session won't launch, or shows a noVNC / "failed to connect" error
:color: warning
:icon: alert

If the session sits in *Queued* for a long time, your request is just waiting
for resources — try a smaller request (fewer CPUs, less memory, shorter time)
or a less busy partition.

If the session reaches *Running* but the desktop fails to open with a noVNC
error, the usual cause is a node-specific reverse-proxy or VNC problem.
A subtle issue of this kind affecting some H100 nodes was fixed in
October 2025, but new ones can appear. To work around:

1. **Delete** the broken session from *My Interactive Sessions*.
2. Try launching again — without pinning to a specific node — so SLURM picks a different host.
3. If it still fails, raise a ticket with SWC IT (the "SWC assist" channel) and include the logs from `~/ondemand/data/sys/dashboard/batch_connect/sys/bc_desktop/<cluster>/output/<session-id>/`, in particular `output.log` and `vnc.log`.
:::

### Modules and conda inside the Desktop

The Desktop terminal is a **login shell**, so `/etc/profile` runs and
`MODULEPATH` is set automatically. `module avail` and `module load miniconda`
work without any extra steps.

If you have customised your shell so that `conda` or specific modules are
only loaded from `~/.bashrc`, you'll notice that your default conda
environment **is not** activated inside the Desktop session. This is not
an OOD bug — it's how login vs non-login shells work.

:::{dropdown} Login shells, .bash_profile and .bashrc — what's happening?
:color: info
:icon: info

- A **login shell** (used by OOD Desktop, by SSH, and by the in-browser Shell) reads `~/.bash_profile` (or `~/.profile`).
- A **non-login shell** (used by SLURM batch jobs and by sub-shells) reads `~/.bashrc`.

So if you put `conda init` and `module load …` in `.bashrc`, your batch jobs
"just work" but your interactive Desktop session looks bare. The simplest
fix is to make `.bash_profile` source `.bashrc`:

```{code-block} bash
:caption: ~/.bash_profile

if [ -f "$HOME/.bashrc" ]; then
    . "$HOME/.bashrc"
fi
```

After logging in again, your conda environments and module setup will be
available in the Desktop terminal too.
:::

## Other Interactive Apps

Beyond Desktop, SWC's OOD instance may expose other apps in the
**Interactive Apps** menu (for example Jupyter, RStudio, or MATLAB).
The launch flow is the same as for Desktop: pick resources, click **Launch**,
wait for *Running*, then click **Connect**.

:::{note}
The exact list of apps available on the SWC instance changes over time. If
your favourite tool isn't there, you can almost always launch it manually
from a terminal inside the Desktop, or via the [browser Shell](#shell-access)
after an `srun`. For browser-based tools like Jupyter Lab, see the
[port-forwarding guide](port-fowarding.md) as an SSH-based alternative.
:::

## My Interactive Sessions

This view lists all your running and recently-finished sessions:

- **Connect** / **Launch Desktop** to (re)open a session in a new tab.
- **Time remaining** counts down to the SLURM wall-time limit.
- **Delete** terminates the session immediately and releases the resources.

:::{warning}
Closing the browser tab **does not** end the session — the SLURM job keeps
running until its wall-time expires. Always click **Delete** on sessions you
are done with, especially GPU ones, so the node is freed for others.
:::

## Current state and known quirks

OOD on `hpc-gw2` is actively maintained but still evolving. As of 2026, the
points worth being aware of are:

- **Stability** — Desktop sessions work reliably across all partitions after
  the October 2025 reverse-proxy fix. Occasional node-specific noVNC errors
  can still happen; the workaround above (delete + relaunch) handles most cases.
- **GPU labels** — the dropdown labels in the Desktop form (e.g. `rtx5000`)
  are cosmetic; the underlying GRES names changed in October 2025 (see the
  dropdown in [Launching a Desktop session](#launching-a-desktop-session)).
  If you write your own `sbatch` scripts, use the new names.
- **GPU accounting** is newly enabled (via the `gpu-nvml` plugin). It's not
  yet user-visible, but is expected to inform fair-use policies in the future.
- **Job Composer templates** are sparse. For anything beyond a quick test,
  write your own `sbatch` script as described in {ref}`slurm-arguments-target`.
- **App catalogue** — Desktop is well-supported; other Interactive Apps come
  and go depending on demand. If you'd like a new one enabled, ask SWC IT.

For anything that looks broken (or for feature requests), the SWC IT team is
reachable via the SWC assist channel.
