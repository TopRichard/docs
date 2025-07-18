# Building software on top of EESSI

## Building software on top of EESSI with EasyBuild
Building on top of EESSI with EasyBuild is relatively straightforward. One crucial feature is that EasyBuild supports building against operating system libraries that are _not_ in a standard prefix (such as `/usr/lib`). This is required when building against EESSI, since all of the software in EESSI is built against the [compatibility layer](../compatibility_layer.md).

### Starting the EESSI software environment
Start your environment as described [here](../using_eessi/setting_up_environment.md)

### Using the EESSI-extend module
The `EESSI-extend` module facilitates building on top of EESSI using EasyBuild. It does a few key things:

1. It configures EasyBuild to match how the rest of the EESSI software is built
2. It configures EasyBuild to use a certain installation path (e.g. in your homedir), taking into account the hardware architecture you are building on
3. It adds the relevant subdirectory from your installation path to your `MODULEPATH`, to make sure your newly installed modules are available
4. It loads the EasyBuild module

The `EESSI-extend` module recognizes a few environment variables. To print an up-to-date list, check the module itself
```
module help EESSI-extend/2023.06-easybuild
```

The installation prefix is determined by `EESSI-extend` through the following logic:

1. If `$EESSI_CVMFS_INSTALL` is set, software is installed in `$EESSI_SOFTWARE_PATH`. This variable shouldn't be used by users and would only be used by CVMFS administrators of the EESSI repository.
2. If `$EESSI_SITE_INSTALL` is set, the EESSI site installation prefix (`$EESSI_SITE_SOFTWARE_PATH`) will be used. This is typically where sites hosting a system that has EESSI deployed would install additional software on top of EESSI and make it available to all their users.
3. If `$EESSI_PROJECT_INSTALL` is set (and `$EESSI_USER_INSTALL` is not set), this prefix will be used. You should use this if you want to install additional software on top of EESSI that should also be usable by your project partners on the same system. For example, if you have a project space at `/project/my_project` that all your project partners can access, you could set `export EESSI_PROJECT_INSTALL=/project/my_project/eessi`. Make sure that this directory has the SGID permission set (`chmod g+s $EESSI_PROJECT_INSTALL`). This way, all the additional installations done with `EESSI-extend` will be put in that prefix, and will get the correct UNIX file permissions so that all your project partners can access it.
4. If `$EESSI_USER_INSTALL` is set, this prefix will be used. You should use this if you want to install additional software on top of EESSI just for your own user. For example, you could set `export EESSI_USER_INSTALL=$HOME/my/eessi/extend/prefix`, and `EESSI-extend` will install all software in this prefix. Unix file permissions will be set such that these installations will be readable only to the user.

If none of the above apply, the default is a user installation in `$HOME/EESSI` (i.e. effectively the same as setting `EESSI_USER_INSTALL=$HOME/EESSI`).

Here, we assume you are just an end-user, not having set any of the above environment variables, and loading the `EESSI-extend` module with the default installation prefix:

```
module load EESSI-extend/2023.06-easybuild
```

Now, if we check the EasyBuild configuration


```
eb --show-config
allow-loaded-modules (E) = EasyBuild, EESSI-extend
buildpath            (E) = /tmp/<user>/easybuild/build
containerpath        (E) = /tmp/<user>/easybuild/containers
debug                (E) = True
experimental         (E) = True
filter-deps          (E) = Autoconf, Automake, Autotools, binutils, bzip2, DBus, flex, gettext, gperf, help2man, intltool, libreadline, libtool, M4, makeinfo, ncurses, util-linux, XZ, zlib
filter-env-vars      (E) = LD_LIBRARY_PATH
hooks                (E) = /cvmfs/software.eessi.io/versions/2023.06/init/easybuild/eb_hooks.py
ignore-osdeps        (E) = True
installpath          (E) = /home/<user>/eessi/versions/2023.06/software/linux/x86_64/amd/zen2
module-extensions    (E) = True
packagepath          (E) = /tmp/<user>/easybuild/packages
prefix               (E) = /tmp/<user>/easybuild
read-only-installdir (E) = True
repositorypath       (E) = /tmp/<user>/easybuild/ebfiles_repo
robot-paths          (D) = /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/amd/zen2/software/EasyBuild/4.9.4/easybuild/easyconfigs
rpath                (E) = True
sourcepath           (E) = /tmp/<user>/easybuild/sources
sticky-bit           (E) = True
sysroot              (E) = /cvmfs/software.eessi.io/versions/2023.06/compat/linux/x86_64
trace                (E) = True
umask                (E) = 077
zip-logs             (E) = bzip2
```

