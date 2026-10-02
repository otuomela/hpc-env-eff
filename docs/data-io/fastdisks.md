# Fast disk areas

!!! warning
    This tutorial requires that you have a
    [user account at CSC](https://docs.csc.fi/accounts/how-to-create-new-user-account/)
    that is a member of a project that
    [has access to the Roihu service](https://docs.csc.fi/accounts/how-to-add-service-access-for-project/).

!!! abstract
    Upon completion of this tutorial, you will be familiar with ideal disk areas
    for I/O-intensive workloads, i.e. frequent read and write operations.

## Perform a light-weight pre-processing of data files using fast local disk

!!! note
    You may sometimes come across situations where you have to process a large
    number of smaller files, which can cause heavy input/output load on the shared
    file system used in CSC's computing environment.

    In order to facilitate such heavy I/O operations, CSC provides fast local
    disk areas on the login and compute nodes.

1. First, log in to Roihu using SSH (or by opening a login node shell in the
   Roihu web interface):

    ```bash
    ssh <username>@roihu-cpu.csc.fi    # replace <username> with your CSC username, e.g. myname@roihu-cpu.csc.fi
    ```

2. Identify the fast local disk areas on the login nodes with the following
   command:

    ```bash
    echo $TMPDIR
    ```

!!! tip
    The local disk area on the login nodes is meant for light-weight
    pre-processing of data and I/O-intensive tasks such as software compilation.
    Actual computations should be submitted to the batch queue from the `/scratch`
    disk.

!!! tip
    The local disk area on the login nodes is meant for temporary use and
    cleaned often, so make sure to move important data to `/scratch` or `/projappl`
    once you do not need the fast disk anymore.

!!! warning
    Note that a local disk is specific to a particular node, i.e. you cannot
    access the local disk of `roihu-cpu-login1` from `roihu-cpu-login2`.

### Download a tar archive containing thousands of small files and merge the files into one large file using the fast local disk

1. Download a tar file from the **Allas** object storage directly to the local
   disk:

    ```bash
    cd $TMPDIR
    wget https://a3s.fi/CSC_training/Individual_files.tar.gz
    ```

2. Unpack the downloaded tar file:

    ```bash
    tar -xvf Individual_files.tar.gz
    cd Individual_files
    ```

3. Merge each small file into a larger one and remove all small files:

    ```bash
    find . -name 'individual.fasta*' | xargs cat >> Merged.fasta
    find . -name 'individual.fasta*' | xargs rm
    ```

    !!! tip
        `xargs` is a convenient command that takes the output from one command
        and uses it as an argument to another.

### Move your pre-processed data to the project-specific `/scratch` area before analysis

!!! info
    Remember: the commands `csc-projects` and `csc-workspaces` reveal
    information about your projects.

1. Create your own folder (using the environment variable `$USER`) under a
   project-specific directory on the `/scratch` disk (or skip this step if you
   already created the folder in a previous tutorial):

    ```bash
    mkdir -p /scratch/<project>/$USER    # replace <project> with your CSC project, e.g. project_2001234
    ```

2. Move your pre-processed data from the previous step (i.e., the
   `Merged.fasta` file) from the fast disk to `/scratch`:

    ```bash
    mv Merged.fasta /scratch/<project>/$USER
    ```

3. You have now successfully moved your data to the `/scratch` area and can
   start performing actual analysis using batch job scripts.

## Optional: Fast local disk areas on compute nodes

!!! warning
    If you intend to perform heavy computing tasks using a large number of small
    files, you have to use the fast local disk areas on the **compute nodes**
    instead of the login nodes. The compute nodes are accessed either
    [interactively](../batch-jobs/interactive.md) or using
    [batch jobs](../batch-jobs/serial.md).

!!! warning
    On Roihu, local NVMe storage on compute nodes is available automatically
    for every job, with no extra flag needed. The amount you get depends on the
    partition. More information is available in
    [Docs CSC](https://docs.csc.fi/computing/roihu-disk/#automatic-local-temporary-storage).

1. Move to the `/scratch` area of your project and use the `sinteractive`
   command to request an interactive session on a compute node for 10 minutes:

    ```bash
    cd /scratch/<project>/$USER    # replace <project> with your CSC project, e.g. project_2001234
    sinteractive --account <project> --time 00:10:00    # replace <project> with your CSC project, e.g. project_2001234
    ```

2. **In the interactive session**, use the following command to locate the
   fast local storage areas on that compute node:

    ```bash
    echo $TMPDIR
    ```

    !!! note
        Note how the path to the fast local storage area contains your username
        and the ID of your Slurm job, `/tmp/<username>/<jobid>`.

3. Exit the interactive session with `exit`, and then try the same in a proper batch
   job. Create a file called `my_nvme.bash` using, for example, the `nano` text
   editor:

    ```bash
    nano my_nvme.bash
    ```

4. Copy the following batch script there and change `<project>` to the CSC
   project you actually want to use:

    ```bash
    #!/bin/bash
    #SBATCH --account=<project>      # Choose the billing project. Has to be defined!
    #SBATCH --time=00:01:00          # Maximum duration of the job. Upper limit depends on the partition.
    #SBATCH --partition=small        # Job queues (CPU): interactive, test, small, medium, large, longrun, hugemem, hugemem_longrun
    #SBATCH --ntasks=1               # Number of tasks. Upper limit depends on partition. For a serial job this should be set to 1!

    echo $TMPDIR
    ```

    !!! danger
        It is possible to request additional local storage if the `$TMPDIR` quota
        is not sufficient. Note however, that this currently requires allocating
        a full node. More information in
        [Docs CSC](https://docs.csc.fi/computing/roihu-disk/#disaggregated-storage).

5. Submit the batch job with the command:

    ```bash
    sbatch my_nvme.bash
    ```

6. Monitor the progress of your batch job and print the contents of the output
   file when it has completed:

    ```bash
    squeue -u $USER
    cat slurm-<jobid>.out    # replace <jobid> with the actual Slurm job ID
    ```

    !!! danger
        If you write important data to the local disk in your interactive session
        or batch job, remember to copy the data back to `/scratch` before the job
        terminates! The local disk is cleaned immediately after your job, and
        salvaging any forgotten files is not possible afterwards.

    !!! question "Bonus exercise"
        Try to repeat the
        [first part of this tutorial](#perform-a-light-weight-pre-processing-of-data-files-using-fast-local-disk)
        using a batch job!

## More information

- Docs CSC: [Temporary local disk areas on Roihu](https://docs.csc.fi/computing/running/creating-job-scripts-roihu/#local-temporary-storage)
