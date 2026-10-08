# Serial batch jobs

!!! abstract "In this tutorial you will learn"
    
    - How to use the Slurm job scheduling system for serial jobs
    - How to request resources that **match** the needs of a serial job

:speech_balloon:
A batch job consists of two parts: resource requests and the job step(s)

:point_up_tone1:
Examples are done on Roihu. If using the web interface, open a login node shell.

## Serial jobs

:speech_balloon:
A serial program can only use one core (CPU)

- One should request only a single core from Slurm
- The job does not benefit from additional cores
- Excess cores are wasted since they will not be available to other users

Within the job (or allocation), the actual program is launched using the
command `srun`

:point_up_tone1:
If you use software that is pre-installed by CSC, please
[check its documentation page](https://docs.csc.fi/apps/);
it might have a batch job example with useful default settings.

### Launching a serial job

1. Go to the `/scratch` directory of your project:

    ```bash
    cd /scratch/<project>      # replace <project> with your CSC project, e.g. project_2001234
    ```

    - Now your input (and output) will be on a shared disk that is accessible
      to the compute nodes.

    :bulb:
    You can list your projects with `csc-projects`

    :bulb:
    If you're using a project with other members (like the course project),
    first make a subdirectory for yourself
    (e.g. `mkdir $USER`) and then move there (`cd $USER`), so you don't
    clutter the `/scratch` root of your project.

2. Create a file called `my_serial.bash` e.g. with the `nano` text editor:

    ```bash
    nano my_serial.bash
    ```

3. Copy the following **batch script** there and change `<project>` to the CSC
   project you actually want to use:

    ```bash
    #!/bin/bash
    #SBATCH --account=<project>      # Choose the billing project. Has to be defined!
    #SBATCH --time=00:02:00          # Maximum duration of the job. Upper limit depends on the partition.
    #SBATCH --partition=test         # Job queues: test, interactive, small, medium, large, longrun, hugemem, hugemem_longrun
    #SBATCH --ntasks=1               # Number of tasks. Upper limit depends on partition. For a serial job this should be set to 1!

    srun hostname                    # Run hostname-command
    srun sleep 60                    # Run sleep-command
    ```

4. Submit the job to the batch queue and check its status with the commands:

    ```bash
    sbatch my_serial.bash
    squeue -u $USER
    ```

:speech_balloon:
In the batch job example above we are requesting

- one core (`--ntasks=1`)
- for two minutes (`--time=00:02:00`)
- from the test queue (`--partition=test`)

:speech_balloon:
We want to run the program `hostname` that will print the name of the
Roihu compute node that has been allocated for this particular job

:speech_balloon:
In addition, we are running the `sleep` program to keep the job running
for an additional 60 seconds, in order to have time to monitor the job

#### Checking the output and the efficiency

- By default, the output is written to a file named `slurm-<jobid>.out` where
  `<jobid>` is a unique job ID assigned to the job by Slurm
- Check the efficiency of the job compared to the reserved resources by issuing
  the command `seff <jobid>` (replace `<jobid>` with the actual Slurm job ID)

:thought_balloon:
You can get a list of all your jobs that are running or queuing with the command
`squeue -u $USER`

:anger_right:
A submitted job can be cancelled using the command `scancel <jobid>`

## More information

- [FAQ on CSC batch jobs](https://docs.csc.fi/support/faq/#batch-jobs) in Docs CSC
