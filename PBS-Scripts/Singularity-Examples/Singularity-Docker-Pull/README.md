# Singularity Docker Pull

## About
You can use the pull command to download pre-built images from resources like Docker. This example pulls a container and executes it in a PBS script on Ocelote. 

## Command Line Container Pull
This can be run prior to running your job. Your PBS script can then be told to execute the singularity image file in your account.
```
[netid@login2 singularity_docker_pull]$ module load singularity/3/3.6.3 
[netid@login2 singularity_docker_pull]$ singularity pull ./lolcow.sif docker://godlovedc/lolcow
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
...
INFO:    Creating SIF file...
[netid@login2 singularity_docker_pull]$
```
## PBS Script
[Click here to download example](singularity_docker_pull.tar.gz)
```
#!/bin/bash

#PBS -W group_list=hpcteam
#PBS -q standard
#PBS -N singularity-docker-pull
#PBS -l select=1:ncpus=1:mem=6gb:pcmem=6gb
#PBS -l walltime=00:01:00

cd $PBS_O_WORKDIR
module load singularity
singularity run lolcow.sif
```
