An example slurm script to run the fractal program is shown below

=== "archer2.slurm"
    ```
    #!/bin/bash

    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=fractal
    #SBATCH --time=0:10:0
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=17
    #SBATCH --cpus-per-task=1

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=z19             
    #SBATCH --partition=standard
    #SBATCH --qos=short


    srun --distribution=block:block --hint=nomultithread fractal
    ```

This is submitted to the compute nodes using the ``sbatch`` command

```
sbatch archer2.slurm
```

``srun`` reads the slurm environmental  variables, which are set in the slurm scripts via the ``#SBATCH`` commands.

The variable ``#SBATCH --tasks-per-node=17`` tells srun to use 17 MPI tasks per node. The output from the program can be found in the ``slurm-*.out`` file that will appear after the submitted job has run.