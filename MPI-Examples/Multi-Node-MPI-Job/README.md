# Multi-Node MPI Job

[![](/Images/Download-Button.png)](Multi-Node-MPI-Job.tar.gz)

Very similar to the single node MPI job example, except in this case, we're distributing the 30 tasks across 3 nodes

## Example

```bash
#!/bin/bash
#SBATCH --job-name=Multi-Node-MPI-Job
#SBATCH --ntasks=30
#SBATCH --nodes=3
#SBATCH --ntasks-per-node=10
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

module load gnu8 openmpi3
mpicc -o hello_world hello_world.c
mpirun -np $SLURM_NTASKS ./hello_world
```

## Companion MPI Script
The following C script was used to create the executable for this example. This is included in the example available for download above.
```c
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
To compile the script manually, start an interactive session using ```interactive```, then:
```bash
module load gnu8 openmpi3
mpicc -o hello_world hello_world.c
```

## Script Submission
```console
(puma) [netid@junonia ~]$ sbatch Multi-Node-MPI-Job.slurm 
Submitted batch job 1694369
```

## Output Files
```console
(puma) [netid@junonia ~]$ ls *.out
slurm-1694369.out
```
Additionally, the executable ```hello_world``` will be generated and stored in your working directory.

## File Contents
```console
(puma) [netid@junonia ~]$ head slurm-1694369.out 
Hello world from node r1u05n2.puma.hpc.arizona.edu. My rank is 1 out of 30 processors
Hello world from node r1u05n2.puma.hpc.arizona.edu. My rank is 2 out of 30 processors
Hello world from node r1u05n2.puma.hpc.arizona.edu. My rank is 6 out of 30 processors
Hello world from node r1u05n2.puma.hpc.arizona.edu. My rank is 7 out of 30 processors
Hello world from node r1u05n2.puma.hpc.arizona.edu. My rank is 5 out of 30 processors
Hello world from node r1u05n2.puma.hpc.arizona.edu. My rank is 8 out of 30 processors
Hello world from node r1u05n2.puma.hpc.arizona.edu. My rank is 0 out of 30 processors
Hello world from node r1u05n2.puma.hpc.arizona.edu. My rank is 4 out of 30 processors
Hello world from node r1u05n2.puma.hpc.arizona.edu. My rank is 3 out of 30 processors
Hello world from node r1u06n1.puma.hpc.arizona.edu. My rank is 14 out of 30 processors
```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
