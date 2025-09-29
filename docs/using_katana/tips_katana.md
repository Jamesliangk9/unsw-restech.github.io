title: Tips for using PBS and Katana effectively

## Keep your jobs under 12 hours if possible

If you request more than 12 hours of `WALLTIME` then you can only use the nodes bought by your school or research group. Keeping your job's run time request under 12 hours means that it can run on any node in the cluster.

!!! important
    Two 10 hour jobs will probably finish sooner that one 20 hour job.

In fact, if there is spare capacity on Katana, which there is most of the time, six 10 hours jobs will finish before a single 20 hour job will.
Requesting more resources for your job decreases the places that the job can run

The most obvious example is going over the 12 hour limit which limits the number of compute nodes that your job can run on. For example specifying the CPU in your job script restricts you to the nodes with that CPU. A job that requests 20Gb will run on a 128Gb node with a 100Gb job already running but a 30Gb job will not be able to.

## Running your jobs interactively makes it hard to manage multiple concurrent jobs

If you are currently only running jobs interactively then you should move to batch jobs which allow you to submit more jobs which then start, run and finish automatically.
If you have multiple batch jobs that are almost identical then you should consider using array jobs

If your batch jobs are the same except for a change in file name or another variable then you should have a look at using array jobs.

# Other Advanced Usages

<<<<<<< Updated upstream
As well as the information presented here, examples are available in the [Restech HPC Github repository](../using_katana/running_jobs/#restech-github-repositories)
=======
<h3> Array Jobs </h3>
>>>>>>> Stashed changes

## Array Jobs

One common use of computational clusters is to do the same thing multiple times - sometimes with slightly different input, sometimes to get averages from randomness within the process.
This is made easier with array jobs.

An array job is a single job script that spawns many almost identical sub-jobs. The only difference between the sub-jobs is an environment variable `$PBS_ARRAY_INDEX` whose value uniquely
identifies an individual sub-job. A regular job becomes an array job when it uses the `#PBS -J` flag. 

For example, the following script will spawn 100 sub-jobs. Each sub-job will require one CPU core, 1GB memory and 1 hour run-time, and it will execute the same application. However, a different
input file will be passed to the application within each sub-job. The first sub-job will read input data from a file called `1.dat`, the second sub-job will read input data from a file called
`2.dat` and so on. 

!!! note
    In this example we are using `brace expansion` - the {} characters around the bash variables - because they are needed for variables that change, like array indices. They aren't strictly
	necessary for `$PBS_O_WORKDIR` but we include them to show consistency.

``` bash
#!/bin/bash
    
#PBS -l select=1:ncpus=1:mem=1gb
#PBS -l walltime=1:00:00
#PBS -j oe
#PBS -J 1-100
    
cd ${PBS_O_WORKDIR}
    
./myprogram ${PBS_ARRAY_INDEX}.dat
```

There are some more examples of array jobs including how to group your computations in an array job on the [UNSW Github HPC examples](https://github.com/unsw-edu-au/Restech-HPC/tree/master/hpc-examples) page. (Note: You need to [join the UNSW GitHub organisation](https://research.unsw.edu.au/github) to access this repo)

## Splitting large Batch Jobs

If your batch job can be split into multiple steps you may want to split one big job up into a number of smaller jobs. There are a number of reasons to spend the time to implement this.

<<<<<<< Updated upstream
1. If your large job runs for over 200 hours, it won't finish on Katana.
2. If your job has multiple steps which use different amounts of resources at each step. If you have a pipeline that takes 50 hours to run and needs 200GB of memory for an hour, but only 50GB the rest of the time, then the memory is sitting idle. 
3. Katana has prioritisations based on how many resources any one user uses. If you ask for 200GB of memory, this will be accounted for when working out your next job's priority.
4. Because there are many more resources for 12 hour jobs, seven or eight 12 hour jobs will often finish well before a single 100 hour job even starts. 
=======
- Instead of running one massive job, it is often better to split it into smaller ones. This has several advantages:

- Maximum runtime limit: Katana jobs cannot exceed 200 hours. Splitting ensures completion.

- Different resource needs: If parts of your pipeline need different resources, splitting prevents waste. For example, if only one step requires 200GB of memory, you shouldn't reserve that much for the whole run.

- Fair-share scheduling: Katana prioritises users based on their resource usage. Requesting excessive memory may lower your future job priority.

- Shorter queue times: Smaller jobs (e.g., seven 12-hour jobs) often run faster overall than one very long job.

- Breaking down your workload into smaller jobs makes better use of Katana's scheduling system and usually leads to faster completion times.

## Accessing the Grace Hopper (GH200) GPU Node on Katana

Katana includes a Grace Hopper (GH200) node, which combines an ARM-based CPU with a Hopper-generation GPU. This node offers much higher performance than Katana’s older GPU nodes (V100 and A100) and is designed for experimental, high-end workloads.

<h3> Key Characteristics of the GH200 Node </h3>
- **CPU**: 72-core ARM CPU (architecture: aarch64) with 480 GB memory.
- **GPU**: One Hopper GPU with 96 GB HBM3 memory.
- **Memory bandwidth**: The CPU and GPU are linked with 900 GB/s NVLink, allowing them to share memory. This means you can run GPU jobs requiring more than 96 GB memory, because the GPU can access CPU memory directly.
- **Architecture**: The ARM CPU architecture is different from the rest of Katana (x86_64). Regular Katana binaries and modules will not work here.

<h3> Software Considerations </h3>
- Use the default GNU compiler and CUDA libraries available on the node.
- Install your own Python environment using Conda for ARM (aarch64), since many precompiled packages won’t run on ARM.
- Jobs here are best suited for Python-based machine learning, deep learning, and experimental HPC applications.

<h3> Submitting Jobs to the GH200 Node </h3>

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

<h3> When to Use the GH200 Node </h3>

- If your job requires very high GPU memory bandwidth or unified CPU–GPU memory.
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
>>>>>>> Stashed changes
