# Apptainer tutorial, part 2

!!! abstract "In this tutorial you will learn"
    
    - How to access host files from inside a container
    - How to pass environment variables to a container
    - How to find programs inside a container

:point_up_tone1:
This tutorial continues from part 1 and uses the same `tutorial.sif`
container image. Run the commands in the directory where you downloaded it.

## Using files

:speech_balloon:
Apptainer containers have their own internal file system that is separated
from the host file system.

- The internal file system is always read-only when the container is run with
  normal user privileges.

:thought_balloon:
In most real use cases you might want to access the host file system to read
and write files.

- To do this, you must bind a writable directory on the host file system to the
  internal file system of the container.

:bulb:
This is done using the command line argument `--bind` (or `-B`). The basic
syntax is `--bind /path/inside/host:/path/inside/container`.

:thought_balloon:
Some remarks:

- The bind path does not need to exist inside the container – it is created if
  necessary.
- More than one bind pair can be specified.
- The option is available for all run methods described in the previous
  tutorial.

Try it out:

1. To run these exercises on Roihu, use `sinteractive` or open a compute node
   shell in the [Roihu web interface](https://www.roihu.csc.fi):

    ```bash
    sinteractive --account <project>  # replace <project> with your CSC project, e.g. project_2001234
    ```

2. Try listing the contents of your project's `/projappl` directory (edit the path as needed)
   from inside the container without `--bind`:

    ```bash
    apptainer exec tutorial.sif ls /projappl/<project> # replace <project> with your CSC project, e.g. project_2001234
    ```

3. The container cannot see the host directory, so you will get a
   `No such file or directory` error.

4. Try binding the host directory `/projappl` to the directory `/projappl` inside
   the container:

    ```bash
    apptainer exec --bind /projappl:/projappl tutorial.sif ls /projappl/<project> # replace <project> with your CSC project, e.g. project_2001234
    ```

5. This time, the host directory is linked to the container directory and the
   command shows what the container sees inside `/projappl`.

    If the path is the same in both the host and the container, you can simplify the
    command a bit. This does the same as the above command:

    ```bash
    apptainer exec --bind /projappl tutorial.sif ls /projappl/<project> # replace <project> with your CSC project, e.g. project_2001234
    ```

    :bulb:
    You can use `--bind` to let the container find, for example, input data
    or configuration files in a certain directory.

6. Bind a host directory in `/projappl` to a directory called `/config` inside
   the container:

    ```bash
    apptainer exec --bind /projappl/<project>:/config tutorial.sif ls /config # replace <project> with your CSC project, e.g. project_2001234
    ```

    :bulb:
    You can use `--bind="$(csc-common-bind)"` to automatically take care of
    most common bind use cases.

## Environment variables

:speech_balloon:
Some software may require environment variables to be set, e.g., to point to
some reference data or a configuration file.

:speech_balloon:
Most environment variables set on the host are inherited by the container.

:point_up_tone1:
Sometimes this may be undesired, in which case the command line option
`--cleanenv` can be used to prevent the host environment from being inherited
by the container.

:speech_balloon:
To set an environment variable specifically inside the container, you can
set an environment variable `$APPTAINERENV_XXX` (where `XXX` is the variable
name) on the host before invoking the container.

1. Set some test variables:

    ```bash
    export TEST1="value1"
    export APPTAINERENV_TEST2="value2"
    ```

2. Compare the outputs of:

    ```bash
    env | grep TEST
    apptainer exec tutorial.sif env | grep TEST
    apptainer exec --cleanenv tutorial.sif env | grep TEST
    ```

    - The `env` command lists all set environment variables.
    - The first command is run on the host and we see `$TEST1` and
      `$APPTAINERENV_TEST2`.
    - The second command is run inside the container and we see `$TEST1`
      (inherited from the host) and `$TEST2` (specifically set inside the
      container by setting `$APPTAINERENV_TEST2` on the host).
    - The third command is also run inside the container, but this time we
      omitted the host environment variables so we only see `$TEST2`.

3. Note that any command-line variables on the host are substituted by their
   values when passed to the container:

    ```bash
    apptainer exec tutorial.sif echo $TEST1
    apptainer exec tutorial.sif echo $TEST2
    ```

    - The first line prints the value set on the host.
    - The second line results in an empty output because a variable called
      `$TEST2` has not been set on the host. It was `APPTAINERENV_TEST2="value2"`,
      remember?

4. If you need to pass environment variables to a container, in most cases it is
   easiest just to set them on the host. If this is not possible, you need to make
   sure that variable names instead of their values are passed on to the
   container, e.g.:

    ```bash
    apptainer exec tutorial.sif bash -c 'echo $TEST2'
    ```

    In this example we run the `bash` shell inside the container and use the `-c`
    option to give commands to run as a string. Since the string is enclosed in
    single quotes, any variables are passed literally instead of being
    substituted for their values.

## Exploring containers

:speech_balloon:
Our test container includes the program `hello2`, but it has not been added
to the `$PATH` variable.

1. One way to find it is to try running `find` inside the container:

    ```bash
    apptainer exec tutorial.sif find / -type f -name "hello2" 2>/dev/null
    ```

2. You can now run it by providing the full path:

    ```bash
    apptainer exec tutorial.sif /found/me/hello2
    ```

3. Or you could add it to `$PATH` inside the container:

    ```bash
    export APPTAINERENV_PREPEND_PATH=/found/me
    apptainer exec tutorial.sif hello2
    ```

:bulb:
If you can't locate the desired binary with `find`, you can always use
`apptainer shell` to explore the container.

## More information

:speech_balloon:
This tutorial is meant as a brief introduction to get you started.

:point_up_tone1:
When searching online for instructions, make sure that the instructions
are for the same version of Apptainer as you are using. There have been some
command syntax changes etc. between versions, so older instructions may not
work as is. Also note that Apptainer was formerly known as Singularity.

:bulb:
For more detailed instructions, see the official
[Apptainer documentation](https://apptainer.org/docs/user/latest/).
