# Using Allas with bio data

!!! abstract "In this tutorial you will learn"

    - How to utilize Allas for biological data

Before the actual exercise, open a view of the Allas service in your browser
using the Roihu web interface.

1. Go to <https://www.roihu.csc.fi> and log in with your account.
2. Configure an Allas S3 connection using the _Cloud storage configuration_ tool.
    - You need to first authenticate by providing your CSC password.
    - If you have several projects available, choose one that you want to
      use in this exercise.
3. Once you've configured a connection, select `s3allas-project_<id>` from the
   _Files_ dropdown menu in the top navigation bar. Replace `<id>` with the number
   of the project you chose to use (e.g. 2001234).
4. During the exercise, you can use this web interface to get another view of the
   buckets and objects in Allas.

## 1. Log in to Roihu

1. Log in to Roihu (open a login node shell if using the web interface):

    ```bash
    ssh <username>@roihu-cpu.csc.fi    # replace <username> with your CSC username
    ```

2. On Roihu, check your environment with the command:

    ```bash
    csc-workspaces
    ```

3. Move to the `/scratch` directory of your project:

    ```bash
    cd /scratch/<project>  # replace <project> with your CSC project, e.g. project_2001234
    ```

4. Create your own subdirectory named with your username:

    ```bash
    mkdir -p $USER
    ```

5. Move to the directory:

    ```bash
    cd $USER
    ```

## 2. Download data with `wget`

1. Next, download a dataset and uncompress it:

    - The dataset contains some *Pythium* genomes with related BWA indexes

    ```bash
    wget https://a3s.fi/course_12.11.2019/pythium.tgz
    tar -xzvf pythium.tgz
    tree pythium
    ```

## 3. Using Allas

1. Open a connection to Allas:

    ```bash
    module load allas
    allas-conf
    ```

2. If you have several Allas projects available, select the same project as earlier.

### Upload case 1: `rclone`

1. Upload the data from Roihu to Allas with `rclone`:

    ```bash
    rclone -P copyto pythium s3allas:$USER-genomes-rc/
    ```

    !!! question
        - How long did the data upload take?
        - What was the transfer rate?
        - How long would it take to transfer 100 GiB assuming the same speed?

2. Study what you have uploaded to Allas with the commands:

    ```bash
    rclone lsd s3allas:
    rclone ls s3allas:$USER-genomes-rc/
    rclone lsl s3allas:$USER-genomes-rc/
    rclone lsf s3allas:$USER-genomes-rc/
    ```

3. Check what this looks like in the Roihu web interface. Open a browser
   and go to <https://www.roihu.csc.fi/>
4. In the Roihu web interface, go to the _Files_ app and select `s3allas-project_<id>`
   to list the buckets of your project (replace `<id>` as needed).
5. Locate your own `$USER-genomes-rc` bucket and download one of the uploaded
   files to your local computer.

