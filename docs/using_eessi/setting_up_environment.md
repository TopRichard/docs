# Setting up your environment

To set up the EESSI environment, you can either load an EESSI module with Lmod or source a bash initialisation script.

## Loading an EESSI module with Lmod

There are a few different scenarios where you may want to set up the EESSI environment by loading an EESSI module with Lmod:

   !!! note "Why do we recommend to unset `MODULEPATH`?"

   Unsetting the `$MODULEPATH` environment variable, which tells Lmod in which directories environment module files are available, may be necessary. The underlying reason to suggest this is that EESSI and your system are most likely based on two different operating system distributions - EESSI uses [compatibility layer ](../compatibility_layer.md "EESSI compatibility layer"), your system almost certainly uses some other Linux distribution. If you can find a way to ensure that the _software stacks_ from your site and EESSI do not mix (in particular when someone is building new software!), then this should be good enough.

1.  You are already using Lmod with version >= 8.6

    In this case, we _recommend_ unsetting `$MODULEPATH`, because EESSI is not designed to mix modules coming from EESSI and from your system.

    ``` { .bash .copy }
    export MODULEPATH=/cvmfs/software.eessi.io/init/modules
    module load EESSI/2023.06
    ```

    :clap: Your environment is now set up, you are ready to start running software provided by EESSI!

2.  You are using an Lmod with a version older than 8.6 or any other tool utilizing `MODULEPATH` ([Tcl-based Environment Modules](https://modules.sourceforge.net/))

    You should unset `$MODULEPATH` to prevent Lmod from attempting to build a cache for your module tree (as this can be very slow if you have a lot of modules). Again, unsetting the `$MODULEPATH` should be considered as a good idea in general so you do not mix local and EESSI
    modules: 

    ``` { .bash .copy }
    unset MODULEPATH
    source /cvmfs/software.eessi.io/versions/2023.06/init/lmod/bash
    ```

    :clap: Your environment is now set up, you are ready to start running software provided by EESSI!

3.  Should Lmod be unavailable and `MODULEPATH` not utilized, you can initialise EESSI via an Lmod module by directly sourcing the Lmod initialisation script (this script automatically loads the EESSI module)

    ``` { .bash .copy }
    source /cvmfs/software.eessi.io/versions/2023.06/init/lmod/bash
    ```

    :clap: Your environment is now set up, you are ready to start running software provided by EESSI!

## Sourcing the EESSI `bash` initialisation script

You can initialise EESSI (in a non-reversible way) by running the command:

   !!! info "This is supported exclusively for bash shell users. If you're using a different shell, please use the alternative approach"

``` { .bash .copy }
source /cvmfs/software.eessi.io/versions/2023.06/init/bash
```

This may take a while as data is downloaded from a Stratum 1 server which is
part of the CernVM-FS infrastructure to distribute files. You should see the
following output:

``` { .bash .no-copy }
Found EESSI repo @ /cvmfs/software.eessi.io/versions/2023.06!
archdetect says x86_64/amd/zen2  # (1)
archdetect could not detect any accelerators
Using x86_64/amd/zen2 as software subdirectory.
Found Lmod configuration file at /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/amd/zen2/.lmod/lmodrc.lua
Found Lmod SitePackage.lua file at /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/amd/zen2/.lmod/SitePackage.lua
Using /cvmfs/software.eessi.io/host_injections/2023.06/software/linux/x86_64/amd/zen2 as the site extension directory for installations.
Using /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/amd/zen2/modules/all as the directory to be added to MODULEPATH.
Using /cvmfs/software.eessi.io/host_injections/2023.06/software/linux/x86_64/amd/zen2/modules/all as the site extension directory to be added to MODULEPATH.
Found libcurl CAs file at RHEL location, setting CURL_CA_BUNDLE
Initializing Lmod...
Prepending /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/amd/zen2/modules/all to $MODULEPATH...
Prepending site path /cvmfs/software.eessi.io/host_injections/2023.06/software/linux/x86_64/amd/zen2/modules/all to $MODULEPATH...
Environment set up to use EESSI (2023.06), have fun!
{EESSI 2023.06} [user@system ~]$  # (2)!
```

What is reported at `(1)` depends on the CPU architecture of the machine you are running the `source` command.

At `(2)` is the prompt indicating that you have access to the EESSI software stack.

:clap: Your environment is now set up, you are ready to start running software provided by EESSI!