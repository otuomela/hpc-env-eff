# Modules in Roihu

!!! warning
    This tutorial requires that you have a
    [user account at CSC](https://docs.csc.fi/accounts/how-to-create-new-user-account/)
    that is a member of a project that
    [has access to the Roihu service](https://docs.csc.fi/accounts/how-to-add-service-access-for-project/).

!!! warning
    You must also have [set up SSH keys](../access/ssh.md) and
    [downloaded an SSH certificate](../access/certificate.md)
    that is still valid.

## Checking the default modules

1. Log in to Roihu-CPU with your user credentials
   (SSH or [Roihu web interface](https://www.roihu.csc.fi)):

    ```bash
    ssh <username>@roihu-cpu.csc.fi    # replace <username> with your CSC username, e.g. myname@roihu-cpu.csc.fi
    ```

2. Try a `module` command! Check out which modules are loaded by default as you
   log in to Roihu:

    ```bash
    module list
    ```

## More module commands with GROMACS as an example

!!! note
    Let's imagine that you want to do some molecular dynamics simulations
    using the [GROMACS](https://www.gromacs.org/about.html) application.

!!! tip
    It is always a good idea to start by checking
    [the application list in Docs CSC](https://docs.csc.fi/apps/)
    to see whether this application is installed in Roihu and how to use it.

1. Check out the [GROMACS page](https://docs.csc.fi/apps/gromacs/).
2. Skim through the documentation and verify that the license allows you
   to use the software.
3. Check which module command you need to run to be able to
   load GROMACS in Roihu.
4. Back on the command-line, check which GROMACS versions are available
   in Roihu:

    ```bash
    module spider gromacs
    ```

    !!! warning
        This might take a while as the command searches through all
        the available modules.

    !!! note
        The list can be quite long. You can go to the next line with
        Enter, or stop viewing by typing `q`.

5. Check if some versions can be loaded directly, *i.e.* are compatible
   with your currently loaded module environment:

    ```bash
    module avail gromacs
    ```

    !!! tip
        Another quick way to list the available versions is by typing
        the load command until the module name and then hit `TAB` twice:

        ```text
        $ module load gromacs # and here double press TAB
        gromacs         gromacs/2025.2  gromacs/2025.4  gromacs/2026.1
        gromacs/2025.1  gromacs/2025.3  gromacs/2026.0
        ```

6. Which version is loaded with the default command?
   Is it the newest version? Try:

    ```bash
    module load gromacs
    ```

7. Do you notice any changes in the output of `module list` compared to
   the first try? Try this again:

    ```bash
    module list
    ```

    !!! warning
        If no version is given in the module command, the default version
        is loaded.

    - The default version is typically the latest **stable** version of
      the program.
    - It is recommended to also provide the version in the module load
      command, as the default version may change.

8. Let's try loading the 2025.1 version specifically:

    ```bash
    module load gromacs/2025.1
    module list
    ```

    !!! warning
        It is generally best to use the latest versions since they are
        typically more performant than old ones and may have useful new features.

9. If you want to do something else in the same session, it is usually
   best to reset the module environment to the default settings. This can
   be done by first removing all loaded modules and then loading
   the default environment:

    ```bash
    module purge            # Purge all (non-sticky) modules
    module list             # List the loaded modules
    module load StdEnv      # Load the default module environment
    module list             # List the loaded modules
    ```

### Loading a module with non-default dependencies

!!! note
    As an example of a module with non-default dependencies, you can
    try to load the GPU version of CP2K 2026.1, a software package for
    electronic structure calculations.

1. Log in to Roihu-GPU with your user credentials
   (SSH or [Roihu web interface](https://www.roihu.csc.fi)):

    ```bash
    ssh <username>@roihu-gpu.csc.fi    # replace <username> with your CSC username, e.g. myname@roihu-gpu.csc.fi
    ```

2. The `cp2k/2026.1` module cannot be loaded in the default environment
   since it has different dependencies.
    - `module avail` will not show it and running `module load cp2k/2026.1`
      in the standard environment will give an error (feel free to try).
3. Check with the `module spider` command which other modules are needed:

    ```bash
    module spider cp2k/2026.1
    ```

4. Load all of the required modules manually before loading `cp2k/2026.1`:

    ```bash
    module purge
    module load gcc/13.4.0
    module load openmpi/5.0.10
    module load cp2k/2026.1
    module list
    ```

## More information

- If actually using GROMACS in Roihu, you would run the application as a batch job
  through the queueing system, which will be discussed in detail later.
- Check out an [example batch job script for GROMACS](https://docs.csc.fi/apps/gromacs/)
  to see how the module is recommended to be loaded
  (`module load gromacs-env` loads the latest minor release of a specific year).
