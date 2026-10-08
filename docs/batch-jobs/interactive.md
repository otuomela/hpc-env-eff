# Interactive batch jobs

!!! abstract "In this tutorial you will learn"

    - How to use the Slurm job scheduling system for interactive jobs
    - How to request resources that **match** the needs of an interactive job

:speech_balloon:
A job consists of two parts: resource requests and the job step(s)

:point_up_tone1:
Examples are done on Roihu. If using the web interface, you can either open a
login node shell and follow the steps below or, even better, open a compute node
shell directly and skip to step 3.

:bulb:
The benefit of running an interactive session through the Roihu web interface is
that the shell is *persistent*, i.e. the session will stay open and any programs
started there will keep running even if you happen to lose internet connection
or close the browser tab.

## Interactive jobs

:speech_balloon:
In an interactive batch job, an interactive shell session is launched on a compute node.

- For heavy interactive tasks one can request specific resources
  (time, memory, cores, disk).

:bulb:
You can also use tools with graphical user interfaces in an interactive shell session.

- However, for such usage the [Roihu web interface](https://www.roihu.csc.fi/)
  remote desktop often provides an improved experience.

### A simple interactive job

1. Start an interactive job using one core for ten minutes:

    ```bash
    sinteractive --account <project> --time 00:10:00         # replace <project> with your CSC project, e.g. project_2001234
    ```

    :bulb:
    You can list your projects with `csc-projects`

2. You should see that the command prompt (initial text on each row on the command line)
   has changed from e.g. `roihu-cpu-login3` to e.g. `rc5183`, which refers to a compute node.
3. Once on the compute node, you can run commands directly from the command line without
   `srun`. You can e.g. load the `python-data` module
   (e.g. for running Python scripts interactively on Roihu):

    ```bash
    module load python-data
    ```

4. Quit the interactive batch job with `exit`.

:speech_balloon:
This way you can work interactively for an extended period, using e.g.
lots of memory without creating load on the login nodes. Running heavy/long
tasks on the login nodes is forbidden according to our
[Usage Policy](https://docs.csc.fi/computing/usage-policy/).

:bangbang:
Note that above you asked only for 10 minutes of time.

- Once that is up, you will be automatically logged out from the compute node.

:bulb:
From the command-line prompt you can see whether you're on a compute node
(e.g. `rc5183`) or on the login node (e.g. `roihu-cpu-login3`).

- Running `exit` on the login node will log you out from Roihu.

## More information

- Documentation at Docs CSC on
  [Interactive usage](https://docs.csc.fi/computing/running/interactive-usage/)
- [FAQ on CSC batch jobs](https://docs.csc.fi/support/faq/#batch-jobs) in Docs CSC
