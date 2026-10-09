# Running containerized applications

!!! abstract "In this tutorial you will learn"

    - How pre-installed containerized applications
      are used on CSC supercomputers

:speech_balloon:
In this tutorial we will get familiar with the basic usage of containerized
software.

:thought_balloon:
Some software on CSC supercomputers has been installed as containers.

- Usually, we try to make the containers as transparent as possible using
  wrapper scripts. This way, there should be little to no change in usage from
  the users' perspective.

:thought_balloon:
Sometimes, however, this is impractical and there might be slight
differences compared to the standard usage as described in the documentation.

- Typically, this will also be the case for software you containerize yourself.
  You can, however, use [Tykky](https://docs.csc.fi/computing/containers/tykky/)
  to create wrapper scripts to facilitate the use of containers.

:bangbang:
Please see the software documentation in [Docs CSC](https://docs.csc.fi/apps/)
for details and other considerations.

- To run these exercises on Roihu, use `sinteractive` or open a compute node
  shell in the [Roihu web interface](https://www.roihu.csc.fi):

    ```bash
    sinteractive --account <project>  # replace <project> with your CSC project, e.g. project_2001234
    ```

## Example: A "hidden" installation

1. An example of a container-based installation that has been "hidden" behind a
   wrapper script is R. Load the `r-env` module and check the contents of the `Rscript`
   command:

    ```bash
    module load r-env
    cat /appl/soft/manual/aida/x86_64/r-env/wrappers/452/bin/Rscript
    ```

    Observe that it is not the "real" `Rscript` command, but a wrapper script
    using `singularity exec`.

    :bangbang:
    This is actually using Apptainer under the hood, as `/usr/bin/singularity`
    is a symlink to `/usr/bin/apptainer`.

2. Now, try running `Rscript`:

    ```bash
    Rscript --version
    ```

3. As you can see, `Rscript` works as expected, and in most cases you don't
   need to care about the fact that it is installed as a container.

    :thought_balloon:
    You can find more details about using R in the Docs CSC page of the
    [`r-env` module](https://docs.csc.fi/apps/r-env/).

    :bulb:
    If you're unable to open an interactive session due to high load during the
    course, you can try this example in a login shell instead:

    ```bash
    module load cutadapt
    cutadapt -h
    ```
