# Apptainer Examples

> During the October 26, 2022 maintenance window, Singularity was removed and replaced with [Apptainer](https://apptainer.org/). The commands ```singularity``` (now a link pointing to Apptainer) and ```apptainer``` may be used to perform all the same operations you're used to and you can still run your existing images. However, remote builds via SyLabs are no longer supported. 

> ⚠️This examples repository is being actively updated to reflect the apptainer changes. Pardon our mess.⚠️

All examples using ```apptainer``` directly on the command line need to be done in [an interactive session on a compute node](https://public.confluence.arizona.edu/display/UAHPC/Running+Jobs+with+SLURM#RunningJobswithSLURM-interactive-jobsInteractiveJobs). If you get an error saying ```apptainer: command not found```, you're likely on a login node. 

## Pulls

### [Hello World](Hello-World)
A basic example of how to pull and run prebuilt images from Singularity Hub.

### [Apptainer Docker Pull](Apptainer-Docker-Pull)
A basic example of how to use apptainer to pull and run Docker images.

## Builds
### [Basic Build Example](Apptainer-Build)

Example showing how to create and build an image locally on a compute node.

### Nvidia Tensorflow Example
> Coming Soon 

An example of modifying a tensorflow image from the Nvidia repository.

### Tensorflow from Scratch
> Coming Soon

An example of how to build a Tensorflow image from scratch.

## Usage

### Simple Tensorflow Example
> Coming Soon 

A machine learning example that shows how to run a Tensorflow script on a GPU node.

## GUIs

### Building and Using an RStudio Server
> Coming Soon 

An example of how to build and use an RStudio server in an Open OnDemand Desktop session using a container. 


*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