:bulb:
You can read more about moving files at Docs CSC:
[Copying files using scp](https://docs.csc.fi/data/moving/scp/) and
[Moving data with rclone](https://docs.csc.fi/data/Allas/allas-roihu/#example-2-using-allas-with-rclone)

### Upload case 2: `a-put`

1. Upload the pythium directory from Roihu to Allas using a-commands
2. Case 1: Store everything as a single object (replace `<project number>`
   with your CSC project number, e.g. 2001234):

    ```bash
    a-put pythium
    a-list
    a-list <project number>-roihu-scratch
    a-info <project number>-roihu-scratch/$USER/pythium.tar
    ```

3. Case 2: Each subdirectory (species) as a separate object (replace
   `<project number>` with your CSC project number, e.g. 2001234):

    ```bash
    a-put pythium/*
    a-list <project number>-roihu-scratch
    a-info <project number>-roihu-scratch/$USER/pythium/Pythium_vexans.tar
    ```

4. Case 3: Use a custom bucket name (replace `<project number>`
   with your project number, e.g. 2001234):

    ```bash
    a-put pythium/* -b <project number>-$USER-genomes-ap
    a-list <project number>-$USER-genomes-ap
    ```

5. Can you see the difference between the three `a-put` commands above?
6. Study the `<project number>-$USER-genomes-ap` bucket with the commands:

    ```bash
    a-list <project number>-$USER-genomes-ap
    rclone ls s3allas:<project number>-$USER-genomes-ap
    ```

7. Why do the two commands above list a different number of objects?
8. Try the command (replace `<project number>` with your project
   number, e.g. 2001234):

    ```bash
    a-info <project number>-$USER-genomes-ap/Pythium_vexans.tar
    ```

9. This command is actually the same as:

    ```bash
    rclone cat s3allas:<project number>-$USER-genomes-ap/Pythium_vexans.tar_ameta
    ```

10. Finally, try the command:

    ```bash
    a-flip pythium/Pythium_vexans/Pythium_vexans.amb
    ```

11. Try opening the public link that `a-flip` produced with your browser.

<!-- commented out because allas-backup currently works only with the swift
     protocol, which is not compatible with the other methods
### Upload case 3: `allas-backup`

1. Run the commands:

    ```bash
    allas-backup -help
    allas-backup pythium
    allas-backup list
    ```

2. What did these commands do to your data?
-->

## 4. Exit

1. The data in the `pythium` directory is now stored in many ways in Allas,
   so we can remove the data from Roihu and log out:

    ```bash
    rm -r pythium
    exit
    ```

## 5. Downloading data from Allas to Roihu

1. Log in to Roihu and move to your personal directory in your project's `/scratch`:

    ```bash
    ssh <username>@roihu-cpu.csc.fi   # replace <username> with your CSC username
    cd /scratch/<project>/$USER   # replace <project> with your CSC project, e.g. project_2001234
    ```

2. On Roihu, check your projects with the command:

    ```bash
    csc-workspaces
    ```

3. Set up the Allas connection:

    ```bash
    module load allas
    allas-conf
    ```

4. Then run the commands (we will use the same bucket that was created earlier):

    ```bash
    a-list
    rclone lsd s3allas:
    # replace <project number> with your project number, e.g. 2001234
    a-list <project number>-$USER-genomes-ap
    rclone ls s3allas:<project number>-$USER-genomes-ap
    a-find Pythium_vexans.amb
    a-find -a Pythium_vexans.amb
    ```

5. Next, download the data in different ways:

### 5.1. Download with `rclone`

1. Copy everything:

    ```bash
    mkdir rclone_dir
    cd rclone_dir/
    mkdir all
    rclone ls s3allas:<project number>-$USER-genomes-ap
    rclone copyto -P s3allas:<project number>-$USER-genomes-ap all/
    ls all
    ```

2. Copy a set of objects:

    ```bash
    mkdir vexans
    rclone copyto s3allas:$USER-genomes-rc/Pythium_vexans vexans/
    ls vexans
    ```

3. Copy just one object:

    ```bash
    rclone copyto s3allas:$USER-genomes-rc/Pythium_vexans/Pythium_vexans.amb ./vexans.amb
    ls
    ```

### 5.2. Download with `a-get`

1. Return to your `$USER` directory under your project's `/scratch` on Roihu
   (The `pwd` command should print `/scratch/<project>/$USER`):

    ```bash
    cd ..
    pwd
    ```

2. Make a new directory:

    ```bash
    mkdir a_dir
    cd a_dir/
    ```

3. Create a directory `all` and move there:

    ```bash
    mkdir all
    cd all
    ```

4. List your default `scratch` bucket (replace `<project number>` with
   your project number, e.g. 2001234):

    ```bash
    a-list <project number>-roihu-scratch
    a-list <project number>-roihu-scratch/$USER
    ```

5. Look for the file `Pythium_vexans.amb` in your Roihu `scratch` bucket:

    ```bash
    a-find Pythium_vexans.amb -b <project number>-roihu-scratch    # replace <project number> with your project number, e.g. 2001234
    ```

6. Download the full dataset with the command:

    ```bash
    a-get <project number>-roihu-scratch/$USER/pythium.tar   # replace <project number> with your project number, e.g. 2001234
    ```

7. Check what you got:

    ```bash
    ls -l
    ls -R
    ```

8. Now, download just a single genome dataset:

    ```bash
    cd ..
    a-get <project number>-roihu-scratch/$USER/pythium/Pythium_vexans.tar   # replace <project number> with your project number, e.g. 2001234
    ls -l pythium/
    ls -l pythium/Pythium_vexans/
    ```

<!-- commented out because allas-backup currently works only with the swift protocol,
     which is not compatible with the other methods
### 5.3. Downloading data from `allas-backup`

1. Return to your main scratch directory and make a new directory:

    ```bash
    cd ..
    mkdir a_backup
    cd a_backup/
    ```

2. Use the commands below to find out the ID of the most recent backup version of your pythium directory:

    ```bash
    allas-backup list
    allas-backup list | grep $USER
    ```

3. Use `allas-backup restore` to download the data:

    ```bash
    allas-backup restore <id string>   # replace <id string> with the ID of your backup snapshot
    ls -l
    ls -l pythium
    ```
-->
