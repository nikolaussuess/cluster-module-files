# Tcl Module files
This repository contains some Tcl module files for (SLURM) clusters. Note that you most likely will have to adapt them to your needs (change paths, ...).

## OpenBLAS v0.3.28
This module provides **OpenBLAS** as a system-wide installation via Environment Modules. It configures compiler and runtime environment variables and integrates with SLURM to control thread usage and avoid CPU oversubscription.

### Installation Requirement
OpenBLAS **must be installed** at the following location:
```
/opt/OpenBLAS/0.3.28
```

If a different OpenBLAS version is required, the module file must be adapted:
- Update the version variable
- Update the installation prefix
- Ensure the corresponding OpenBLAS installation exists at the configured path

The module assumes a standard OpenBLAS directory layout.

### Environment Changes

When the module is loaded, the following paths are added to the environment:

- `PATH`: `/opt/OpenBLAS/0.3.28/bin`
- `LD_LIBRARY_PATH`: `/opt/OpenBLAS/0.3.28/lib`
- `LD_RUN_PATH`: `/opt/OpenBLAS/0.3.28/lib`
- `CPATH`: `/opt/OpenBLAS/0.3.28/include`

### Thread Control and SLURM Integration
- **Default Behavior**<br>
  If the module is loaded inside a SLURM job and:
    - `SLURM_CPUS_PER_TASK` is set
    - `OPENBLAS_NUM_THREADS` is not already set by the user

    Then the module sets `OPENBLAS_NUM_THREADS = SLURM_CPUS_PER_TASK`. This prevents OpenBLAS from using more CPU threads than allocated by SLURM. You can simply load the environment with
```
module load openblas/0.3.28
```

- The number of OpenBLAS threads can always be set manually. If `OPENBLAS_NUM_THREADS` is already defined, the module will not override it.
```bash
export OPENBLAS_NUM_THREADS=4
module load openblas/0.3.28
```

If the module is loaded inside a SLURM job but `SLURM_CPUS_PER_TASK` is not set, a warning is printed:
```
Warning: SLURM_CPUS_PER_TASK is not set, OpenBLAS might use all available CPUs by default.
```


## OpenMPI
This module provides **OpenMPI** as a system-wide MPI implementation via Environment Modules. It configures the required runtime, compiler, and manual page paths to make OpenMPI available in the user environment.

The following versions were added:

- `4.1.1`
- `5.0.3`

Please make sure to compile these versions to `/opt/openmpi/<version>`!

If a different OpenMPI version is required, the module file must be adapted:

- Update the version variable
- Update the installation prefix (root)
- Ensure the corresponding OpenMPI installation exists at the configured path

The module will not function correctly if the installation directory is missing.

### Environment Changes

When the module is loaded, the following paths are added to the environment:

- `PATH`: `/opt/openmpi/<version>/bin`
- `MANPATH`: `/opt/openmpi/<version>/share/man`
- `LD_LIBRARY_PATH`: `/opt/openmpi/<version>/lib`
- `LD_RUN_PATH`: `/opt/openmpi/<version>/lib`

These changes make OpenMPI binaries, libraries, and manual pages available to the user.

### Usage Example
```
# Load OpenMPI:
module load openmpi/4.1.1
# Check the MPI version:
mpirun --version
# Compile an MPI program:
mpicc hello.c -o hello
# Run an MPI program:
mpirun -np 4 ./hello
# Unload the module
module purge
```

## Python via venv
The module files in the `python` subdirectory provide a system Python installation together with automatic management of Python virtual environments using `venv`. It is intended for use with Environment Modules on shared systems and HPC clusters (e.g. SLURM).

### Python Installation
**Python must be installed at:**<br>
`/opt/python/<version>`

The Python executable must be available at:<br>
`/opt/python/<version>/bin/python3`

If a different Python version is required, the module file must be adapted accordingly:

- Update the Python version in the module file
- Adapt the installation path
- Ensure the matching Python version is installed at the configured location

In order to install Python, do the following:

1) Download Python and extract it. E.g., for version `3.7.17`:
```bash
wget https://www.python.org/ftp/python/3.7.17/Python-3.7.17.tgz
tar xzf Python-3.7.17.tgz
cd Python-3.7.17
```
2) In order to compile python from source, you will need further dependencies, depending on the python modules you need. We compiled with the following packages installed:
```bash
apt install build-essential

# optional
apt search libffi-dev
apt install libffi-dev
apt install libbz2-dev
apt install lzma liblzma-dev libbz2-dev
apt show  libgdbm-compat-dev
apt install  libgdbm-compat-dev
apt install libsqlite3-dev
apt install libgdbm-dev
apt install uuid-dev lzma-dev liblzma-dev
```
3) Adapt the prefix and compile:
```bash
./configure --prefix=/opt/python/<version> --enable-optimizations
make
make install

# e.g.:
cd Python-3.7.17
./configure --prefix=/opt/python/3.7.17 --enable-optimizations
make
make install
cd ..
```

