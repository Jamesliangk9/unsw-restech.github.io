title: Navigation and Quick Start Guide

# Finding Information and Getting Started on Katana

Katana documentation is organised into sections covering access, jobs, storage, software, data transfer, and support. If you are unsure where to begin, use the links below to identify the section most relevant to your work.

This page also provides a short starting path for new users, followed by practical checks that can help confirm that a Katana session and job are working as expected.

---

## Quick Navigation

| I want to... | Start here |
|---|---|
| Request an account or connect to Katana | [Accessing Katana](/using_katana/accessing_katana/) |
| Use Katana through a web browser | [Web Access to Katana](/using_katana/ondemand/) |
| Submit an interactive or batch job | [Running Jobs on Katana](/using_katana/running_jobs/) |
| Check, modify, or delete a job | [Monitor Your Jobs](/using_katana/monitor_jobs/) |
| Decide where to store files | [Storage Locations](/storage/storage_locations/) |
| Transfer, move, compress, or extract large files | [Katana Data Mover](/storage/kdm/) |
| Transfer files between OneDrive and Katana | [OneDrive](/storage/onedrive/) |
| Store research data for the long term | [Data Archive](/storage/data_archive/) |
| Find and load installed software | [Environment Modules](/software/environment_modules/) |
| Install my own software | [Installing Software](/software/installing_software/) |
| Use Python environments and packages | [Python](/software/python/) |
| Use JupyterLab or Jupyter Notebooks | [Jupyter Notebooks](/software/jupyter-notebooks/) |
| Find answers to common questions | [FAQ](/help_support/faq/) |
| Understand an unfamiliar HPC term | [Glossary](/help_support/glossary/) |
| Contact the Research Technology Services team | [Help and Support](/help_support/user_support/) |

!!! tip
    Use the documentation search bar when you know the name of a command, error, application, or topic but do not know which section contains it.

---

## Recommended Path for New Users

New Katana users will usually need the following pages in this order:

1. [Accessing Katana](/using_katana/accessing_katana/)
2. [Storage Locations](/storage/storage_locations/)
3. [Running Jobs on Katana](/using_katana/running_jobs/)
4. [Monitor Your Jobs](/using_katana/monitor_jobs/)
5. [Environment Modules](/software/environment_modules/)

---

# Quick Start Guide

This section introduces a basic Katana command-line workflow. It is intended for users who have connected to Katana but are not yet familiar with the commands used to check their session, request compute resources, and confirm that a job is running correctly.

!!! note
    User IDs, job IDs, node names, filenames, module versions, and paths shown below are examples. Replace them with values from your own session.

## 1. Connect to Katana

From a terminal on your computer, connect using your UNSW zID:

```bash
ssh z1234567@katana.restech.unsw.edu.au
```

Replace `z1234567` with your own zID.

After logging in, confirm the machine name:

```bash
hostname
```

On a login node, the hostname will normally begin with `katana`.

!!! warning
    Login nodes are intended for preparing files, submitting jobs, transferring small files, and monitoring jobs. Computationally intensive work should be run through PBS on a compute node.

---

## 2. Check Your Current Location

Before running or submitting a job, confirm your current directory and account:

```bash
pwd
whoami
```

`pwd` shows your current directory, for example:

```text
/home/z1234567
```

`whoami` shows the account currently being used, for example:

```text
z1234567
```

These checks are useful when a command cannot find a file or when you are unsure which directory you are working in.

---

## 3. Start an Interactive Job

An interactive job gives you a shell on a compute node. This is useful for testing commands, checking software, and running short interactive workloads.

For example:

```bash
qsub -I -l select=1:ncpus=2:mem=8gb -l walltime=02:00:00
```

This requests:

- 1 compute node
- 2 CPU cores
- 8 GB of memory
- a maximum runtime of 2 hours

The command may wait while resources are allocated.

When the job starts, confirm the compute node with:

