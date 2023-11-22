# Use the SLEAP module on the HPC cluster

```{role} bash(code)
:language: bash
```
```{role} python(code)
:language: python
```

This guide explains how to use the [SLEAP](https://sleap.ai/) module that is
installed on the SWC's HPC cluster to run training and/or inference jobs.

:::{warning}
Some links within this document point to the
[SWC internal wiki](https://wiki.ucl.ac.uk/display/SI/SWC+Intranet),
which is only accessible from within the SWC network.
:::

:::{dropdown} Interpreting code blocks wihin this document
:color: info
:icon: info

Shell commands will be shown in code blocks like this
(with the `$` sign indicating the shell prompt):
```{code-block} console
$ echo "Hello world!"
```

Similarly, Python code blocks will appear with the `>>>` sign indicating the
Python interpreter prompt:
```{code-block} pycon
>>> print("Hello world!")
```

The expected outputs of both shell and Python commands will be shown without
any prompt:
```{code-block} console
Hello world!
```
:::

## Abbreviations
| Acronym | Meaning |
| --- | --- |
| SLEAP | Social LEAP Estimates Animal Poses |
| SWC | Sainsbury Wellcome Centre |
| HPC | High Performance Computing |
| SLURM | Simple Linux Utility for Resource Management |
| GUI | Graphical User Interface |

## Prerequisites

### Access to the HPC cluster and SLEAP module
Verify that you can access HPC gateway node (typing your `<SWC-PASSWORD>` both times when prompted):
```{code-block} console
$ ssh <SWC-USERNAME>@ssh.swc.ucl.ac.uk
$ ssh hpc-gw1
```

If you are wondering about the two SSH commands, see the Appendix for
[Why do we SSH twice?](#why-do-we-ssh-twice).


SLEAP should be listed among the available modules:

```{code-block} console
$ module avail
SLEAP/2023-08-01
SLEAP/2023-03-13
```

`SLEAP/2023-03-13` corresponds to `sleap v.1.2.9` whereas `SLEAP/2023-08-01` is `v1.3.1`.
We recommend using the latter.

You can load the latest version by running:

```{code-block} console
$ module load SLEAP
```
If you want to load a specific version, you can do so by typing the full module name,
including the date e.g. `module load SLEAP/2023-03-13`

If a module has been successfully loaded, it will be listed when you run `module list`,
along with other modules it may depend on:

```{code-block} console
$ module list
Currently Loaded Modulefiles:
 1) cuda/11.8   2) SLEAP/2023-08-01
```

If you have troubles with loading the SLEAP module, see the
[Troubleshooting section](#problems-with-the-sleap-module).


### Install SLEAP on your local PC/laptop
While you can delegate the GPU-intensive work to the HPC cluster,
you will need to use the SLEAP GUI for some steps, such as labelling frames.
Thus, you also need to install SLEAP on your local PC/laptop.

We recommend following the official [SLEAP installation guide](https://sleap.ai/installation.html).
To be on the safe side, ensure that the local installation version matches the one on the cluster.

### Mount the SWC filesystem on your local PC/laptop
The rest of this guide assumes that you have mounted the SWC filesystem on your local PC/laptop.
If you have not done so, please follow the relevant instructions on the
[SWC internal wiki](https://wiki.ucl.ac.uk/display/SSC/SWC+Storage+Platform+Overview).

We will also assume that the data you are working with are stored in a `ceph` or `winstor`
directory to which you have access to. In the rest of this guide, we will use the path
`/ceph/scratch/neuroinformatics-dropoff/SLEAP_HPC_test_data` which contains a SLEAP project
for test purposes. You should replace this with the path to your own data.

:::{dropdown} Data storage location matters
:color: warning
:icon: alert-fill

The cluster has fast access to data stored on the `ceph` and `winstor` filesystems.
If your data is stored elsewhere, make sure to transfer it to `ceph` or `winstor`
before running the job. You can use tools such as [`rsync`](https://linux.die.net/man/1/rsync)
to copy data from your local machine to `ceph` via an ssh connection. For example:

```{code-block} console
$ rsync -avz <LOCAL-DIR> <SWC-USERNAME>@ssh.swc.ucl.ac.uk:/ceph/scratch/neuroinformatics-dropoff/SLEAP_HPC_test_data
```
:::

## Model training
This will consist of two parts - [preparing a training job](#prepare-the-training-job)
(on your local SLEAP installation) and [running a training job](#run-the-training-job)
(on the HPC cluster's SLEAP module). Some evaluation metrics for the trained models
can be [viewed via the SLEAP GUI](#evaluate-the-trained-models) on your local SLEAP installation.

### Prepare the training job
Follow the SLEAP instructions for [Creating a Project](https://sleap.ai/tutorials/new-project.html)
and [Initial Labelling](https://sleap.ai/tutorials/initial-labeling.html).
Ensure that the project file (e.g. `labels.v001.slp`) is saved in the mounted SWC filesystem
(as opposed to your local filesystem).

Next, follow the instructions in [Remote Training](https://sleap.ai/guides/remote.html#remote-training),
i.e. "Predict" -> "Run Training…" -> "Export Training Job Package…".
- For selecting the right configuration parameters, see [Configuring Models](https://sleap.ai/guides/choosing-models.html#) and [Troubleshooting Workflows](https://sleap.ai/guides/troubleshooting-workflows.html)
- Set the "Predict On" parameter to "nothing". Remote training and inference (prediction) are easiest to run separately on the HPC Cluster. Also unselect "visualize predictions" in training settings, if it's enabled by default.
- If you are working with a top-down camera view, set the "Rotation Min Angle" and "Rotation Max Angle" to -180 and 180 respectively in the "Augmentation" section.
- Make sure to save the exported training job package (e.g. `labels.v001.slp.training_job.zip`) in the mounted SWC filesystem, for example, in the same directory as the project file.
- Unzip the training job package. This will create a folder with the same name (minus the `.zip` extension). This folder contains everything needed to run the training job on the HPC cluster.

### Run the training job
Login to the HPC cluster as described above.
```{code-block} console
$ ssh <SWC-USERNAME>@ssh.swc.ucl.ac.uk
$ ssh hpc-gw1
```
Navigate to the training job folder (replace with your own path) and list its contents:
```{code-block} console
$ cd /ceph/scratch/neuroinformatics-dropoff/SLEAP_HPC_test_data
$ cd labels.v001.slp.training_job
$ ls -1
centered_instance.json
centroid.json
inference-script.sh
jobs.yaml
labels.v001.pkg.slp
labels.v001.slp.predictions.slp
slurm-train-script.sh
swc-hpc-pose-estimation
train-script.sh
```
There should be a `train-script.sh` file created by SLEAP, which already contains the
commands to run the training. You can see the contents of the file by running `cat train-script.sh`:
```{code-block} bash
:caption: train-script.sh
:name: train-script-sh
:linenos:
#!/bin/bash
sleap-train centroid.json labels.v001.pkg.slp
sleap-train centered_instance.json labels.v001.pkg.slp
```
The precise commands will depend on the model configuration you chose in SLEAP.
Here we see two separate training calls, one for the "centroid" and another for
the "centered_instance" model. That's because in this example we have chosen
the ["Top-Down"](https://sleap.ai/tutorials/initial-training.html#training-options)
configuration, which consists of two neural networks - the first for isolating
the animal instances (by finding their centroids) and the second for predicting
all the body parts per instance.

![Top-Down model configuration](https://sleap.ai/_images/topdown_approach.jpg)

:::{dropdown} More on "Top-Down" vs "Bottom-Up" models
:color: info
:icon: info

Although the "Top-Down" configuration was designed with multiple animals in mind,
it can also be used for single-animal videos. It makes sense to use it for videos
where the animal occupies a relatively small portion of the frame - see
[Troubleshooting Workflows](https://sleap.ai/guides/troubleshooting-workflows.html) for more info.
:::

Next you need to create a SLURM batch script, which will schedule the training job
on the HPC cluster. Create a new file called `slurm-train-script.sh`
(You can do this in the terminal with `nano`/`vim` or in a text editor of
your choice on your local PC/laptop). Here we create the script in the same folder
as the training job, but you can save it anywhere you want, or even keep track of it with `git`.

```{code-block} console
$ nano slurm-train-script.sh
```

An example is provided below, followed by explanations.
```{code-block} bash
:caption: slurm-train-script.sh
:name: slurm-train-script-sh
:linenos:
#!/bin/bash

#SBATCH -p gpu # partition
#SBATCH -N 1   # number of nodes
#SBATCH --mem 12G # memory pool for all cores
#SBATCH -n 2 # number of cores
#SBATCH -t 0-04:00 # time (D-HH:MM)
#SBATCH --gres gpu:1 # request 1 GPU (of any kind)
#SBATCH -o slurm.%N.%j.out # write STDOUT
#SBATCH -e slurm.%N.%j.err # write STDERR
#SBATCH --mail-type=ALL
#SBATCH --mail-user=name@domain.com

# Load the SLEAP module
module load SLEAP

# Define directories for data and exported training job
DATA_DIR=/ceph/scratch/neuroinformatics-dropoff/SLEAP_HPC_test_data
JOB_DIR=$DATA_DIR/labels.v001.slp.training_job
# Go to the job directory
cd $JOB_DIR

# Run the training script generated by SLEAP
./train-script.sh
```

In `nano`, you can save the file by pressing `Ctrl+O` and exit by pressing `Ctrl+X`.

:::{dropdown} Explanation of the batch script
:color: info
:icon: info
- The `#SBATCH` lines are SLURM directives. They specify the resources needed
for the job, such as the number of nodes, CPUs, memory, etc.
A primer on the most useful SLURM arguments is provided in the [appendix](#slurm-arguments-primer).
For more information  see the [SLURM documentation](https://slurm.schedmd.com/sbatch.html).

- The `#` lines are comments. They are not executed by SLURM, but they are useful
for explaining the script to your future self and others.

- The `module load SLEAP` line loads the latest SLEAP module and any other modules
it may depend on.

- The `cd` line changes the working directory to the training job folder.
This is necessary because the `train-script.sh` file contains relative paths
to the  model configuration and the project file.

- The `./train-script.sh` line runs the training job (executes the contained commands).
:::

Now you can submit the batch script via running the following command
(in the same directory as the script):
```{code-block} console
$ sbatch slurm-train-script.sh
Submitted batch job 3445652
```
:::{warning}
If you are getting a permission error, make the script files executable
by running in the terminal:

```{code-block} console
$ chmod +x train-script.sh
$ chmod +x slurm-train-script.sh
```

If the scripts are not in the same folder, you will need to specify the full path:
`chmod +x /path/to/script.sh`
:::

You may monitor the progress of the job in various ways:

::::{tab-set}

:::{tab-item} squeue

View the status of the queued/running jobs with [`squeue`](https://slurm.schedmd.com/squeue.html):

```{code-block} console
$ squeue -u <SWC-USERNAME>
JOBID    PARTITION  NAME     USER      ST  TIME   NODES  NODELIST(REASON)
3445652  gpu        slurm_ba sirmpila  R   23:11  1      gpu-sr670-20
```
:::

:::{tab-item} sacct

View status of running/completed jobs with [`sacct`](https://slurm.schedmd.com/sacct.html):

```{code-block} console
$ sacct -u <SWC-USERNAME>
JobID           JobName  Partition    Account  AllocCPUS      State ExitCode
------------ ---------- ---------- ---------- ---------- ---------- --------
3445652      slurm_bat+        gpu     swc-ac          2  COMPLETED      0:0
3445652.bat+      batch                swc-ac          2  COMPLETED      0:0
```
Run `sacct` with some more helpful arguments
(view jobs from the last 24 hours, including the time elapsed):

```{code-block} console
$ sacct -u nstest \
--starttime $(date -d '24 hours ago' +%Y-%m-%dT%H:%M:%S) \
--endtime $(date +%Y-%m-%dT%H:%M:%S) \
--format=JobID,JobName,Partition,AllocCPUS,State,Start,End,Elapsed,MaxRSS
```
:::

:::{tab-item} view the logs

View the contents of standard output and error
(the node name and job ID will differ in each case):
```{code-block} console
$ cat slurm.gpu-sr670-20.3445652.out
$ cat slurm.gpu-sr670-20.3445652.err
```
:::

::::

### Evaluate the trained models
Upon successful completion of the training job, a `models` folder will have
been created in the training job directory. It contains one subfolder per
training run (by default prefixed with the date and time of the run).

```{code-block} console
$ cd /ceph/scratch/neuroinformatics-dropoff/SLEAP_HPC_test_data
$ cd labels.v001.slp.training_job
$ cd models
$ ls -1
230509_141357.centered_instance
230509_141357.centroid
```

Each subfolder holds the trained model files (e.g. `best_model.h5`),
their configurations (`training_config.json`) and some evaluation metrics.

```{code-block} console
$ cd 230509_141357.centered_instance
$ ls -1
best_model.h5
initial_config.json
labels_gt.train.slp
labels_gt.val.slp
labels_pr.train.slp
labels_pr.val.slp
metrics.train.npz
metrics.val.npz
training_config.json
training_log.csv
```
The SLEAP GUI on your local machine can be used to quickly evaluate the trained models.

- Select "Predict" -> "Evaluation Metrics for Trained Models..."
- Click on "Add Trained Models(s)" and select the subfolder(s) containing the model(s) you want to evaluate (e.g. `230509_141357.centered_instance`).
- You can view the basic metrics on the shown table or you can also view a more detailed report (including plots) by clicking "View Metrics".

## Model inference
By inference, we mean using a trained model to predict the labels on new frames/videos.
SLEAP provides the `sleap-track` command line utility for running inference
on a single video or a folder of videos.

Below is an example SLURM batch script that contains a `sleap-track` call.
```{code-block} bash
:caption: slurm-infer-script.sh
:name: slurm-infer-script-sh
:linenos:
#!/bin/bash

#SBATCH -p gpu # partition
#SBATCH -N 1   # number of nodes
#SBATCH --mem 12G # memory pool for all cores
#SBATCH -n 2 # number of cores
#SBATCH -t 0-01:00 # time (D-HH:MM)
#SBATCH --gres gpu:1 # request 1 GPU (of any kind)
#SBATCH -o slurm.%N.%j.out # write STDOUT
#SBATCH -e slurm.%N.%j.err # write STDERR
#SBATCH --mail-type=ALL
#SBATCH --mail-user=name@domain.com

# Load the SLEAP module
module load SLEAP

# Define directories for data and exported training job
DATA_DIR=/ceph/scratch/neuroinformatics-dropoff/SLEAP_HPC_test_data
JOB_DIR=$DATA_DIR/labels.v001.slp.training_job
# Go to the job directory
cd $JOB_DIR

# Run the inference command
sleap-track $DATA_DIR/videos/M708149_EPM_20200317_165049331-converted.mp4 \
    -m $JOB_DIR/models/230509_141357.centroid/training_config.json \
    -m $JOB_DIR/models/230509_141357.centered_instance/training_config.json \
    --gpu auto \
    --tracking.tracker none \
    -o labels.v001.slp.predictions.slp \
    --verbosity json \
    --no-empty-frames
```
The script is very similar to the training script, with the following differences:
- The time limit `-t` is set lower, since inference is normally faster than training. This will however depend on the size of the video and the number of models used.
- The `./train-script.sh` line is replaced by the `sleap-track` command.
- The `\` character is used to split the long `sleap-track` command into multiple lines for readability. It is not necessary if the command is written on a single line.

::: {dropdown} Explanation of the sleap-track arguments
:color: info
:icon: info

 Some important command line arguments are explained below.
 You can view a full list of the available arguments by running `sleap-track --help`.
- The first argument is the path to the video file to be processed.
- The `-m` option is used to specify the path to the model configuration file(s) to be used for inference. In this example we use the two models that were trained above.
- The `--gpu` option is used to specify the GPU to be used for inference. The `auto` value will automatically select the GPU with the highest percentage of available memory (of the GPUs that are available on the machine/node)
- The `--tracking.tracker` option is used to specify the tracker for inference. Since in this example we only have one animal, we set it to "none".
- The `-o` option is used to specify the path to the output file containing the predictions.
- The above script will predict all the frames in the video. You may select specific frames via the `--frames` option. For example: `--frames 1-50` or `--frames 1,3,5,7,9`.
:::

You can submit and monitor the inference job in the same way as the training job.
```{code-block} console
$ sbatch slurm-infer-script.sh
$ squeue -u <SWC-USERNAME>
```
Upon completion, a `labels.v001.slp.predictions.slp` file will have been created in the job directory.

You can use the SLEAP GUI on your local machine to load and view the predictions:
"File" -> "Open Project..." -> select the `labels.v001.slp.predictions.slp` file.

## The training-inference cycle
Now that you have some predictions, you can keep improving your models by repeating
the training-inference cycle. The basic steps are:
- Manually correct some of the predictions: see [Prediction-assisted labeling](https://sleap.ai/tutorials/assisted-labeling.html)
- Merge corrected labels into the initial training set: see [Merging guide](https://sleap.ai/guides/merging.html)
- Save the merged training set as `labels.v002.slp`
- Export a new training job `labels.v002.slp.training_job` (you may reuse the training configurations from `v001`)
- Repeat the training-inference cycle until satisfied

## Troubleshooting

### Problems with the SLEAP module

In this section, we will describe how to test that the SLEAP module is loaded
correctly for you and that it can use the available GPUs.

Login to the HPC cluster as described [above](#access-to-the-hpc-cluster-and-sleap-module).

Start an interactive job on a GPU node. This step is necessary, because we need
to test the module's access to the GPU.
```{code-block} console
$ srun -p fast --gres=gpu:1 --pty bash -i
```
:::{dropdown} Explain the above command
:color: info
:icon: info

* `-p fast` requests a node from the "fast" partition. This refers to the queue of nodes with a 3-hour time limit. They are meant for short jobs, such as testing.
* `--gres=gpu:1` requests 1 GPU of any kind
*  `--pty` is short for "pseudo-terminal".
*  The `-i` stands for "interactive"

Taken together, the above command will start an interactive bash terminal session
on a node of the "fast" partition, equipped with 1 GPU.
:::

First, let's verify that you are indeed on a node equipped with a functional
GPU, by typing `nvidia-smi`:
```{code-block} console
$ nvidia-smi
Wed Sep 27 10:34:35 2023
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 525.125.06   Driver Version: 525.125.06   CUDA Version: 12.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:41:00.0 Off |                  N/A |
|  0%   42C    P8    22W / 240W |      1MiB /  8192MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```
Your output should look similar to the above. You will be able to see the GPU
name, temperature, memory usage, etc. If you see an error message instead,
(even though you are on a GPU node) please contact the SWC Scientific Computing team.

Next, load the SLEAP module.
```{code-block} console
$ module load SLEAP
Loading SLEAP/2023-08-01
  Loading requirement: cuda/11.8
```

To verify that the module was loaded successfully:
```{code-block} console
$ module list
Currently Loaded Modulefiles:
 1) SLEAP/2023-08-01
```
You can essentially think of the module as a centrally installed conda environment.
When it is loaded, you should be using a particular Python executable.
You can verify this by running:

```{code-block} console
$ which python
/ceph/apps/ubuntu-20/packages/SLEAP/2023-08-01/bin/python
```

Finally we will verify that the `sleap` python package can be imported and can
"see" the GPU. We will mostly just follow the
[relevant SLEAP instructions](https://sleap.ai/installation.html#testing-that-things-are-working).
First, start a Python interpreter:
```{code-block} console
$ python
```
Next, run the following Python commands:

::: {warning}
The {python}`import sleap` command may take some time to run (more than a minute).
This is normal. Subsequent imports should be faster.
:::

```{code-block} pycon
>>> import sleap

>>> sleap.versions()
SLEAP: 1.3.1
TensorFlow: 2.8.4
Numpy: 1.21.6
Python: 3.7.12
OS: Linux-5.4.0-109-generic-x86_64-with-debian-bullseye-sid

>>> sleap.system_summary()
GPUs: 1/1 available
  Device: /physical_device:GPU:0
         Available: True
        Initialized: False
     Memory growth: None

>>> import tensorflow as tf

>>> print(tf.config.list_physical_devices('GPU'))
[PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU')]

>>> tf.constant("Hello world!")
<tf.Tensor: shape=(), dtype=string, numpy=b'Hello world!'>
```

If all is as expected, you can exit the Python interpreter, and then exit the GPU node
```{code-block} pycon
>>> exit()
```
```{code-block} console
$ exit()
```
If you encounter troubles with using the SLEAP module, contact the
Niko Sirmpilatze of the SWC [Neuroinformatics Unit](https://neuroinformatics.dev/).

To completely exit the HPC cluster, you will need to logout of the SSH session  twice:
```bash
$ logout
$ logout
```
See [Why do we SSH twice?](#why-do-we-ssh-twice) in the Appendix for an explanation.

## Appendix

### SLURM arguments primer

Here are the most important SLURM arguments used in the above examples
in conjunction with `sbatch` or `srun`.

**Partition (Queue)**
- Name: `--partition`
- Alias: `-p`
- Description: Specifies the partition (or queue) to submit the job to. In this case, the job will be submitted to the "gpu" partition. To see a list of all partitions/queues, the nodes they contain and their respective time limits, type `sinfo` when logged in to the HPC cluster.
- Example values: `gpu`, `cpu`, `fast`, `medium`

**Job Name**
- Name: `--job-name`
- Alias: `-J`
- Description: Specifies a name for the job, which will appear in various SLURM commands and logs, making it easier to identify the job (especially when you have multiple jobs queued up)
- Example values: `training_run_24`

**Number of Nodes**
- Name: `--nodes`
- Alias: `-N`
- Description: Defines the number of nodes required for the job.
- Example values: `1`
- Note: This should always be `1`, unless you really know what you're doing

**Number of Cores**
- Name: `--ntasks`
- Alias: `-n`
- Description: Defines the number of cores (or tasks) required for the job.
- Example values: `1`, `4`, `8`

**Memory Pool for All Cores**
- Name: `--mem`
- Description: Specifies the total amount of memory (RAM) required for the job across all cores (per node)
- Example values: `8G`, `16G`, `32G`

**Time Limit**
- Name: `--time`
- Alias: `-t`
- Description: Sets the maximum time the job is allowed to run. The format is D-HH:MM, where D is days, HH is hours, and MM is minutes.
- Example values: `0-01:00` (1 hour), `0-04:00` (4 hours), `1-00:00` (1 day).
- Note: If the job exceeds the time limit, it will be terminated by SLURM. On the other hand, avoid requesting way more time than what your job needs, as this may delay its scheduling (depending on resource availability).

**Generic Resources (GPUs)**
* Name: `--gres`
* Description: Requests generic resources, such as GPUs.
* Example values: `gpu:1`, `gpu:rtx2080:1`, `gpu:rtx5000:1`, `gpu:a100_2g.10gb:1`
* Note: No GPU will be allocated to you unless you specify it via the `--gres` argument (ecen if you are on the "GPU" partition. To request 1 GPU of any kind, use `--gres gpu:1`. To request a specific GPU type, you have to include its name, e.g. `--gres gpu:rtx2080:1`. You can view the available GPU types on the [SWC internal wiki](https://wiki.ucl.ac.uk/display/SSC/CPU+and+GPU+Platform+architecture).

**Standard Output File**
- Name: `--output`
- Alias: `-o`
- Description: Defines the file where the standard output (STDOUT) will be written. In the examples scripts, it's set to slurm.%N.%j.out, where %N is the node name and %j is the job ID.
- Example values: `slurm.%N.%j.out`, `slurm.MyAwesomeJob.out`
- Note: this file contains the output of the commands executed by the job (i.e. the messages that normally gets printed on the terminal).

**Standard Error File**
- Name: `--error`
- Alias: `-e`
- Description: Specifies the file where the standard error (STDERR) will be written. In the examples, it's set to slurm.%N.%j.err, where %N is the node name and %j is the job ID.
- Example values: `slurm.%N.%j.err`, `slurm.MyAwesomeJob.err`
- Note: this file is very useful for debugging, as it contains all the error messages produced by the commands executed by the job.

**Email Notifications**
* Name: `--mail-type`
* Description: Defines the conditions under which the user will be notified by email.
Example values: `ALL`, `BEGIN`, `END`, `FAIL`

**Email Address**
* Name: `--mail-user`
* Description: Specifies the email address to which notifications will be sent.
* Note: currently this feature does not work on the SWC HPC cluster.

**Array jobs**
* Name: `--array`
* Description: Job array index values (a list of integers in increasing order). The task index can be accessed via the `SLURM_ARRAY_TASK_ID` environment variable.
* Example values: `--array=1-10`, `--array=1-100%5` (100 jobs, but only 5 of them will be allowed to run in parallel at any given time).
* Note: if an array consists of many jobs, using the `%` syntax to limit the maximum number of parallel jobs is recommended to prevent overloading the cluster.


### Why do we SSH twice?

We first need to distinguish the different types of nodes on the SWC HPC system:

- the *bastion* node (or "jump host") - `ssh.swc.ucl.ac.uk`. This serves as a single entry point to the cluster from external networks. By funneling all external SSH connections through this node, it's easier to monitor, log, and control access, reducing the attack surface. The *bastion* node has very little processing power. It can be used to submit and monitor SLURM jobs, but it shouldn't be used for anything else.
- the *gateway* node - `hpc-gw1`. This is a more powerful machine and can be used for light processing, such as editing your scripts, creating and copying files etc. However don't use it for anything computationally intensive, since this node's resources are shared across all users.
- the *compute* nodes - `enc1-node10`, `gpu-sr670-21`, etc. These are the machinces that actually run the jobs we submit, either interactively via `srun` or via batch scripts submitted with `sbatch`.

![](../_static/swc_hpc_access_flowchart.png)

The home directory, as well as the locations where filesystems like `ceph` are mounted, are shared across all of the nodes.

The first `ssh` command - `ssh <SWC-USERNAME>@ssh.swc.ucl.ac.uk` only takes you to the *bastion* node. A second command - `ssh hpc-gw1` - is needed to reach the *gateway* node.

Similarly, if you are on the *gateway* node, typing `logout` once will only get you one layer outo the *bastion* node. You need to type `logout` again to exit the *bastion* node and return to your local machine.

The *compute* nodes should only be accessed via the SLURM `srun` or `sbatch` commands. This can be done from either the *bastion* or the *gateway* nodes. If you are running an interactive job on one of the *compute* nodes, you can terminate it by typing `exit`. This will return you to the node from which you entered.
