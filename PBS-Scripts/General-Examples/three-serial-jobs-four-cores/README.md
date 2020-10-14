# Three Serial Jobs Requesting Four Cores
Script to run three independent serial jobs requesting four cores on a single node. 

## Script Example
[Click here to download example](four_core_three_serial_job.tar.gz)

The following script runs three independent R scripts simultaneously by running two as background processes. Any executable can be used with this method. Do not assign more resources than the node has. 
```
#!/bin/bash
#PBS -N three_serial_jobs
#PBS -j oe
#PBS -W group_list=<YOUR_GROUP>
#PBS -q standard
#PBS -l select=1:ncpus=4:mem=24gb:pcmem=6gb
#PBS -l walltime=01:00:00

cd $PBS_O_WORKDIR
### Any executable can be run below. The R code is used only as an example.
module load R/3.6.1
date
Rscript second_job.r &
date
Rscript first_job.r &
date
Rscript third_job.r
date
```
