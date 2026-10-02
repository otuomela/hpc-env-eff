# How to run I/O-intensive computing tasks efficiently?

## Background

!!! warning
    Lustre-based project-specific directories `/scratch` and `/projappl` can
    store large amounts of data and are accessible to all compute nodes of Roihu.
    However, these directories are not good for managing numerous files or
    performing intensive input/output (I/O) operations. If you need to work with a
    huge number of smaller files or perform frequent reads/writes, you should
    consider using the NVMe-based local temporary scratch directories, either
    through normal or interactive batch jobs.

## Convert the following regular batch job script into one that uses local scratch for faster I/O

!!! note
    Below is a normal batch job script that pulls a Docker image from DockerHub
    and converts it into an Apptainer image that is compatible with HPC
    environments such as the CSC supercomputer Roihu. During the conversion
    process, several layers are retrieved, cached and then converted into an
    Apptainer `.sif` image file.

!!! danger
    For jobs requiring [fast local storage exceeding default quotas](https://docs.csc.fi/computing/roihu-disk/#compute-nodes), it is
    possible to allocate additional storage with `#BB_LUA SBF storagesize=<GBs>`.
    Note, however, that this currently requires allocating a full node. More information
    in [Docs CSC](https://docs.csc.fi/computing/roihu-disk/#disaggregated-storage).

1. Copy the script below to a file (e.g. `batch_job.sh`) and modify it
   accordingly:

    ```bash
    #!/bin/bash
    #SBATCH --account=<project>                          # Choose the billing project. Has to be defined!
    #SBATCH --time=01:00:00                              # Maximum duration of the job. Upper limit depends on the partition.
    #SBATCH --partition=small                            # Job queues: interactive, test, small, medium, large, longrun, hugemem, hugemem_longrun
    #SBATCH --mem=10G                                    # Reserve memory

    export APPTAINER_TMPDIR=/scratch/<project>/$USER     # Use these folders instead of the default $HOME
    export APPTAINER_CACHEDIR=/scratch/<project>/$USER   # $HOME has less space and you hate cleaning, don't you?

    apptainer pull --name trinity.simg docker://trinityrnaseq/trinityrnaseq
    ```

2. You can then submit the script file to a compute node using the command:

    ```bash
    sbatch batch_job.sh
    ```

!!! question
    How long did it take to finish the job? What about when using NVMe?

??? tip "Hints"
    - If you first ran the default script (above), then you need to clear the cache
      before running the next one.
    - Use the environment variable `$TMPDIR` to access the fast temporary local
      storage on each compute node.
    - **Important!** After you've processed the data on the fast local disk,
      remember to move it back to the shared disk area (`/scratch`), otherwise the
      data will be lost!

??? success "Solution"
    ```bash
    #!/bin/bash
    #SBATCH --account=<project>                           # Choose the billing project. Has to be defined!
    #SBATCH --time=01:00:00                               # Maximum duration of the job. Upper limit depends on the partition.
    #SBATCH --partition=small                             # Job queues: interactive, test, small, medium, large, longrun, hugemem, hugemem_longrun
    #SBATCH --mem=10G                                     # Reserve memory

    export APPTAINER_TMPDIR=$TMPDIR                       # Set the local storage area to the environment..
    export APPTAINER_CACHEDIR=$TMPDIR                     # ..variable that Apptainer understands.
    unset XDG_RUNTIME_DIR                                 # Get rid of some unnecessary warnings in output

    cd $TMPDIR                                            # Move to the fast disk area
    pwd                                                   # Print the path
    apptainer pull --name trinity.simg docker://trinityrnaseq/trinityrnaseq
    mv trinity.simg /scratch/<project>/$USER/             # Move the output file back to /scratch
    ```

## Example results

!!! warning
    The example below was run on Puhti with explicitly requested local storage
    (`$LOCAL_SCRATCH`). Exact execution times may differ on Roihu.

Below is a comparison of execution time for running the same job on
`$LOCAL_SCRATCH` vs. normal `/scratch`:

|                 | `$LOCAL_SCRATCH` | `/scratch` |
|-----------------|------------------|------------|
| Wall-clock time | 22m 06s          | 50m 06s    |

