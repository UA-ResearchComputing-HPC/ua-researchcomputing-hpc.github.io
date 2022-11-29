# Apptainer Build

[![](/Images/Download-Button.png)](Apptainer-Build.tar.gz)

## About
One of Singularity's limitations is it requires root authority to build an image from a recipe. Singularity 3.0 got around this restriction by allowing remote builds through SyLabs which you may have gotten used to on HPC. Apptainer, however, has removed this option and now allows for local builds when bootstrapping off of many images. This example shows how to create and build an image locally on a compute node. 

## Example Recipe File

```singularity
BootStrap: docker
From: nersc/ubuntu-mpi:14.04
 
%runscript
    echo "This is what happens when you run the container..."
```

# Build Instructions
Apptainer allows for a local build and the ```--remote``` option is no longer available. 
```console
[netid@cpu1 ~]$ apptainer build example.sif example.recipe 
INFO:    User not listed in /etc/subuid, trying root-mapped namespace
INFO:    The %post section will be run under fakeroot
INFO:    Starting build...
. . .
INFO:    Adding runscript
INFO:    Creating SIF file...
INFO:    Build complete: example.sif
[netid@cpu1 ~]$ 
```

## Submission Script
```bash
#!/bin/bash
#SBATCH --job-name=nersc_example
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --mem=1gb                    
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

apptainer run example.sif
```

## Job Submission
```console
[netid@cpu1 ~]$ sbatch nersc_example.slurm 
Submitted batch job 514821
```

## Output
```console
[netid@cpu1 ~]$ cat slurm-514821.out 
This is what happens when you run the container...
```

-----
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
