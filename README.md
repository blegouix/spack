# PDI spack repository

This is a [spack](https://spack.io/) repository with recipes for [PDI](https://pdi.dev) and its plugins.
It can be installed in a few simple steps:
1. [setup spack](#step-1-setup),
2. [(optional): reuse already installed packages](#step-2-optional-reuse-already-installed-packages),
3. [(optional): setup a non-default compiler](#step-3-optional-setup-a-non-default-compiler),
4. [install](#step-4-installation).


## Step #1: Setup

To use it, you should first setup spack:
```sh
# 1. Get and enable Spack
git clone https://github.com/spack/spack.git
. spack/share/spack/setup-env.sh
# 2. Get and enable this spack repo
git clone https://github.com/pdidev/spack.git spack/var/spack/repos/pdi
spack repo add spack/var/spack/repos/pdi
```


## Step #2 (optional): reuse already installed packages

### Option #2.1: Reuse already installed packages on super-computers that use spack

You can tell your local spack instance that there is an upstream spack instance by editing `spack/etc/spack/defaults/upstreams.yaml`.
```yaml
upstreams:
  name-of-spack-instance:
    install_tree: /path/to/spack/opt/spack
```

This will let spack use the already installed packages when it sees fit, (which is almost never).
You can however force spack to use already installed package by using the flag `--reuse` when calling `spack install`.


**For example on [Ruche](https://mesocentre.pages.centralesupelec.fr/user_doc/ruche/09_softwares/)**, you can do:
```sh
cat<<EOF > spack/etc/spack/defaults/upstreams.yaml
upstreams:
  ruche-system:
    install_tree: /gpfs/softs/spack/opt/spack/
EOF
```


### Option #2.2: Reuse already installed packages on super-computers that does not use spack

If there are packages already present on the machine that you want spack to use _e.g._ MPI, you can specify them as externals through the `packages.yaml` file found in either in a Spack installation’s `etc/spack/` or a user’s `~/.spack/` directory.  Here’s an example of an external configuration:
```yaml
packages:
  openmpi:
    externals:
    - spec: "openmpi@1.4.3%gcc@4.4.7 arch=linux-debian7-x86_64"
      prefix: /opt/openmpi-1.4.3
    - spec: "openmpi@1.4.3%gcc@4.4.7 arch=linux-debian7-x86_64+debug"
      prefix: /opt/openmpi-1.4.3-debug
    - spec: "openmpi@1.6.5%intel@10.1 arch=linux-debian7-x86_64"
      prefix: /opt/openmpi-1.6.5-intel
```
Is it recommended to only put MPI implementations, CMake and `openssl` as externals and let spack take care of the rest.


## Step #3 (optional): setup a non-default compiler

For compilers, you can specify them in the user's `~/.spack/linux/compilers.yaml`. 
```yaml
compilers:
- compiler:
    spec: gcc@4.8.5
    paths:
      cc: /usr/bin/gcc
      cxx: /usr/bin/g++
      f77: /usr/bin/gfortran
      fc: /usr/bin/gfortran
    flags: {}
    operating_system: centos7
    target: x86_64
    modules: []
    environment: {}
    extra_rpaths: []
```

This can be done automatically by calling `spack compiler find` when the compilers are loaded.
This is needed if you want to use the Intel compilers. 


**For example on [Ruche](https://mesocentre.pages.centralesupelec.fr/user_doc/ruche/09_softwares/)**, you can do:
```sh
spack load gcc@9.2.0
spack compiler find
spack unload gcc@9.2.0
```


## Step #4: Installation

You can install PDI and most of its plugins using the following instructions after you've [done the setup](#setup):
```sh
# Install PDI and most of its plugins
spack install pdiplugin-decl-hdf5 pdiplugin-decl-netcdf pdiplugin-mpi pdiplugin-pycall pdiplugin-serialize pdiplugin-set-value pdiplugin-trace pdiplugin-user-code
```

If you only need some of the plugins, you can adapt the last line.
