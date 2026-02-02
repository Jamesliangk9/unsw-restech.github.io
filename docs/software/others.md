title: Others

<h2> Ansys </h2>

Both Ansys Workbench and Ansys Electronic Desktop are available on Katana. The most user-friendly way to run Ansys is via Katana OnDemand:
../using_katana/ondemand.md

<h3> Ansys Batch Jobs </h3>

NOTE:
The version of Ansys available through myAccess MUST NOT be used for any research purposes, including generating files intended to be run on Katana.

Once you are familiar with how jobs run on Katana, you can submit Ansys jobs as batch jobs. Batch jobs run without requiring any interactive input from you:
../../help_support/glossary#batch-jobs

To use Ansys CFX on Katana, you must upload your .cfx and .def files to Katana.

A brief batch job example is shown below. For more detailed examples, see our GitHub repository:
https://github.com/unsw-edu-au/Restech-HPC/blob/master/hpc-examples/ansys/ansys-cfx.md

NOTE:
You must join the UNSW GitHub organisation to access this repository:
https://research.unsw.edu.au/github

Example batch workflow:
```bash
#!/bin/bash

<RESOURCE REQUESTS>

cd $PBS_O_WORKDIR

module load intel-mpi/2021.7.1
module load ansys/2021r1

cfx5solve -batch -def <filename>.def -part $NCPUS -start-method "Intel MPI Local Parallel"
```
<h3> Ansys Fluent </h3>

Ansys Fluent input files can be generated locally and transferred to Katana to run as a batch job. A brief example is shown below, with more complete examples available in our GitHub repository:
https://github.com/unsw-edu-au/Restech-HPC/blob/master/hpc-examples/ansys/ansys-fluent.md

NOTE:
You must join the UNSW GitHub organisation to access this repository.

Example batch workflow:
```bash
#!/bin/bash

<RESOURCE REQUESTS>

cd $PBS_O_WORKDIR

module load ansys/2021r1
module load intel-mpi/2021.7.1

fluent 3d -g -t $NCPUS -i fluent.in > output.out
```
<h3> Ansys on Gadi </h3>

If you have an account on Gadi (the NCI supercomputer), you can also run Ansys there:
https://nci.org.au/our-services/supercomputing

UNSW has an institutional licence for Ansys on Gadi. You must join the relevant software group as described in NCI’s documentation:
https://opus.nci.org.au/display/Help/Ansys+Fluent

<h2> Biosciences </h2>

A range of bioscience software packages is installed on Katana. If you are unable to find a specific package, it may be included as part of a larger software suite listed on this page.

<h3>Datasets</h3>

To avoid repeated downloads by multiple users, commonly used datasets have already been downloaded. This includes multiple versions of the NCBI nr and nt databases:
https://www.ncbi.nlm.nih.gov/

These datasets are stored under:
/data

<h3>Stand-alone Software</h3>

Blast+
module load blast-plus/2.12.0

Mothur
module load mothur/1.48.0

<h3>Python Modules</h3>

All Python versions available via the environment modules include BioPython and Snakemake.

To view available Python modules:
module avail python

<h3>R Modules</h3>

To view available R modules:
```bash
module avail r
```
Bioconductor is installed within R. To minimise dependency issues, we recommend loading the latest available R version and installing Bioconductor packages into your personal R library.

Installation instructions are available on the Bioconductor website:
https://www.bioconductor.org/

Example R commands (plain text):
```bash
if (!require("BiocManager", quietly = TRUE))
install.packages("BiocManager")

BiocManager::install("Biobase")
BiocManager::install(c("GenomicFeatures", "AnnotationDbi"))
BiocManager::install("DESeq2")
```
<h3>Perl Modules</h3>

All Perl versions available through environment modules include BioPerl. If you do not use the module system to access Perl, BioPerl will not be available.

To view available Perl modules:
module avail perl

<h3>Conda</h3>

If you wish to install bioscience software using Conda, refer to the Python documentation page:
../software/python/

NOTE:
Some software (such as Bioconductor) does not work well when installed via Conda due to dependency complexity and version lag.

<h2> Comsol </h2>

The most user-friendly way to run Comsol interactively is via Katana OnDemand:
../using_katana/ondemand.md

NOTE:
You must belong to a group that owns a COMSOL licence (mech, spree, quantum, or biomodel).

Comsol Batch Jobs

NOTE:
The version of COMSOL available via myAccess MUST NOT be used for research, including generating files to be run on Katana.

An example batch job file is available in our GitHub repository:
https://github.com/unsw-edu-au/Restech-HPC/blob/master/hpc-examples/comsol/comsol.pbs

NOTE:
You must join the UNSW GitHub organisation to access this repository.

