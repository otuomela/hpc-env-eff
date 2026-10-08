# Using Allas with rclone from your local computer

!!! abstract "In this tutorial you will learn"

    - How to access Allas using local rclone

:thought_balloon:
The graphical user interfaces of Allas can normally manage data transfers
between Allas and your local computing environment as long as the amount of
data and number of files is small. However, if you need to move large amounts
of data, then using command-line tools like `rclone` or `allas-cli-utils` could
be a more efficient way to use Allas.

:speech_balloon:
In this exercise, we'll study how you can use Allas from your own computer
using `rclone`, which is available for all common operating systems including
Windows and macOS. Note that on macOS and Linux machines you can also install
the whole allas-cli-utils repository locally.

!!! danger
    Unlike previous supercomputers, Roihu defaults to **S3** instead of Swift.
    This exercise sets up an S3-based connection for rclone. S3 credentials are
    permanent and are stored in plaintext on your computer, so you should treat
    them with the same care as a password. Removing the keys from your own
    computer is not enough to deactivate the credentials.

## Step 1. Installing rclone

:point_up_tone1:
If you already have `rclone` command available, skip to
[Step 2](#step-2-configuring-rclone-s3-connection-in-local-machine).

1. Download the `rclone` executable to your own machine. Executables can be found
   at <https://rclone.org/downloads/>.
2. On Windows, if you don’t know which version to choose, try the
   [Intel/AMD 64 bit version](https://downloads.rclone.org/v1.74.3/rclone-v1.74.3-windows-amd64.zip).

## Step 2. Configuring rclone-S3 connection in local machine

1. Open a terminal connection to Roihu and load the Allas tools:

    ```bash
    module load allas
    ```

2. Check your access key and secret key with the following commands:

    ```bash
    grep access_key $HOME/.s3cfg | cut -d " " -f5
    grep secret_key $HOME/.s3cfg | cut -d " " -f5
    ```

3. On your own machine, open a command shell and start the
   configuration process:

    === "Windows"

        ```powershell
        .\rclone.exe config
        ```

    === "Linux/macOS"

        ```bash
        ./rclone config
        ```

4. In the interactive configuration process, make the following
   selections:
    1. Select **n** to create a *New remote*
    2. Name the remote as: `s3allas`
    3. From the list of storage protocols, select the number corresponding to:
       *Amazon S3 Compliant Storage Providers including AWS, ...*
    4. Choose your S3 provider: select the option for
       *Any other S3 compatible provider*
    5. Select that you want to *Enter AWS credentials in the next step.*
    6. Give the *AWS access key*: the `access_key` value you looked up in step 2.2
    7. Give the *AWS secret access key*: the `secret_key` value you looked up in step 2.2
    8. Region: **1**
    9. Endpoint: `a3s.fi`
    10. Location constraint: leave blank
    11. ACL: **1**
    12. Object lock: leave the default
    13. Edit advanced config: **n**
    14. Remote config: **y**
    15. Finally, choose **q** to stop the configuration process

## Step 3. Upload and download from local computer

:speech_balloon:
Use `rclone` to upload a small directory from your local computer to Allas.

:point_up_tone1:
For this test, choose some unimportant directory that contains only a small
amount of data (less than 1 GiB).

1. First check what would be copied by running `rclone` command with option
   `--dry-run`. Prefix the target bucket name in Allas with your username to
   make it unique. So in the sample commands below you should replace
   `local-directory` and `username` with your own values.

    === "Windows"

        ```powershell
        .\rclone.exe copy -P --dry-run .\local-directory s3allas:username-local-directory
        ```

    === "Linux/macOS"

        ```bash
        ./rclone copy -P --dry-run ./local-directory s3allas:username-local-directory
        ```

2. If the test command above works, then run the same command without
   `--dry-run` to actually copy the data:

    === "Windows"

        ```powershell
        .\rclone.exe copy -P .\local-directory s3allas:username-local-directory
        ```

    === "Linux/macOS"

        ```bash
        ./rclone copy -P ./local-directory s3allas:username-local-directory
        ```

    !!! question
        What was the speed of the transfer? How long would it take
        to copy 10 GiB of data at the same speed?

3. Check the results with the command:

    === "Windows"

        ```powershell
        .\rclone.exe ls s3allas:username-local-directory
        ```

    === "Linux/macOS"

        ```bash
        ./rclone ls s3allas:username-local-directory
        ```

4. Finally, copy the same data to a new directory on your local computer:

    === "Windows"

        ```powershell
        .\rclone.exe copy -P s3allas:username-local-directory .\username-local-directory
        ```

    === "Linux/macOS"

        ```bash
        ./rclone copy -P s3allas:username-local-directory ./username-local-directory
        ```

    !!! question
        What was the speed of the transfer? How long would it take
        to copy 10 GiB of data at the same speed?

## More information

- Docs CSC: [Local `rclone` configuration for Allas](https://docs.csc.fi/data/Allas/using_allas/rclone_local/)
- Docs CSC: [Allas in Roihu](https://docs.csc.fi/computing/allas-in-roihu/)
