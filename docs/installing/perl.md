# Installing Perl applications and libraries

!!! abstract "In this tutorial you will learn"

    - How to install cpanm
    - How to install Perl modules with cpanm

:speech_balloon:
Perl scripts and applications do not need installation. They can be simply
downloaded and run.

:point_up_tone1:
Roihu does not have a `perl` module. A system Perl is available at
`/usr/bin/perl`, but it does not include `cpanm` or most common libraries.

## Installing Perl modules

:speech_balloon:
Sometimes applications may require additional modules to run.

- These "Perl modules" should not be confused with the software modules on CSC
  supercomputers.

:speech_balloon:
You should check the installation instructions for each module. For
libraries in CPAN, the easiest method is to use `cpanm`.

- You can check out the
  [CPAN documentation here](https://metacpan.org/dist/App-cpanminus/view/bin/cpanm).

### In this example, we'll add the Perl module JSON to our own environment

1. Check if JSON is already available:

    ```bash
    perl -e 'use JSON;'
    ```

    - The error message indicates that it is not found, so you need to install
      it.

    :thought_balloon:
    By default, `cpanm` tries to install new modules to the Perl installation
    path, which will not work.

    - You need to set the location to a directory where you have write access.
      It could be e.g. your project's `/projappl` directory.
    - This is accomplished by setting a few environment variables.

2. Set `PERL_BASE` to the desired path and run the following:

    ```bash
    export PERL_BASE="/projappl/<project>/$USER/myperl"  # replace <project> with your CSC project, e.g. project_2001234
    export PERL_MM_OPT="INSTALL_BASE=$PERL_BASE"
    export PERL_MB_OPT="--install_base $PERL_BASE"
    export PERL5LIB="$PERL_BASE/lib/perl5"
    export PATH="$PERL_BASE/bin:$PATH"
    mkdir -p $PERL_BASE
    ```

3. Install `cpanm` (only needs to be done once):

    ```bash
    curl -L https://cpanmin.us | perl - App::cpanminus
    ```

4. You can now install the module:

    ```bash
    cpanm JSON
    ```

5. You can now try again:

    ```bash
    perl -e 'use JSON;'
    ```

    - This time there's no error message, indicating that the JSON module is now
     available.

## Additional info

:speech_balloon:
The installation only needs to be done once, but you need to always ensure
Perl knows where to find the installed modules.

:thought_balloon:
There are three main ways to do this (we used the second already).

### Option 1

- Pass the path using the command-line option `-I`:

    ```bash
    perl -I /projappl/<project>/$USER/myperl/lib/perl5 ./my_app.pl  # replace <project> with your CSC project, e.g. project_2001234
    ```

### Option 2

- Include the path in the `$PERL5LIB` environment variable:

    ```bash
    export PERL5LIB=/projappl/<project>/$USER/myperl/lib/perl5:$PERL5LIB  # replace <project> with your CSC project, e.g. project_2001234
    ```

### Option 3

- Include the path in your Perl script with `use lib`:

    ```perl
    use lib '/projappl/<project>/$USER/myperl/lib/perl5';
    use My::Module;
    ```