```bash
hostname
```

You should now see a compute-node hostname such as:

```text
k001
```

At this point, commands in this terminal are running within the resources allocated to your interactive job.

---

## 4. Check Your Jobs

To see your current PBS jobs:

```bash
qstat -su "$USER"
```

Common job states include:

| State | Meaning |
|---|---|
| `Q` | Queued and waiting to start |
| `R` | Running |
| `H` | Held |
| `E` | Exiting |
| `F` | Finished; check the exit status and output files to determine the result |

To view detailed information about a job:

```bash
JOB_ID=6900507
qstat -f "$JOB_ID"
```

Replace `6900507` with your own job ID.

For more information, see [Monitor Your Jobs](/using_katana/monitor_jobs/).

---

## 5. Find and Load Software

List the modules currently loaded:

```bash
module list
```

Browse available modules:

```bash
module avail
```

Search for Python:

```bash
module avail python
```

Load a version shown by `module avail`, for example:

```bash
module load python/3.10.8
```

Confirm which executable will run:

```bash
which python3
python3 --version
```

!!! note
    The version shown above is only an example. Use a version currently available on Katana.

Modules apply to the current shell. If you open another SSH session or submit a batch job, load the required modules again in that shell or job script.

---

## 6. Work from the Correct Directory

Check your location:

```bash
pwd
```

List files:

```bash
ls -lah
```

Your home directory can be referenced with:

```bash
$HOME
```

Your scratch directory can be referenced with:

```bash
/srv/scratch/$USER
```

For example:

```bash
cd /srv/scratch/$USER
```

Use scratch for working data and large temporary research files.

!!! warning
    Scratch storage is not backed up. Keep another copy of important data in an appropriate backed-up or archival location.

See [Storage Locations](/storage/storage_locations/) for guidance.

---

## 7. Transfer Large Files Using KDM

For large data transfers, use the Katana Data Mover rather than the Katana login nodes.

Connect to KDM:

```bash
ssh z1234567@kdm.restech.unsw.edu.au
```

Replace `z1234567` with your own zID.

For a large or restartable transfer, use `rsync`:

```bash
rsync -avhP /path/to/source/ /srv/scratch/$USER/my-project/
```

Replace the example source and destination paths with your own.

### Copying files from OneDrive to scratch

After OneDrive has been configured and mounted on KDM, copy files from the mounted OneDrive directory to scratch.

For example:

```bash
mkdir -p /srv/scratch/$USER/my-project

rsync -avhP     /home/$USER/OneDrive/path/to/files/     /srv/scratch/$USER/my-project/
```

For a single file:

```bash
rsync -avhP     /home/$USER/OneDrive/path/to/file     /srv/scratch/$USER/
```

The `-P` option displays transfer progress and keeps partially transferred files if a transfer is interrupted.

!!! note
    The exact OneDrive mount path depends on how OneDrive has been configured. Confirm the mounted directory before starting the transfer.

See [Katana Data Mover](/storage/kdm/) and [OneDrive](/storage/onedrive/) for the full instructions.

---

## 8. Request a GPU When Needed

A normal CPU job does not receive a GPU automatically.

View the current node overview:

```bash
pstat
```

List GPU models configured on Katana:

```bash
pbsnodes -av | grep gpu_model
```

To reduce repeated lines:

```bash
pbsnodes -av | grep gpu_model | sort -u
```

Request a GPU:

```bash
qsub -I -l select=1:ncpus=4:mem=32gb:ngpus=1 -l walltime=02:00:00
```

To request a specific GPU model, first check the available `gpu_model` values. For example:

```bash
qsub -I -l select=1:ncpus=4:mem=32gb:ngpus=1:gpu_model=H200 -l walltime=02:00:00
```

After the job starts:

```bash
hostname
nvidia-smi
```

!!! note
    Requesting a specific GPU model can increase queue time because the job can only run on nodes containing that model.