Apart from the `installpath`, this is exactly how EasyBuild is configured when software is built for EESSI itself.

!!! note
    Be aware that `EESSI-extend` will optimize the installation for your current hardware architecture, and the `installpath` also contains this architecture in it's directory structure (just like regular EESSI installations do). This means you should run the installation on the node type on which you also want to use the software. If you want the installation to be present for multiple node types, you can simply run it once on each type of node.

And, if we check our `MODULEPATH`, we see that the `installpath` that EasyBuild will use here is prepended
```
$ echo $MODULEPATH
/home/<user>/eessi/versions/2023.06/software/linux/x86_64/amd/zen2/modules/all:...
```

### Building
Now, you are ready to build. For example, suppose you want to install `netcdf4-python-1.6.5-foss-2023b.eb` (which is not present at the time of writing), you run:

```
eb netcdf4-python-1.6.5-foss-2023b.eb
```

!!! note
    If this netCDF for python module is available by the time you are trying, you can force a local rebuild by adding the `--rebuild` argument in order to experiment with building locally, or pick a different EasyConfig to build.

### Using the newly built module

If the installation was done in the site installation path (i.e. `EESSI_SITE_INSTALL` was set, and things were installed in `/cvmfs/software.eessi.io/host_injections/...`), the modules are available by default to anyone who has initialized the EESSI software environment.

If the installation through `EESSI-extend` was done in a `EESSI_PROJECT_INSTALL` or `EESSI_USER_INSTALL` location, one has to make sure to load the `EESSI-extend` module before loading the module of interest, since this adds those prefixes to the `MODULEPATH`.

If we don't have the `EESSI-extend` module loaded, it will not find any modules installed in the `EESSI_PROJECT_INSTALL` or `EESSI_USER_INSTALL` locations:
```
$ module unload EESSI-extend
$ module av netcdf4-python/1.6.5-foss-2023b
No module(s) or extension(s) found!
```

But, if we load `EESSI-extend` first:

```
$ module load EESSI-extend/2023.06-easybuild
$ module av netcdf4-python/1.6.5-foss-2023b

---- /home/<user>/eessi/versions/2023.06/software/linux/x86_64/amd/zen2/modules/all ----
   netcdf4-python/1.6.5-foss-2023b
```

This means you'll _always_ need to load the `EESSI-extend` module if you want to use these modules (also, and particularly when you want to use them in a job script).

## Manually building software on top of EESSI (without EasyBuild)

!!! warning

    Even with the help of the `buildenv` module, this documentation is intended for "experts" in the runtime linker and it's behaviour,
    and most cases are untested. Any feedback on this topic is highly appreciated. 
    
Building and running software on top of EESSI without EasyBuild is now more straightforward thanks to the freshly developed `buildenv` module. 

EESSI uses a [compatibility layer](../compatibility_layer.md) to ensure that it takes as few libraries from the host as possible. Starting an EESSI prefix
shell before building and loading any of the `buildenv` modules (one per `foss` toolchain), this is the safest way to make sure all libraries will point to the required locations in the compatibility layer and do not leak in from the host operating system. A step by step: 

