# Full Node MPI Job

Script to run an MPI job using all 28 Cores on a single node with 168GB of memory. 

## Script Example
[Click here to download example](full_node_mpi_job.tar.gz)

```
#!/bin/bash
#PBS -N myjobname
#PBS -W group_list=<YOUR_GROUP>
#PBS -q windfall
#PBS -l select=1:ncpus=28:mem=168gb
#PBS -l walltime=01:00:00

module load openmpi
cd $PBS_O_WORKDIR
  
date
/usr/bin/time mpirun -np 28 ./hello_world
date
```

## Companion MPI Script

The following C script was used to create the executable for this example. This is included in the example available for download above. 

#### C script:
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
To compile:
```
module load openmpi
mpicc -o hello_world hello_world.c
```