---

## 9. Finish an Interactive Job

When finished:

```bash
exit
```

This ends the interactive job and releases the allocated resources.

Confirm that the job is no longer running:

```bash
qstat -su "$USER"
```

---

# Troubleshooting

## My Job Is Waiting in the Queue

Check the job state:

```bash
qstat -su "$USER"
```

For more detail:

```bash
JOB_ID=6900507
qstat -f "$JOB_ID"
```

A queued job may be waiting because the requested combination of CPU, memory, GPU model, and walltime is not currently available.

---

## My Job Finished Immediately

Check the completed-job information:

```bash
JOB_ID=6900507
qstat -xf "$JOB_ID"
```

Then list recently modified files:

```bash
ls -lht
```

Look for the first meaningful error message, missing files, missing commands or libraries, an incorrect working directory, or a non-zero exit status.

Read an output file with:

```bash
less filename
```

Press `q` to leave `less`.

---

## My Batch Job Cannot Find My Files

Inside a PBS batch script, add:

```bash
cd "$PBS_O_WORKDIR"
```

You can also add:

```bash
echo "Job ID: $PBS_JOBID"
echo "Node: $(hostname)"
echo "Working directory: $(pwd)"
echo "Start time: $(date)"
```

These lines record useful diagnostic information in the job output.

---

## A Command Works in One Terminal but Not Another

Check loaded modules:

```bash
module list
```

If the required module is missing, load it again.

Environment changes made in one SSH session are not automatically copied to another session. Required modules should also be loaded inside PBS job scripts.

---

## My GPU Application Cannot Find a GPU

Confirm that the active job requested a GPU:

```bash
JOB_ID=6900507
qstat -f "$JOB_ID"
```

On the allocated compute node:

```bash
hostname
nvidia-smi
```

Check that `ngpus=1` was included in the resource request and that the required GPU-enabled software environment has been loaded.

---

## My SSH Session Disconnected

Log in again and check your jobs:

```bash
qstat -su "$USER"
```

A batch job continues independently of the SSH session. For long-running production workloads, use a batch job rather than relying on an interactive terminal connection.

---

## I Cannot Find My Output File

For a completed job:

```bash
JOB_ID=6900507
qstat -xf "$JOB_ID"
```

Look for `Output_Path` and `Error_Path`.

Also check:

```bash
pwd
ls -lht
```

---

## I Am Running Out of Storage Space

Show filesystem space:

```bash
df -h "$HOME" "/srv/scratch/$USER"
```

This reports filesystem space, not your personal allocation.

Check how much space your directories are using:

```bash
du -sh "$HOME"
du -sh "/srv/scratch/$USER"
```

---

# Information to Collect Before Requesting Support

Before contacting Research Technology Services, collect as much of the following information as possible:

```text
zID:
Date and approximate time:
Job ID:
Node name:
Working directory:
Command or script:
Software and module versions:
Expected result:
Actual result:
Complete error message:
Steps needed to reproduce the problem:
```

Useful commands include:

```bash
hostname
pwd
module list
qstat -su "$USER"
```

For an active job:

```bash
JOB_ID=6900507
qstat -f "$JOB_ID"
```

For a completed job:

```bash
JOB_ID=6900507
qstat -xf "$JOB_ID"
```

See [Help and Support](/help_support/user_support/) for current contact methods.

---

# Quick Diagnostic Checklist

Before requesting help, confirm the following:

- [ ] I am running computational work inside a PBS job, not on a login node.
- [ ] I checked the job state with `qstat`.
- [ ] I read the complete output and error information.
- [ ] I confirmed the working directory and input paths.
- [ ] I loaded the required software modules inside the job or shell where the command is running.
- [ ] I checked the requested CPU, memory, walltime, and GPU resources.
- [ ] I recorded the job ID and compute node.
- [ ] I can describe the steps needed to reproduce the problem.
