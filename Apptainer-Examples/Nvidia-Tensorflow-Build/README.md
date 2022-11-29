# Nvidia NGC Tensorflow Build

See our online documentation for more information on Nvidia NGC images: [Apptainer, Nvidia, and GPUs](https://public.confluence.arizona.edu/display/UAHPC/Containers#Containers-Apptainer,Nvidia,andGPUs)

## Overview
The NVIDIA GPU Cloud (NGC) provides GPU-accelerated HPC and deep learning containers for scientific computing.  NVIDIA tests HPC container compatibility with the Singularity runtime through a rigorous QA process. Application-specific information may vary so it is recommended that you follow the container-specific documentation before running with Singularity. If the container documentation does not include Singularity information, then the container has not yet been tested under Singularity. Apptainer can be used to pull, execute, and bootstrap off of Singularity images. 

To start, you'll need to [register with Nvidia](https://ngc.nvidia.com/signin). Once you have an account, you can [view their images from their catalogue](https://catalog.ngc.nvidia.com/). Click on the name of the software you're interested in to view available versions.

## Build the Container

### Find Your Base Image


### Recipe


### Build

```
[netid@cpu1 ~]$ apptainer build nvidia-tensorflow.sif nvidia-tensorflow.recipe 
INFO:    User not listed in /etc/subuid, trying root-mapped namespace
INFO:    The %post section will be run under fakeroot
INFO:    Starting build...
. . .
```

## Job Example

### Batch Script

### Submission

### Output


-----
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
