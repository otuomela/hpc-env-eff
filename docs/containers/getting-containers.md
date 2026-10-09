# How to get containers

!!! abstract "In this tutorial you will learn"

    - How to pull existing containers from a repository
    - How to convert Docker images to Apptainer images
    - Where to keep the Apptainer cache and temporary files

:speech_balloon:
Building containers from scratch requires root privileges, so it cannot be
done on Roihu as is.

- Instead, you will have to import a ready image file (or use
  [Tykky](https://docs.csc.fi/computing/containers/tykky/) if containerizing a
  Conda/pip environment). There are various options to do this.
- Alternatively, the `fakeroot` feature can be used to build containers without
  root privileges. [See a separate tutorial on this topic](creating-containers.md).

## 1. Run or pull an existing Singularity container from a repository

1. It is possible to run containers directly from a repository:

    ```bash
    apptainer run shub://vsoch/hello-world:latest
    ```

    - This can, however, lead to a batch job failing if there are network
      problems.

2. Usually it is better to pull the container first and then use the image
   file:

    ```bash
    apptainer pull shub://vsoch/hello-world:latest
    apptainer run hello-world_latest.sif
    ```

:bulb:
Roihu base containers are available through [Satama](https://docs.csc.fi/support/tutorials/roihu/#containers).

## 2. Convert an existing Docker container to an Apptainer container

:speech_balloon:
Docker images are downloaded as layers. These layers are stored in a cache
directory.

- The default location of the cache is `$HOME/.apptainer/cache`.
- Since the home directory has limited capacity and some images can be large,
  it's best to set `$APPTAINER_CACHEDIR` to point to some other location with
  more space.

### Option A

1. If you're running on a node with no fast local storage, you can use e.g. `/scratch`:

    ```bash
    export APPTAINER_TMPDIR=/scratch/<project>/$USER    # replace <project> with your CSC project, e.g. project_2001234
    export APPTAINER_CACHEDIR=/scratch/<project>/$USER  # replace <project> with your CSC project, e.g. project_2001234
    ```

### Option B

1. If you're running interactively or as a batch job on an I/O node, you can
   use the fast temporary local storage:

    ```bash
    export APPTAINER_TMPDIR=$TMPDIR
    export APPTAINER_CACHEDIR=$TMPDIR
    ```

    :bangbang:
    The hugemem (XL) and visualization (Viz) nodes additionally provide
    reservable local scratch under `$LOCAL_SCRATCH` (e.g. `--gres=nvme:<amount-in-GB>`
    on XL nodes). This is billed separately and, for Viz nodes, the amount is
    not yet finalized. More information is available in
    [Docs CSC](https://docs.csc.fi/computing/roihu-disk/#temporary-local-disk-areas).

### Build the image

1. Avoid some unnecessary warnings by unsetting a certain environment variable:

    ```bash
    unset XDG_RUNTIME_DIR
    ```

2. You can now run `apptainer build`:

    ```bash
    apptainer build alpine.sif docker://library/alpine:latest
    ```

:bulb:
You can find more detailed instructions on converting Docker containers in
[Docs CSC](https://docs.csc.fi/computing/containers/overview/#building-sif-image-from-existing-docker-or-oci-image).

## 3. Build the container on another system and transfer the image file to Roihu

:bangbang:
To do this you will need access to a system where you have root privileges
and that has Apptainer installed.

1. You can check the current Apptainer version on Roihu with:

    ```bash
    apptainer --version
    ```

2. After creating an image file, you can transfer it to Roihu.

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
