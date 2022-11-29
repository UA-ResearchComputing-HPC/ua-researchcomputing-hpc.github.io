# Nvidia NGC Tensorflow Build

See our online documentation for more information on Nvidia NGC images: [Apptainer, Nvidia, and GPUs](https://public.confluence.arizona.edu/display/UAHPC/Containers#Containers-Apptainer,Nvidia,andGPUs)

## Overview
The NVIDIA GPU Cloud (NGC) provides GPU-accelerated HPC and deep learning containers for scientific computing.  NVIDIA tests HPC container compatibility with the Singularity runtime through a rigorous QA process. Application-specific information may vary so it is recommended that you follow the container-specific documentation before running with Singularity. If the container documentation does not include Singularity information, then the container has not yet been tested under Singularity. Apptainer can be used to pull, execute, and bootstrap off of Singularity images. 

In this example, we'll create an Apptainer recipe that boostraps off of an Nvidia NGC Tensorflow image and pip-installs custom Python packages. 

To start, you'll need to [register with Nvidia](https://ngc.nvidia.com/signin). Once you have an account, you can [view their images from their catalogue](https://catalog.ngc.nvidia.com/). Click on the name of the software you're interested in to view available versions.

## Build the Container

### Find Your Base Image

Start by navigating to the [Nvidia Registry](https://catalog.ngc.nvidia.com/). Then, under the search bar, enter the name of the image you're looking for. For example, Tensorflow and Pytorch are common options. 

<img src="nvidia-catalog-search.png" alt="nvidia-catalog-search" width="500"/> 

Once you find and click the image repository you're looking for, go to the Tags tab. In this example, we'll use the latest (as of this writing) image that uses Python 3. The information needed to bootstrap off this image is the image path. Click the ellipses (```...```) in the top right of the relevant tag's tile and click "Copy Image Path":

<img src="tensorflow-tags.png" alt="tensorflow-tags" width="500"/> 

We'll now use this to create our recipe.

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
