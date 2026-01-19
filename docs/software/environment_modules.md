title: Environment Modules

Environment Modules are a means of changing your environment to run specfic versions of installed sotware. This section provides information on how they are used on Katana.

<h2> How do I discover what software is available? </h2>

``` bash
z1234567@katana3:~ $ module avail
----------------------------------------- /apps/Modules/linux-rocky8-ivybridge -----------------------------------------
adios2/2.8.3                        icu4c/67.1                       openfoam-org/9_20240202
alsa-lib/1.2.3.2                    icu4c/73.2                       openfoam-org/10_20240227
amber/22                            idba/1.1.1                       openfoam-org/11
amber/22-gcc-8.5.0-cuda             idba/1.1.3                       openfoam/2206
ansys/2021R1                        igv/2.12.3                       openfoam/2206-hdf4
ansys/2021R2                        imagemagick/7.0.8-7              openfoam/2312
ansys/2022R2                        infernal/1.1.4                   openfoam/2406
ansys/2023R1                        inputproto/2.3.2                 openfoam/2506
ansys/2023R2                        intel-compilers/2022.2.1         openjpeg/1.5.1
ansys/2024R2                        intel-mkl/2022.2.1               openjpeg/1.5.2
ansys/2025R1                        intel-mpi/2021.7.1               openjpeg/2.3.1

etc..

Environment Modules allow you to modify your environment so you can run specific software versions on Katana.
```

<h2> Discovering Available Software </h2>

Run:
```bash
module avail
```

<h2> Loading a Software Version </h2>

```bash
module add adios2/2.8.3
# or
module load adios2/2.8.3
```

<h2> Viewing Loaded Modules </h2>

```bash
module list

Would return:

Currently Loaded Modulefiles:
 1) adios2/2.8.3
```

<h2> Removing Software </h2>

```bash
module rm adios2/2.8.3
# or
module unload adios2/2.8.3
```

<h2> Removing All Modules </h2>

```bash
module purge
```

<h2> Module Help </h2>

```bash
module help mrbayes
```

<h2> Switching Versions </h2>

```bash
module switch ansys/2021R1 ansys/2021R2
Would return:

Switching from ansys/2021R1 to ansys/2021R2
  Unloading useless requirement: krb5/1.19.3
  Loading requirement: krb5/1.19.3
```

<h2> Viewing Modifications a Module Makes </h2>

```bash
module show mothur/1.42.3
```

<h2> Why Modules Reset </h2>

Modules only apply to the current session.  
They must be loaded again in new SSH sessions or inside job scripts.

<h2> Using Modules in a Job Script </h2>

```bash
#!/bin/bash
#PBS -l select=1:ncpus=1:mem=4gb
#PBS -j oe

module purge
module add intel/18.0.1.163 #(add these two lines to the script as needed)

cd ${PBS_O_WORKDIR}
./myprog
```

<h2> What if the software that I want is not on the list? </h2>

If you require additional software installed on Katana then please email the details to [restech.support@unsw.edu.au](mailto:restech.support@unsw.edu.au) including URLs and
the version number that you wish installed.


<h2> How do I find out more about a particular piece of software? </h2>

You can find out more about a piece of software by using the module help command. For example:

``` bash
[z1234567@katana1 ~]$ module help ansys/2021R2
    
----------- Module Specific Help for 'mrbayes/3.2.2' --------------
    
MrBayes 3.2.2 is installed in /apps/mrbayes/3.2.2
    
This module was complied against beagle/2.1.2 and openmpi/1.6.4 with MPI support.
    
More information about the commands made available by this module is available
at http://mrbayes.sourceforge.net
```

<h2> How can I find out what paths and other environment variables a module uses? </h2>

``` bash
[z1234567@katana1 ~]$ module show mothur/1.42.3
-------------------------------------------------------------------
/apps/modules/bio/mothur/1.42.3:

module-whatis     Mothur 1.42.3 
conflict     mothur 
setenv         MOTHUR_ROOT /apps/mothur/1.42.3 
prepend-path     PATH /apps/mothur/1.42.3/bin 
setenv         LAST_MODULE_TYPE bio 
setenv         LAST_MODULE_NAME mothur/1.42.3 
setenv         LAST_MODULE_VERSION 1.42.3 
-------------------------------------------------------------------
```

<h2> Why does the cluster forget my choice of modules? </h2>

Environment modules are desined to make a temporary change to your evironment and only affect the session in which they are loaded. Loading a module in one SSH session 
will not affect any other SSH session or any jobs submitted from that session. Modules must be loaded in every session where they will be used.

<h2> How do module files interact with Perl, Python and R? </h2>

Perl, Python and R all have their own library/module systems: [CPAN](https://www.cpan.org/), [PyPI](https://pypi.org/) and [CRAN](https://cran.r-project.org/). Information on 
installing a library or module for yourself can be found on the help pages for [Perl](../others#perl), [Python](../python) and [R](../r/). If you are having difficulty
installing the library or module then can help you.  Please email such requests to [restech.support@unsw.edu.au](mailto:restech.support@unsw.edu.au).