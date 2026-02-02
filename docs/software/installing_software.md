title: Installing Software

Environment software installation on Katana follows shared-system best practices. Always check existing software availability before installing anything yourself.

---

<h2> Checking if a Package Is Already Installed </h2>

!!! note
    Before installing software yourself, check whether it is already available via the system environment or as a module.
    You may also want to ask your research colleagues if they have already installed it.

Some commonly used software packages are included with Katana's operating system. You can list installed system packages with:

```bash
yum list installed
```

!!! warning
    Do **not** attempt to run administrative commands as a user.
    This includes `apt-get`, `yum install`, `su`, or `sudo`.

You can also check available applications using Environment Modules:

```bash
module avail
```

---

<h2> R and Python Packages </h2>

Many Python and R packages are already installed on Katana.

Documentation for installing your own packages can be found here:
- [Python virtual environments](../python#python-virtual-environments)
- [Installing R libraries](../r#installing-libraries)

!!! note
    If you encounter issues installing packages yourself, contact
    restech.support@unsw.edu.au for assistance.

---

<h2> Installing Binary Packages </h2>

Installing from source is preferred for performance reasons, but some software is only distributed as precompiled binaries.

When downloading binaries, ensure they match Katana��s platform.

!!! note
    Katana login and compute nodes run **Rocky Linux 8.9 (64?bit, x86_64 / AMD64)**.

    - Small software: install in your **home directory** (backed up nightly)
    - Large software: install in your **scratch directory**

Example download:

```bash
wget https://website.org/binary/application
```

Make the file executable:

```bash
chmod u+rx ./application
```

---

<h2> Compiling Software from Source </h2>

Compiling from source is generally more efficient but can be more complex.

!!! note
    - Small software: install in your **home directory**
    - Large software: install in your **scratch directory**

---

<h2> Installing Software from GitHub </h2>

Source code is commonly hosted on GitHub. Git is available by default on Katana.

UNSW also maintains its own [GitHub organisation](../../using_katana/github).

Installation steps depend on how the project is structured by the author.

---

<h2> Installing a GitHub Release </h2>

Many projects provide versioned *releases* to ensure reproducibility.

You may be able to download either a binary or source release:

```bash
wget https://github.com/project/project/releases/download/v1.48.1/project.linux_8.zip
```

```bash
wget https://github.com/project/project/archive/refs/tags/v1.48.1.tar.gz
```

---

<h2> Cloning a GitHub Repository </h2>

If no suitable release exists, clone the repository directly:

```bash
git clone https://github.com/project/project.git
```

This creates a local copy of the source code along with documentation.

---

<h2> README and INSTALL Files </h2>

- **README**: overview and basic usage or install steps
- **INSTALL**: detailed compilation and configuration instructions

Always read both files fully before attempting to compile software.

---

<h2> Compilers </h2>

The system compilers `gcc` and `ld` are recommended by default.

Some software requires specific compilers or versions. Katana provides many via modules, including:

- [Intel Compilers and Software Libraries](../others#intel-compilers-and-software-libraries)

!!! note
    Always install software inside an **interactive session** (`qsub -I`),
    not directly on a login node.

---

<h2> Configuring the Build </h2>

Most source packages provide a configuration script.

To install into your home directory:

```bash
./configure --prefix=$HOME/apps/{PACKAGE}/{VERSION}
```

Then build and install:

```bash
make
make install
```

---

<h2> Creating Personal Module Files </h2>

You can manage multiple versions of your own software using personal environment modules.

A template module file is available at:

```bash
/ apps / modules / templates / module_file
```

You will likely need to customise:

- `basepath`
- `version`
- `url`
- `installed`
- `compiled_with`
- `mpiversion`
- `prereq`

Example values:

```text
set basepath $env(HOME)/apps/{SOFTWARE_NAME}
set version  {VERSION_NUMBER}
```

!!! note
    Add the following line to your `~/.bashrc` to enable personal modules:

    ```bash
    module use --append $HOME/apps/Modules
    ```

Once configured, you can load your software like any other module.
