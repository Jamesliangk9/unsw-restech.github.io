Title: Addtional Tips for Using PBS and Katana Effectively

## Keep Your Jobs Under 12 Hours Whenever Possible

On Katana, jobs that request more than 12 hours of `walltime` can only run on nodes owned by your school or research group. By keeping your job runtime request under 12 hours, your job becomes eligible to run on any available node in the cluster. This greatly improves your chances of running sooner.

!!! important
    Two 10-hour jobs will usually complete faster than one 20-hour job.

In fact, when spare capacity is available (which is often the case), several shorter jobs will typically complete well before one long job even starts.

Requesting more resources than necessary can also reduce the number of places your job can run. For example:
- Going beyond 12 hours limits which nodes are available.
- Requesting a specific CPU type restricts you to only nodes with that CPU.
- A job asking for 20GB of memory can run alongside a 100GB job on a 128GB node, but a job asking for 30GB may not fit.

## Why Avoid Running All Jobs Interactively

Interactive jobs are useful for testing, debugging, or short exploratory tasks, but they are not efficient for managing large workloads. If you only run interactively:
- You cannot queue up multiple jobs to run automatically.
- You may end up waiting for resources instead of letting jobs start in the background.

Batch jobs let you submit many jobs at once, which then run and finish without further input. If your jobs are very similar, you should consider **array jobs**, which make managing repetitive workloads much easier.

## Other Advanced Usage

### Array Jobs

Many computational tasks require repeating the same process with small variations, such as different input files or random seeds. Array jobs allow you to handle these efficiently by spawning multiple sub-jobs from a single script. Each sub-job is distinguished by the special environment variable `$PBS_ARRAY_INDEX`.

For example, the following script will run 100 sub-jobs, each using 1 CPU core, 1GB of memory, and 1 hour of walltime. The sub-jobs will read different input files (1.dat, 2.dat, ..., 100.dat):

```bash
#!/bin/bash

#PBS -l select=1:ncpus=1:mem=1gb
#PBS -l walltime=1:00:00
#PBS -j oe
#PBS -J 1-100

cd ${PBS_O_WORKDIR}

./myprogram ${PBS_ARRAY_INDEX}.dat
```

!!! Note
    - We use brace expansion (the {} around variables) to ensure correct substitution. While not required for every variable (e.g., ${PBS_O_WORKDIR}), it is good practice for consistency.

    - More examples, including grouping and advanced usage of arrays, are available in the UNSW HPC GitHub examples. (Access requires joining the UNSW GitHub organisation.)

## Splitting Large Batch Jobs

- Instead of running one massive job, it is often better to split it into smaller ones. This has several advantages:

- Maximum runtime limit: Katana jobs cannot exceed 200 hours. Splitting ensures completion.

- Different resource needs: If parts of your pipeline need different resources, splitting prevents waste. For example, if only one step requires 200GB of memory, you shouldn't reserve that much for the whole run.

- Fair-share scheduling: Katana prioritises users based on their resource usage. Requesting excessive memory may lower your future job priority.

- Shorter queue times: Smaller jobs (e.g., seven 12-hour jobs) often run faster overall than one very long job.

- Breaking down your workload into smaller jobs makes better use of Katana's scheduling system and usually leads to faster completion times.

## Accessing the Grace Hopper (GH200) GPU Node on Katana

Katana includes a Grace Hopper (GH200) node, which combines an ARM-based CPU with a Hopper-generation GPU. This node offers much higher performance than Katana鈥檚 older GPU nodes (V100 and A100) and is designed for experimental, high-end workloads.

### Key Characteristics of the GH200 Node
- **CPU**: 72-core ARM CPU (architecture: aarch64) with 480 GB memory.
- **GPU**: One Hopper GPU with 96 GB HBM3 memory.
- **Memory bandwidth**: The CPU and GPU are linked with 900 GB/s NVLink, allowing them to share memory. This means you can run GPU jobs requiring more than 96 GB memory, because the GPU can access CPU memory directly.
- **Architecture**: The ARM CPU architecture is different from the rest of Katana (x86_64). Regular Katana binaries and modules will not work here.

### Software Considerations
- Use the default GNU compiler and CUDA libraries available on the node.
- Install your own Python environment using Conda for ARM (aarch64), since many precompiled packages won鈥檛 run on ARM.
- Jobs here are best suited for Python-based machine learning, deep learning, and experimental HPC applications.

### Submitting Jobs to the GH200 Node

To request the GH200 node, you must specify the cpu_arch=aarch64 resource in your job submission. For example:

Direct command line submission:
```bash
[z1234567@katana ~]$ qsub -l select=1:cpu_arch=aarch64:ngpus=1:ncpus=72:mem=480gb myjob.pbs
1238.kman.restech.unsw.edu.au
```

Or inside your job script(e.g., myjob.pbs):
```bash
#PBS -l select=1:cpu_arch=aarch64:ngpus=1:ncpus=72:mem=480gb
```

### When to Use the GH200 Node

- If your job requires very high GPU memory bandwidth or unified CPU鈥揋PU memory.
- If you are experimenting with next-generation AI/ML workloads.
- If your application can be built or run on ARM (aarch64) architecture.

## SSH KeepAlive

To stop your connection disconnecting after some idle time, you can send some empty packets to keep your session alive. You want to change the
frequency of these packets from 0 (none) to a small time interval, say 60 seconds. The configuration differs depending on the SSH client used.

On PuTTy: Category -> Connection -> "Seconds between keepalives"

On MobaXterm: Settings -> Configuration -> SSH -> SSH keepalive 

On Linux and WSL you send keepalive packets for all servers by editing ~/.ssh/config and adding the lines 

``` bash

   Host *
      ServerAliveInterval 60

```