* First of all, load the environment by starting an EESSI shell as described [here](https://www.eessi.io/docs/using_eessi/setting_up_environment). 
```
source /cvmfs/software.eessi.io/versions/2023.06/init/lmod/bash
```
* Load one of the `buildenv` modules, there's one per `foss` toolchain available in EESSI. (`foss` is a good option as it will also
  include MPI with OpenMPI and math libraries via FlexiBLAS/FFTW). 
* Compile as you would do normally. The module includes wrappers for the compilers (`gcc`, `g++`, `gfortran`)  and linkers (`ld`, `ld.bfd`, `ld.gold`).
* Make sure the library resolution points to the EESSI stack. For this, `ldd` from compatibility layer and **not** `/usr/bin/ldd` should be used
  when checking the binary.

* Run! 

To exemplify this, take the classic MPI Hello World example code:

```
/*The Parallel Hello World Program*/
#include <stdio.h>
#include <mpi.h>

int main(int argc, char **argv)
{
   int rank;

   MPI_Init(&argc,&argv);
   MPI_Comm_rank(MPI_COMM_WORLD, &rank);

   printf("Hello World from MPI rank %d\n", rank);

   MPI_Finalize();
}

```

As described in the steps above, prepare the environment and load the required dependencies. For this case, we will use `buildenv/default-foss-2023b` module.

```
# Starting the EESSI shell
$ source /cvmfs/software.eessi.io/versions/2023.06/init/bash

# Loading the environment
{EESSI 2023.06} $ module load buildenv/default-foss-2023b
```

You're already good to compile!

```
# Compile the code manually
{EESSI 2023.06} $ mpicc -o HelloWorld mpi.c
```

This is the moment to check if the compiler picked all the libraries from the software and compatibility layer, not the host.

Look at the difference on the library solving when using the compatibility layer ldd from the host one:

```sh

# ldd from the compatibility layer, notice how all libraries are resolved from the software layer -> "/cvmfs/software.eessi.io/versions/2023.06/software", the libc and the interpreter point to the compatibility layer, so all good to go!

{EESSI 2023.06} hvela@ThinkPad-hvela:~/HPCNow/EESSI/develop/buildenv_tests$ ldd HelloWorld 
	linux-vdso.so.1 (0x00007ffc1d112000)
	libmpi.so.40 => /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/intel/haswell/software/OpenMPI/4.1.6-GCC-13.2.0/lib/libmpi.so.40 (0x00007ed09aa64000)
	libc.so.6 => /cvmfs/software.eessi.io/versions/2023.06/compat/linux/x86_64/lib/../lib64/libc.so.6 (0x00007ed09a893000)
	[...]
	libevent_pthreads-2.1.so.7 => /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/intel/haswell/software/libevent/2.1.12-GCCcore-13.2.0/lib64/libevent_pthreads-2.1.so.7 (0x00007ed09a4de000)
	libm.so.6 => /cvmfs/software.eessi.io/versions/2023.06/compat/linux/x86_64/lib/../lib64/libm.so.6 (0x00007ed09a3fe000)
	/cvmfs/software.eessi.io/versions/2023.06/compat/linux/x86_64/lib64/ld-linux-x86-64.so.2 (0x00007ed09ab8e000)

```

Now is the moment of truth, if everything looks right when checking with ldd, you should be fine to run the program:

```
{EESSI 2023.06} $ mpirun -n 2 HelloWorld
Hello World from rank 0
Hello World from rank 1

```

!!! warning

    RPATH should never point to a compatibility layer directory, only to software layer ones, as all resolving is done via the runtime linker (`ld-linux*.so`)
    that is shipped with EESSI, which automatically searches these locations.

The biggest downside of this approach is that your executable becomes bound to the architecture you linked your libraries for, i.e., if you add to your executable RPATH a `libhdf5.so`compiled for `intel_avx512`, you will not be able to run that binary on a machine with a different architecture. If this is an issue for you, you should look into how EESSI itself organises the location of binaries and perhaps leverage the relevant environment variables (e.g., `EESSI_SOFTWARE_SUBDIR`).



