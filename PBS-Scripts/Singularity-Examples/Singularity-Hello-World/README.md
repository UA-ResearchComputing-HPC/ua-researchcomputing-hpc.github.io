# Singularity Hello World
## About
You can use the pull or build commands to download pre-built images from resources like Singularity Hub or Docker. This example pulls a container from Singularity Hub and executes it in a PBS script on Ocelote. 

## Command Line Container Pull
If the singularity image is not in your account already, you can pull it from Singularity Hub:
```
[netid@login2 singularity_hello_world]$ module load singularity
[netid@login2 singularity_hello_world]$ singularity pull ./hello-world.sif shub://vsoch/hello-world
INFO:    Downloading shub image
59.8MiB / 59.8MiB [==============================================================================] 100 % 88.5 MiB/s 0s
```

## PBS Script
[Click here to download example](singularity_hello_world.tar.gz)
```
#!/bin/bash
#PBS -W group_list=hpcteam
#PBS -q standard
#PBS -N singularity-hello-world
#PBS -j oe
#PBS -l select=1:ncpus=1:mem=6gb:pcmem=6gb
#PBS -l walltime=00:01:00

cd $PBS_O_WORKDIR
module load singularity
singularity run hello-world.sif
```
