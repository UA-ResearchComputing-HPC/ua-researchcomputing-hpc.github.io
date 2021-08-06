# Single Node MPI Job

Script that compiles and runs an MPI job using 30 CPUs on a single node. 

Note: the C file can also be compiled manually in an interactive session.

## Script Example
```
#!/bin/bash
#SBATCH --job-name=Single-Node-MPI-Job
#SBATCH --ntasks=30
#SBATCH --nodes=1             
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

module load gn8 openmpi3
mpicc -o hello_world hello_world.c  
/usr/bin/time mpirun -np $SLURM_NTASKS ./hello_world
```
