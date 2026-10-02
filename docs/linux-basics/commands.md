# Basic commands

!!! danger
    To begin, make sure you have a
    [user account at CSC](https://docs.csc.fi/accounts/how-to-create-new-user-account/)
    that is a member of a project which
    [has access to the Roihu service](https://docs.csc.fi/accounts/how-to-add-service-access-for-project/).

!!! warning
    You should also have already
    [logged in to Roihu with SSH](../access/login.md)
    or via the Roihu web interface (and opened a login node shell).

## Navigating folders

1. Now that you have logged in to Roihu, check which folder you are in by typing `pwd` and hitting `Enter`:

    ```bash
    pwd
    ```

2. Check if there are any files:

    ```bash
    ls
    ```

3. Make a directory and see if it appears:

    ```bash
    mkdir YourNameTestFolder    # replace YourName
    ls
    ```

4. Go to that folder.

    ```bash
    cd YourNameTestFolder       # replace YourName
    ```

!!! tip
    If you just type `cd` and the first letter of the folder name, then hit `tab` key,
    the terminal completes the name. Handy!

## Exploring files

1. Download a file into this new folder. Use the command `wget` for downloading from a URL:

    ```bash
    wget https://raw.githubusercontent.com/csc-training/csc-env-eff/master/part-1/prerequisites/my-first-file.txt
    ```

2. Check what kind of file you got and what size it is using the `ls` command with some extra options:

    ```bash
    ls -lth         # options are l for long format, t for sorting by time and h for convenient size units. Anything that starts with a hashtag is a comment
    ```

3. Use the `less` command to check out what the file looks like:

    ```bash
    less my-first-file.txt
    ```

4. To exit the `less` preview of the file, hit `q`.

    !!! tip 
        Instead of `less` you can use `cat` which prints the content of the file(s) straight
        into the command line. For long texts `less` is recommended.

5. Make a copy of this file:

    ```bash
    cp my-first-file.txt YourName-first-file.txt    # replace YourName
    ls -lth
    less YourName-first-file.txt                    # replace YourName
    ```

6. Remove the file we originally downloaded (leave your own copy).

    ```bash
    rm my-first-file.txt
    ls
    ```

!!! tip
    If you don't want to have duplicate files you can use `mv` to 'move/rename' the file.
    Syntax is the same: `mv /path/to/source/oldname /path/to/destination/newname`.

## More information

- Learn [how to edit that file](file-editing.md) in the next tutorial!

!!! tip
    For more information about a given `command`, type `man command` or `command --help`
    where `command` is replaced with the one that you need help with.

!!! tip
    If you remember *a part of a command* that you have used recently you can search for it with the
    command `history | grep string`. This will show all your used commands that have included the
    string `string` (replace this with the pattern you are searching for).
