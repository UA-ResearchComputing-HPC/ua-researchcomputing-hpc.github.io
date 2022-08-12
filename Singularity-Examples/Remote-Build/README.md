# Singularity Remote Build

[![](/Images/Download-Button.png)](Remote-Build.tar.gz)

## About
An earlier limitation of Singularity was the requirement for access to a root account to build a container. You not have root access on an HPC cluster. Singularity 3.0 introduced the ability to build a container in the cloud, negating the root requirement.

## Example Recipe File

```singularity
BootStrap: docker
From: nersc/ubuntu-mpi:14.04
 
%runscript
    echo "This is what happens when you run the container..."
```

## Build Instructions
1. Log into [https://cloud.sylabs.io]
2. Generate an access token (API key)
3. From an interactive session: ```singularity remote login``` and paste in the API key
4. In your recipe file's directory: ```singularity build --remote ./nersc.sif nersc.recipe```

```console
[netid@r1u03n1 ~]$ singularity build --remote nersc.sif nersc.recipe
INFO:    Remote "default" now in use.
INFO:    Starting build...
Getting image source signatures
...
INFO:    Build complete: nersc.sif
[netid@r1u03n1 ~]$ 
```

## Submission Script
```bash
#!/bin/bash
#SBATCH --job-name=Singularity_Docker_Pull
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --mem=1gb                    
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

singularity run nersc.sif
```

## Job Submission
```console
[netid@r1u03n1 ~]$ sbatch remote-build-example.slurm 
Submitted batch job 1996740
```

## Output
```console
[netid@r1u03n1 ~]$ cat slurm-1996740.out 
Hello world! This is what happens when you run the container...
```

-----
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
