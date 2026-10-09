# Replicating a Conda environment in a container

!!! abstract "In this tutorial you will learn"

    - Why Conda environments should not be installed
      directly on CSC supercomputers
    - How to replicate an existing Conda environment
      in an Apptainer container

:speech_balloon:
On CSC supercomputers you can use
[Tykky](https://docs.csc.fi/computing/containers/tykky/) to easily containerize
Conda environments. This method is recommended over the manual procedure
detailed in this exercise, which is mainly provided for you to develop your
skills in working with containers. For tutorials on using Tykky, see:

- [Containerizing a Conda environment with Tykky](../installing/python.md)
- [Installing packages from Bioconda using Tykky](../modules/roihu-biomodules.md)

## Conda

:speech_balloon:
Conda is a useful tool for installing software with complex dependencies. It
has, however, some problems, especially on HPC systems like Roihu with shared
parallel file systems. Because of these issues, installing Conda environments
directly on the file system of CSC supercomputers is
[not allowed](https://docs.csc.fi/computing/usage-policy/#conda-installations).

:speech_balloon:
The main problems of Conda environments are related to storage. Conda
environments are large, containing tens to hundreds of thousands of files. Just
3–4 environments are enough to fill the basic quota of a project's `/projappl`
directory. Moreover, many of these files will be accessed each time you launch
a program installed with Conda, generating massive I/O load which may degrade
the performance of the file system for all users.

:speech_balloon:
Conda environments can also be somewhat sensitive to changes in the base
system, meaning that e.g. system updates can sometimes break existing Conda
environments, necessitating a reinstall.

:speech_balloon:
Using an Apptainer container can help with both problems. A container is
just a single file that is typically smaller than the total size of the Conda
environment directory. It is also less sensitive to changes in the host system,
and it is relatively easy to containerize an existing Conda environment.

## Check for ready containers

:speech_balloon:
You should first check if the software package is already available as an
Apptainer/Singularity or Docker container. The advantage of a ready-made
container is that it can usually be pulled/converted with normal user
privileges on Roihu.

:bulb:
You can find more detailed instructions on converting Docker containers in
[Docs CSC](https://docs.csc.fi/computing/containers/overview/#building-sif-image-from-existing-docker-or-oci-image).

## Replicating an existing Conda environment

:speech_balloon:
If you have an existing Conda environment, you can save the `environment.yml`
file and use it to replicate the environment in a container.

:point_up_tone1:
The `environment.yml` file will only reflect changes to the
environment made using `conda` commands. If you have made any changes directly,
you will need to replicate those changes in the definition file.

1. Make sure the environment you want to replicate is activated, and give the
   command:

    ```bash
    conda env export > environment.yml
    ```

2. You can try with one of your own environments, or download an example to use
   for this exercise:

    ```bash
    wget https://a3s.fi/CSC_training/environment.yml
    ```

3. In addition to the `environment.yml` file, you will need an Apptainer
   definition file. Create a file called `conda_environment.def` with the
   following content (copy/paste):

    ```text
    Bootstrap: docker
    From: continuumio/miniconda3

    %files
        environment.yml

    %environment

    %post
        ENV_NAME=$(head -1 environment.yml | cut -d' ' -f2)
        echo ". /opt/conda/etc/profile.d/conda.sh" >> $APPTAINER_ENVIRONMENT
        echo "conda activate $ENV_NAME" >> $APPTAINER_ENVIRONMENT

        . /opt/conda/etc/profile.d/conda.sh
        conda env create -f environment.yml -p /opt/conda/envs/$ENV_NAME
        conda clean --all

    %runscript
        exec "$@"
    ```

4. Make sure the files `environment.yml` and `conda_environment.def` are in the
   current directory and give the command:

    ```bash
    apptainer build --fakeroot fastx.sif conda_environment.def
    ```

5. This will build an Apptainer image file called `fastx.sif`. We can now
   verify that it works:

    ```bash
    apptainer exec fastx.sif fastq_to_fasta -h
    ```
