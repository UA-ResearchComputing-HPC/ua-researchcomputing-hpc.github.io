# Singularity Remote Build

## About
An earlier limitation of Singularity was the requirement for access to a root account to build a container.  You will not have root access on a HPC cluster.  Singularity 3.0 introduced the ability to build a container in the cloud negating the root requirement.

## Example Recipe File
```
BootStrap: docker
From: nersc/ubuntu-mpi:14.04
 
%runscript
    echo "This is what happens when you run the container..."
```

## Build Instructions
1.  Log into [https://cloud.sylabs.io](https://cloud.sylabs.io)
2. Generate an access token (API key)
3. From your working directory: module load singularity
4. ```singularity remote login``` and paste in the API key
5. ```singularity build --remote ~/nersc.sif nersc.recipe```

## Run Instructions
```
[netid@i0n11 singularity_remote_build]$ singularity run nersc.sif 
This is what happens when you run the container...
```

[back](../)
