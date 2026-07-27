title: Navigation and Troubleshooting

# Finding Information and Troubleshooting on Katana

Katana documentation is organised by task. When you are unsure where to begin, first identify what you are trying to do, then use the links below to find the relevant instructions.

This page also provides a basic troubleshooting workflow for common Katana problems. It is intended as a starting point rather than a replacement for the detailed documentation linked throughout the page.

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
6. The page for the application or programming language being used

The basic workflow is:

```text
Connect to Katana
        ↓
Prepare files and software
        ↓
Request compute resources
        ↓
Run the job
        ↓
Monitor the job
        ↓
Review output and resource usage
```

---

# Troubleshooting Workflow

When something does not work, avoid changing several commands at once. Collect information step by step so that the source of the problem is easier to identify.

!!! note
    The job IDs, node names, filenames, module versions, and paths shown below are examples. Replace them with values from your own session before running the commands.

## 1. Check Where You Are

Run:

```bash
hostname
pwd
whoami
```

These commands show:

- the machine you are connected to
- your current directory
- the account being used

A Katana login node normally has a hostname beginning with `katana`. A compute node normally has a hostname such as `k001`.

!!! warning
    Login nodes are intended for preparing files, submitting jobs, and monitoring jobs. Run computationally intensive work on a compute node through an interactive or batch job.

---

## 2. Check Your Jobs

Show your current jobs:

```bash
qstat -su "$USER"
```

Common job states include:

| State | Meaning |
|---|---|
| `Q` | Queued and waiting to start |
| `R` | Running |
| `C` | Completed |
| `H` | Held |
| `E` | Exiting |
| `S` | Suspended |
| `F` | Finished; check the exit status and output files to determine the result |

View full details for a job:

```bash
JOB_ID=6900507
qstat -f "$JOB_ID"
```

View information retained for a completed job:

```bash
JOB_ID=6900507
qstat -H "$JOB_ID"
```

You can also use:

```bash
JOB_ID=6900507
qstat -xf "$JOB_ID"
```

Replace `6900507` with the numeric job ID returned by `qsub`.

---

## 3. Check the Output and Error Files

List recently modified files:

```bash
ls -lht
```

To find the output path recorded by PBS:

```bash
JOB_ID=6900507
qstat -xf "$JOB_ID"
```

Look for the `Output_Path` and `Error_Path` fields.

Read an output file with `less`:

```bash
less myjob.o6900507
```

Press `q` to leave `less`.

Output filenames depend on the job script and submission options, so use the paths reported by `qstat -xf` rather than assuming a particular filename.

Look for:

- the first meaningful error message
- missing files
- missing commands or libraries
- memory errors
- walltime termination
- an unexpected working directory
- a non-zero exit status

!!! tip
    Start with the first meaningful error message. Later messages are often consequences of the first failure.

---

## 4. Confirm the Working Directory

Inside a PBS batch script, use:

```bash
cd "$PBS_O_WORKDIR"
```

This changes the job to the directory from which it was submitted.

You can temporarily add the following diagnostic commands to a job script:

```bash
echo "Job ID: $PBS_JOBID"
echo "Node: $(hostname)"
echo "Working directory: $(pwd)"
echo "Start time: $(date)"
```

These lines record useful information in the job output.

---

## 5. Confirm the Software Environment

List the modules loaded in the current shell:

```bash
module list
```

Search for available software:

```bash
module avail
```

Search for available Python modules:

```bash
module avail python
```

Load a version that appears in the output. For example:

```bash
module load python/3.10.8
```

Confirm which executable will run:

```bash
which python3
python3 --version
```

!!! note
    Modules apply only to the current shell. Load the required modules again in each new SSH session and inside each PBS job script.

A job script can begin with:

```bash
module purge
module load python/3.10.8
```

Replace the example Python version with a version currently shown by `module avail python`.

---

## 6. Confirm the Requested Resources

View the resource request recorded for a job:

```bash
JOB_ID=6900507
qstat -f "$JOB_ID"
```

Check that the request includes enough:

- CPU cores
- memory
- walltime
- GPUs, when required

An interactive CPU job can be requested with:

```bash
qsub -I -l select=1:ncpus=2:mem=8gb -l walltime=02:00:00
```

A basic GPU job can be requested with:

```bash
qsub -I -l select=1:ncpus=4:mem=32gb:ngpus=1 -l walltime=02:00:00
```

If `ngpus` is not specified, the job does not receive a GPU.

---

## 7. Check GPU Availability and Select a GPU

View the current node overview:

```bash
pstat
```

List the GPU models configured on Katana:

```bash
pbsnodes -av | grep gpu_model
```

To reduce repeated lines:

```bash
pbsnodes -av | grep gpu_model | sort -u
```

Inspect a particular node:

```bash
NODE=k206
pbsnodes "$NODE"
```

Replace `k206` with a node name shown by `pstat` or `pbsnodes`.

Request a specific GPU model by using a value shown by `pbsnodes -av | grep gpu_model`.

For example, to request an A100:

```bash
qsub -I -l select=1:ncpus=4:mem=32gb:ngpus=1:gpu_model=A100 -l walltime=02:00:00
```

To request an H200, first confirm that `H200` appears in the available GPU model output, then run:

```bash
qsub -I -l select=1:ncpus=4:mem=32gb:ngpus=1:gpu_model=H200 -l walltime=02:00:00
```

After the interactive job starts, confirm the allocated node and GPU:

