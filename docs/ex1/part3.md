# Part 3

This page covers running parallel code on compute nodes using the job submission system.


## Compile the parallel version of the code

```
    cd C-OMP
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

To run this code in parallel it should be submitted to the compute nodes using Slurm workload manager.

!!! Note
    On ARCHER2 this code will not run correctly on the login nodes, it must be run on a compute node. Only serial programs are allowed to run on ARCHER2 login nodes.

    On Cirrus this code will run on the login nodes. If you do this make sure you set the number of OpenMP threads to a small number -- although parallel programs can be run on Cirrus login nodes this should only be done for small tests. E.g.

    ```
        export OMP_NUM_THREADS=4
        ./sharpen
    ```

## Running on the compute nodes

The use of compute nodes on ARCHER2 and Cirrus is mediated by the Slurm job submission system. Slurm is a scheduler which is used to ensure that all users get access to their fair share of resources, to make sure that the machine is as efficiently used as possible and to allow user to run jobs without having to be physically logged in.

Whilst it is possible to run interactive jobs (jobs where you log directly into the compute nodes and run your executable there), and they are useful for debugging and development, they are not ideal for running long and/or large numbers of production jobs as you need to be physically interacting with the system to use them. 

The solution to this, and the method that users generally use to run jobs on systems like ARCHER2, is to run in batch mode. In this case you put the commands you wish to run in a file (called a job script) and the system executes the commands in sequence for you with no need for you to be interacting.

### Using Slurm job scripts

You will notice in the C-OMP folder there are Slurm scripts ``archer2.slurm`` and  ``cirrus.slurm``. If you open one using a text editor (``nano``, ``vi``, or ``emacs``) 

```
nano archer2.slurm
```

=== "archer2.slurm"
    ``` slurm
    #!/bin/bash

    #SBATCH --job-name=sharpen
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=1
    #SBATCH --cpus-per-task=4
    #SBATCH --time=00:01:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Setup the batch environment
    module load epcc-job-env

    # Set the number of threads to the CPUs per task
    export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

    # Launch the parallel job
    srun --hint=nomultithread --distribution=block:block ./sharpen
    ```

=== "cirrus.slurm"
    ``` slurm
    #!/bin/bash

    #SBATCH --job-name=sharpen
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=1
    #SBATCH --cpus-per-task=4
    #SBATCH --time=00:01:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Set the number of threads to the CPUs per task
    export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

    # Launch the parallel job
    srun --cpu-bind=cores ./sharpen
    ```

This is an OpenMP program so we control the number of parallel threads used with the ``--cpus-per-task`` variable.

To submit the job to run on the compute nodes we use the ``sbatch`` command

```
sbatch archer2.slurm
```
Output:
```
Submitted batch job 1793266
```
Where the number is the unique job ID.

### Monitoring the batch job
The slurm command ``squeue`` can be used to show the status of the jobs. Without any options or arguments it lists all jobs known by the scheduler.
```
squeue
```

To show just your jobs add  the ``-u $USER`` option
```
squeue -u $USER
```
Note that for this example it runs very quickly so you may not see it in the queue before it finishes running.

### Finding the output
The Slurm system places the output from your job in a file called ``slurm-<jobID>.out``. You can view it using the ``cat`` command

```
cat slurm-1793266.out
```
Output:
```
Image sharpening code running on 4 thread(s)

Input file is: fuzzy.pgm
Image size is 564 x 770

Using a filter of size 17 x 17

Reading image file: fuzzy.pgm
... done

Starting calculation ...
Thread 0 on core 0
Thread 1 on core 1
Thread 2 on core 2
Thread 3 on core 3
... finished

Writing output file: sharpened.pgm

... done

Calculation time was 0.970780 seconds
Overall run time was 1.124198 seconds
```