**Note:** For Python 3.6.x, you will have to apply a patch, since this version is no longer compatible with the current version of GCC. See [https://github.com/pyenv/pyenv/issues/1889#issuecomment-837697366](https://github.com/pyenv/pyenv/issues/1889#issuecomment-837697366)

Here is a full list to install all python versions from the python subdirectory (**except python 3.6** because of the patch):
```bash
mkdir -p /opt/python

wget https://www.python.org/ftp/python/3.7.17/Python-3.7.17.tgz
tar xzf Python-3.7.17.tgz
cd Python-3.7.17/
./configure --prefix=/opt/python/3.7.17 --enable-optimizations
make
make install
cd ..

wget https://www.python.org/ftp/python/3.8.20/Python-3.8.20.tgz
tar xzf Python-3.8.20.tgz
cd Python-3.8.20/
./configure --prefix=/opt/python/3.8.20 --enable-optimizations
make
make install
cd ..

wget https://www.python.org/ftp/python/3.9.21/Python-3.9.21.tgz
tar xzf Python-3.9.21.tgz
cd Python-3.9.21
./configure --prefix=/opt/python/3.9.21 --enable-optimizations
make
make install
cd ..

wget https://www.python.org/ftp/python/3.10.16/Python-3.10.16.tgz
tar xzf Python-3.10.16.tgz
cd Python-3.10.16
./configure --prefix=/opt/python/3.10.16 --enable-optimizations
make
make install
cd ..

wget https://www.python.org/ftp/python/3.11.11/Python-3.11.11.tgz
tar xzf Python-3.11.11.tgz
cd Python-3.11.11
./configure --prefix=/opt/python/3.11.11 --enable-optimizations
make
make install
cd ..

wget https://www.python.org/ftp/python/3.12.9/Python-3.12.9.tgz
tar xzf Python-3.12.9.tgz
cd Python-3.12.9
./configure --prefix=/opt/python/3.12.9 --enable-optimizations
make
make install
cd ..
```


### Installation of the module file
After you have installed python, just move the module files (with the correct paths and version numbers) to `/etc/environment-modules/modules/python`.

You might also want to *create symbolic links* so that e.g. `module load python/3.12` will automatically load `python/3.12.9`. This can be done e.g. with:
```bash
cd /etc/environment-modules/modules/python
for file in *; do 
    IFS='.' read -ra parts <<<"$file"
    echo ${parts[@]}
    ln -s "${parts[0]}.${parts[1]}.${parts[2]}" "${parts[0]}.${parts[1]}"
done
```

**The module will fail to load if the installation directory or Python binary does not exist.**


### Conflicting Modules

The following modules conflict with this module and cannot be loaded at the same time:

- miniconda
- miniforge

### Module usage
The configuration is done via environment variables.

| Environment variable | possible values | Default value | Description |
|--- | --- | --- | --- |
| `ENV_NAME` | name without whitespace characters (i.e., `[a-zA-Z0-9_\-\.]`) | `"job_${SLURM_JOB_ID}"` for temporary environments when using SLURM,<br>"testenv" for permanent environments or when SLURM is not used | Set a custom name for your environment (default: environment named after SLURM job ID).<br>We RECOMMEND to keep the default name for temporary environments but use a custom one for permanent environments. |
| `ENV_MODE` | `{ "temporary", "permanent", "none" }` | `"temporary"` | Choose between temporary (default) and permanent environments, or do not automatically create/load an environment.<br><br>By default, environments created for SLURM jobs are temporary and are automatically deleted when the job finishes. However, you can set the environment to be permanent, allowing it to persist beyond the completion of the job. Temporary environments should be stored locally on an SSD or in RAM (default location `/srv/environments/$USER/venvs/$ENV_NAME`), while persistent ones could be stored in the home directory on an NFS (default location `$HOME/venvs/$ENV_NAME`).<br><br>If you do NOT want the module to automatically load or create an environment, you can set `ENV_MODE` no `none`. Do this e.g. if you have your environment stored at a different location. This mode will only add the python binary directory to the PATH variable (e.g., `PATH=/opt/python/3.6.15/bin:$PATH`) so that the python and pip executables can be used to e.g. create a new environment. You can then call `"source your/env/dir/bin/activate"` yourself.|
| `KEEP_EXISTING` | `{ 0, 1 }` | `0` | Prevent the deletion of an existing temporary environment before creation or after job execution. This way, you could take advantage of a fast tmpfs (stored in RAM) while your (temporary) environment is not deleted (until a reboot of the node).<br><br>This option is only for advanced users and otherwise not recommended for production use.<br><br>CURRENTLY mostly UNTESTED.|

For simple cases, you do not have to override any of these variables.

The module can be loaded with `module load python` (or, if you require a specific version: `module load python/version`). This sets up the environment (i.e., it eventually creates the environment and adapts the environment variables so that e.g. `python`/`pip` can be found). After the job, you SHOULD call `module purge` to cleanup.

The module behavior is controlled via the environment variable `ENV_MODE`.

Supported values:

| Value        | Description |
|-------------|-------------|
| temporary   | Create and manages a temporary virtual environment (default). |
| permanent   | Creates a permanent virtual environment in the user's home directory (at `$HOME/venvs/$ENV_NAME`). |
| none        | Do not create or activate any virtual environment. |

If `ENV_MODE` is not set, it defaults to `temporary`.

Depending on `ENV_MODE`, virtual environments are stored in:

- temporary: `/srv/environments/$USER/venvs`
- permanent: `$HOME/venvs`
- none: no virtual environment is used

To load the module and set up the Python environment, use the following command:
```bash
# module load python/version; e.g.
module load python/3.6.15
```

If you wish to use a permanent environment called "project_x" with python version 3.8.20, then use e.g.:
```bash
export ENV_MODE="permanent"
export ENV_NAME="project_x"
module load python/3.8.20

# now you could e.g. install libraries, e.g. with
# pip install networkx

# Execute your program here, e.g.
# python3 my_program.py

# Cleanup
module purge
```

### Known issues
* *Python Version Mismatch:*<br>
  If you attempt to load a virtual environment that was created with a different version of Python, a warning will be displayed, and the environment will use the original Python version rather than the one specified by the module.