Example batch workflow:
```bash
#!/bin/bash

<RESOURCE REQUESTS>

mkdir -p ${TMPDIR}/comsol

export MY_COMSOL_DIR=/srv/scratch/$USER/comsoldir

module load comsol/5.6-spree

comsol -nn 1 -np $NCPUS
-recoverydir ${MY_COMSOL_DIR}/recoveries
-tmpdir ${TMPDIR}/comsol
batch
-inputfile ${MY_COMSOL_DIR}/MyModel.mph
-outputfile ${MY_COMSOL_DIR}/MyModelOut.mph
-batchlog ${MY_COMSOL_DIR}/MyModel.log
```

<h2> Intel Compilers and Software Libraries </h2>

Research Technology Services provides access to the Intel Compiler Collection via environment modules. This collection includes compilers, libraries, debugging tools, and MPI software optimised for Intel CPUs.

Compilers (module: intel-compilers)

Intel C Compiler (icc)

Intel C++ Compiler (icpc)

Intel Fortran Compiler (ifort)

Libraries

Intel Math Kernel Library (MKL) – intel-mkl

Intel Threading Building Blocks (TBB) – intel-tbb

Intel Integrated Performance Primitives (IPP)

Debugger

Intel Debugger (idbc)

<h2> Java </h2>

Java is installed as part of the operating system, but that version may change without notice, potentially affecting reproducibility. For this reason, we recommend using Java versions provided through environment modules.

To view available Java modules:
```bash
module avail java
```
Each Java module sets the environment variable:
```bash
_JAVA_TOOL_OPTIONS -Xmx1g
```
This sets the Java heap size to 1 GB. If you require more memory, set _JAVA_OPTIONS to override it:
```bash
export _JAVA_OPTIONS="-Xmx5g"
```
<h2> Matlab </h2>

Running Interactively

Matlab can be run interactively using Katana OnDemand for graphical sessions, or via qsub for text-based interactive sessions:
../using_katana/ondemand
../../help_support/glossary#interactive-jobs

Batch Jobs

Matlab can also be run within a batch job. The example below runs Matlab without a graphical interface. Jobs start in your home directory by default.

Example:
```bash
module load matlab/R2022a
matlab -nodisplay -nosplash -r scriptfile
```
If your Matlab script is not in your home directory, either specify the full path or change directories within the batch script.

Using a full path:
```bash
module load matlab/R2022a
matlab -nodisplay -nosplash -r /path/to/script/scriptfile
```
Changing directory:
```bash
cd /path/to/script/
module load matlab/R2022a
matlab -nodisplay -nosplash -r scriptfile
```
If the script is located in the directory where the job was submitted, you can use $PBS_O_WORKDIR:
```bash
cd $PBS_O_WORKDIR
module load matlab/R2022a
matlab -nodisplay -nosplash -r scriptfile
```
Later versions of Matlab support the -batch flag:
```bash
module load matlab/R2022a
matlab -batch -r scriptfile
```
<h2> Operating Systems </h2>

Katana compute nodes run Rocky Linux. To check the exact version, run:
```bash
cat /etc/redhat-release
```
Research software is managed using environment modules:
```bash
../../software/environment_modules/
```
This allows multiple versions of software to coexist and lets users select the version they need.

<h2> Perl </h2>

The default Perl version on Katana is 5.26.3, provided by Rocky Linux and located at:
/usr/bin/perl

Perl is also available via environment modules.

A typical Perl script begins with:
```bash
#!/usr/bin/perl
```
This restricts execution to the system Perl version. To allow Perl modules loaded via the environment module system, use:
```bash
#!/usr/bin/env perl
```
This ensures the Perl interpreter is resolved from your PATH.

<h2> Stata </h2>

Stata is available as an environment module.

When running Stata in a PBS batch script, use:
```bash
stata -b do StataClusterWorkshop.do
```
To install additional Stata commands, use the findit command on your local machine. Copy the resulting .ado (and possibly .hlp) files into a directory called myadofiles in your home directory.

Then tell Stata to search that directory:
```bash
sysdir set PERSONAL $HOME/myadofiles
```
<h2> tmux </h2>

Terminal sessions on Katana are live. If you disconnect or close your terminal, your session ends. This can interrupt long-running tasks or interactive jobs.

To prevent this, use tmux to create an interruptible session.

NOTE:
You must reconnect to the same Katana login node (katana1, katana2, or katana3) to reattach to your tmux session.

To start tmux:
```bash
tmux
```
This opens a new session with a status bar at the bottom. Programs started inside tmux continue running even if you disconnect.

To reconnect later:
```bash
tmux a
```
tmux supports multiple sessions, split panes, and more. Documentation is available at:
https://github.com/tmux/tmux/wiki

<h2> Zip </h2>

Compressing Large Directories

For compressing large directories or directories with many files, we recommend using tgzme:
https://github.com/unsw-edu-au/tgzme/blob/master/tgzme.sh

tgzme is based on the following command:
```bash
tar -c $DIRECTORY | pigz > $DIRECTORY.tar.gz
```
This tool was developed by Dr. Edwards:
https://orcid.org/0000-0002-3645-5539