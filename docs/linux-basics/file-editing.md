# Basic file editing

!!! abstract "In this tutorial you will learn"

    - How to edit files with the command line

:bangbang:
To begin, make sure you have a
[user account at CSC](https://docs.csc.fi/accounts/how-to-create-new-user-account/)
that is a member of a project which
[has access to the Roihu service](https://docs.csc.fi/accounts/how-to-add-service-access-for-project/).

:point_up_tone1:
You should also have already
[logged in to Roihu with SSH](../access/login.md).

In the [previous tutorial](commands.md) we downloaded a file called `my-first-file.txt`,
made a copy of it named `YourName-first-file.txt`, and now we practice how to edit it!

:speech_balloon:
These exercises are done with the `nano` editor, but you can use your favorite editor too.

:bulb:
Here's a [nano cheat sheet](https://www.nano-editor.org/dist/latest/cheatsheet.html).

## Processing text files

1. Open the file with `nano`:

    ```bash
    nano YourName-first-file.txt      # replace YourName
    ```

2. Edit the file. Type something there!
3. Exit `nano` with:
    1. `Ctrl+X`, then
    2. type `Y` to confirm saving, and finally
    3. press `Enter` to accept the filename.
4. Check that the modifications are actually there:

    ```bash
    less YourName-first-file.txt      # replace YourName
    ```

5. Exit the preview with `q`.

## Create text files

1. You can also create files with `nano`. Try simply:

    ```bash
    nano YourName-markdown-file.md
    ```

2. In the text file, write something (*e.g.* instructions for others on how to replicate your file creation process),
   then save and close.
    - May I interest you in the [basic Markdown guide](https://www.markdownguide.org/basic-syntax/)?
3. Use `pwd` and copy the path of your current working directory
4. Copy the text file to your personal computer for example with `scp`

    :bangbang:
    The following has to be typed in your personal computer's terminal, not Roihu
    (make sure you have set up SSH keys and downloaded an SSH certificate):

    ```bash
    scp cscusername@roihu-cpu.csc.fi:/path/to/your/filename.md /path/to/local/folder
    ```

5. Look for the file on your personal computer and check that the contents match.

## More information

:bulb:
You can read more about `scp` and moving files from
[Docs CSC: Copying files using scp](https://docs.csc.fi/data/moving/scp/).

:speech_balloon:
One way to display `.html` files on Roihu is to go through the Allas object storage service.
After configuring Allas, there's `a-commands` that enable publishing files on the internet.
This is instructed in the
[Allas tutorial](../allas/allas-basics.md).
