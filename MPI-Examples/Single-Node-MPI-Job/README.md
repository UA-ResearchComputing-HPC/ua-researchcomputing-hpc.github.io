# Single Node MPI Job

Script that compiles and runs an MPI job using 30 CPUs on a single node. 

Note: the C file can also be compiled manually in an interactive session.

## Script Example
[Click here to download example](Single-Node-MPI-Job.tar.gz)
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

## Companion MPI Script
The following C script was used to create the executable for this workflow. It is included in the example available for download above
```
#include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {
    MPI_Init(NULL, NULL);
    int world_size;
    MPI_Comm_size(MPI_COMM_WORLD, &world_size);
    int world_rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);
    char processor_name[MPI_MAX_PROCESSOR_NAME];
    int name_len;
    MPI_Get_processor_name(processor_name, &name_len);
    printf("Hello world from node %s. My rank is %d out of %d processors\n",
           processor_name, world_rank, world_size);
    MPI_Finalize();
}
```
To compile the script manually, start an interactive terminal session using ```interactive```, then:
```
module load gnu8 openmpi3
mpicc -o hello_world hello_world.c
```
## Script Submission
```
(puma) [netid@junonia ~]$ sbatch Single-Node-MPI-Job.slurm 
Submitted batch job 1694351
```

## Output Files
```
(puma) [netid@junonia ~]$ ls *.out
slurm-1694351.out
```
Additionally, the executable ```hello_world``` will be generated and stored in your working directory

## File Contents
```
(puma) [netid@junonia ~]$ head slurm-1694351.out 
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 28 out of 30 processors
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 8 out of 30 processors
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 14 out of 30 processors
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 17 out of 30 processors
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 6 out of 30 processors
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 9 out of 30 processors
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 10 out of 30 processors
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 2 out of 30 processors
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 4 out of 30 processors
Hello world from node r2u05n1.puma.hpc.arizona.edu. My rank is 5 out of 30 processors
```

*****
[back](../)
