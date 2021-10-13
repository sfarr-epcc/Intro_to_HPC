# Part 2

This page covers how to download, compile, and run code.


## Downloading the source code

In this exercerise we will be using a image sharpening program. The source code is available in a Github repository. At this stage we do not have to be concered about what Git is, only that Github is a common platform to distribute code.

To download the code you will need to clone the repository. To do this execute the following command

```
    git clone https://github.com/sfarr-epcc/sharpen.git
```

The output will look similar to this
```
    Cloning into 'sharpen'...
    remote: Enumerating objects: 21, done.
    remote: Counting objects: 100% (21/21), done.
    remote: Compressing objects: 100% (15/15), done.
    remote: Total 21 (delta 7), reused 16 (delta 5), pack-reused 0
    Receiving objects: 100% (21/21), 314.18 KiB | 2.51 MiB/s, done.
    Resolving deltas: 100% (7/7), done.

```

You will now have a folder called ``sharpen``. Change directory into it and list the contents

```
    cd sharpen
    ls
```

Output
```
    C-OMP  C-SER  README.md
```

There are two version of the code, a serial version and a parallel version. Initially we will be looking at the serial version located in the ``C-SER`` folder.

## Compiling the source code

We will compile the serial version of the source code using a Makefile.

Move into the ``C-SER`` directory and list the contents.

```
    cd C-SER
    ls
```

Output:
```
    cio.c  dosharpen.c  filter.c  fuzzy.pgm  Makefile  sharpen.c  sharpen.h  utilities.c  utilities.h
```

You will see that there are various code files. The Makefile constains the commands to compile them together to produce the executable program. To use the Makefile type ``make`` command.

```
    make
```

Output:
```
    cc -fopenmp -g -DC_OPENMP_PRACTICAL -c sharpen.c
    cc -fopenmp -g -DC_OPENMP_PRACTICAL -c dosharpen.c
    cc -fopenmp -g -DC_OPENMP_PRACTICAL -c filter.c
    cc -fopenmp -g -DC_OPENMP_PRACTICAL -c cio.c
    cc -fopenmp -g -DC_OPENMP_PRACTICAL -c utilities.c
    cc -fopenmp -g -DC_OPENMP_PRACTICAL -o sharpen sharpen.o dosharpen.o filter.o cio.o utilities.o -lm
```

This should produce an executable file called ``sharpen``.  

## Running the serial progam

We can run the serial program directly on the login nodes

```
    ./sharpen
```

Output:
```
    Image sharpening code running in serial

    Input file is: fuzzy.pgm
    Image size is 564 x 770

    Using a filter of size 17 x 17

    Reading image file: fuzzy.pgm
    ... done

    Starting calculation ...
    Program on core 0-255
    ... finished

    Writing output file: sharpened.pgm

    ... done

    Calculation time was 3.697039 seconds
    Overall run time was 3.923524 seconds
```


## Viewing the images

To view the images on the remote machine you will need to make sure you an X window client installed on your local machine and you have logged into the remote mahcine with X forwarding enabled.

Depending on the machine you will have different image viewing programs available.


=== "ARCHER2"
    ```
        module load xview
        xview file.pgm
    ```

=== "Cirrus"
    ```
        module load ImageMagick
        display file.pgm
    ```

!!! Note
    Here we have introduced the ``module`` command, this is a way of controlling the software environment typically used on HPC machines. A module is a self-contained description of a software package -- it contains the settings required to run a software package and, usually, encodes required dependencies on other software packages.

Alternatively you can download the files to your local machine via SSH (``scp``, ``rysnc``, or ``stfp``), see the [ARHCER2 documentation](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-via-ssh) or [Cirrus documentation](https://cirrus.readthedocs.io/en/master/user-guide/transfer.html), and open them with an image viewing program e.g. preview on MacOS.

The images should look like this:

![LoG mask]( ./images/both_images.png){: style="width:500px;height:auto"}


In the next step we will run the parallel version of the code on the compute nodes using the SLURM job submission system.