```bash
hostname
nvidia-smi
```

!!! note
    Requesting a specific GPU model can increase queue time because the job can only run on nodes containing that model. When a specific model is not required, omit `gpu_model` and allow PBS to allocate any available GPU.

---

## 8. Check Storage and Paths

Show files in the current directory:

```bash
ls -lah
```

Show free space on the filesystems containing your home and scratch directories:

```bash
df -h "$HOME" "/srv/scratch/$USER"
```

This reports filesystem space. It does not report your personal storage allocation or quota.

Check how much space your directories currently use:

```bash
du -sh "$HOME"
du -sh "/srv/scratch/$USER"
```

Common storage-related problems include:

- writing large datasets to the home directory
- using an incorrect absolute path
- trying to access a file that exists only on another computer
- permission problems in a shared directory
- expecting scratch storage to be backed up
- expecting files in `$TMPDIR` to remain after a job finishes

See [Storage Locations](/storage/storage_locations/) before moving or deleting important data.

For large transfers and file-management tasks, use the [Katana Data Mover](/storage/kdm/) rather than the login nodes.

---

## 9. Reproduce the Problem Interactively

When a batch job fails without a clear explanation, request a short interactive session with similar resources:

```bash
qsub -I -l select=1:ncpus=2:mem=8gb -l walltime=02:00:00
```

Then reproduce the setup one command at a time. For example:

```bash
cd "$HOME/my-project"
module purge
module load python/3.10.8
python3 my-script.py
```

Replace the project path, module version, and script name with your own values.

Interactive testing is useful for:

- checking paths
- testing module combinations
- installing software
- confirming package versions
- identifying missing input files
- testing a smaller version of the workload

Once the commands work interactively, place the same setup in the batch script.

---

# Common Problems

## My Job Is Stuck in the Queue

Check the job:

```bash
JOB_ID=6900507
qstat -f "$JOB_ID"
```

A queued job may be waiting because the requested combination of CPU, memory, GPU model, and walltime is not currently available.

Consider whether the job genuinely needs:

- the requested number of CPU cores
- the requested amount of memory
- a particular GPU model
- a long walltime

Requesting fewer resources or a shorter walltime may allow the scheduler to place the job sooner, but only reduce resources when the workload can genuinely run within the smaller request.

See [Monitor Your Jobs](/using_katana/monitor_jobs/) for more information.

---

## My Job Finished Immediately

Check the output and error files first:

```bash
ls -lht
```

Then check the completed-job information:

```bash
JOB_ID=6900507
qstat -xf "$JOB_ID"
```

Verify that:

- the command exists
- the required module was loaded in the job script
- the input files exist
- the working directory is correct
- the script uses Linux line endings
- the script has no typing or syntax errors

Add the following near the beginning of a shell script while debugging:

```bash
set -x
```

This prints commands as the shell executes them, making it easier to identify where the script stopped.

Remove `set -x` after debugging if the output becomes unnecessarily large.

---

## A Command Works in One Terminal but Not Another

The software may have been loaded only in the first shell.

Run:

```bash
module list
```

Then load the required module again in the new shell or job script.

Environment changes made in one SSH session are not automatically copied to another session.

---

## My Job Was Killed

Check the completed-job information and output:

```bash
JOB_ID=6900507
qstat -xf "$JOB_ID"
```

Common causes include:

- the job exceeded its requested memory
- the job reached its walltime
- the application exited with an error
- the job was deleted manually or administratively

Use the recorded resource usage to adjust the next request. Avoid requesting substantially more resources than the workload needs, because larger requests can wait longer in the queue.

---

## My GPU Application Cannot Find a GPU

Confirm that the job requested a GPU:

```bash
JOB_ID=6900507
qstat -f "$JOB_ID"
```

On the allocated compute node, run:

```bash
hostname
nvidia-smi
```

Also confirm that:

- `ngpus=1` was included in the resource request
- the application is running inside the allocated job
- the correct GPU-enabled software module or environment was loaded
- the installed software version supports the available GPU environment

Do not test GPU workloads on a login node.

---

## My SSH Session Disconnected

Log in again and check whether the job still exists:

```bash
qstat -su "$USER"
```

A submitted batch job runs independently of the SSH session. An interactive shell may no longer be usable after the connection is lost.

For long-running production workloads, use a batch job rather than relying on an interactive terminal connection.

---

## I Cannot Find My Output File

Check the PBS record:

```bash
JOB_ID=6900507
qstat -xf "$JOB_ID"
```

Look for `Output_Path` and `Error_Path`.

Also check the directory from which the job was submitted:

```bash
pwd
ls -lht
```

Including the following line in a batch script helps ensure that it runs from the submission directory:

```bash
cd "$PBS_O_WORKDIR"
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

Copy error messages as text where possible. Screenshots can provide additional context, but text is easier to search and inspect.

See [Help and Support](/help_support/user_support/) for current contact methods.

---

# Quick Diagnostic Checklist

Before requesting help, confirm the following:

- [ ] I am running computational work inside a PBS job, not on a login node.
- [ ] I checked the job state with `qstat`.
- [ ] I read the complete output and error files.
- [ ] I confirmed the working directory and input paths.
- [ ] I loaded the required software modules inside the job script.
- [ ] I checked the requested CPU, memory, walltime, and GPU resources.
- [ ] I recorded the job ID and compute node.
- [ ] I can describe the steps needed to reproduce the problem.