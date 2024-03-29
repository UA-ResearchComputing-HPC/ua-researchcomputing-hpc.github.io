# Python Examples

> :bulb: If you have never run a batch job before, see our [Quick Start Guide](https://public.confluence.arizona.edu/display/UAHPC/Puma+Quick+Start) for a walkthrough. We also have a [video recording of our Intro to HPC workshop](https://public.confluence.arizona.edu/display/UAHPC/Training#Training-IntroductiontoHPC) that goes over system use and batch scripts. Intro to HPC also comes with a [companion page](https://ua-researchcomputing-hpc.github.io/Intro-to-HPC/). 

This page has a collection of various python examples that users may find helpful. For information on using python on HPC, see our [online documentation](https://public.confluence.arizona.edu/display/UAHPC/Using+and+Installing+Python).


## General Examples

### [Basic Python Example](Basic-Python-Example)
A basic python example to get started. 

### [Using a Virtual Environment](Using-Virtualenv)
Virtual environments are great ways to customize your own python environments and add user packages. This tutorial installs a non-standard package and makes use of it in a batch script. 

### [Memory Limits](Memory-Limits)
Sometimes jobs use more memory than they've been allotted and are killed by the job scheduler. Python has different methods for detecting memory usage that can help profile a workflow as part of the testing process, or kill a task once a memory threshold is met. This example shows how to kill a python process that exceeds a threshold set by the user. 

---------
## Jupyter

### [Converting a Jupyter Notebook to a Batch Script](Converting-Jupyter-to-Batch)
Jupyter notebooks are available through our Open OnDemand web interface. This is a great, user-friendly tool and works well for many types of analyses. However, there may be some cases when it might be advantageous to run jobs in batch instead (for example, jobs that takes hours or days to complete). This example goes over the process of converting an existing Jupyter notebook to a python script that can be executed as a batch job.

---------
## Anaconda

### [Using an Anaconda Environment in a Batch Script](Anaconda-In-Batch)
This example goes through the process of setting up an anaconda environment, installing packages, and running a batch job using the environment. If you've set Anaconda's auto-activate feature to ```false``` and have run into an error about ```conda``` not being a valid command, this example shows you how to fix the problem. 

---------
## Parallelization and MPI Jobs

### [Multiprocessing](Multiprocessing)
The python ```multiprocessing``` package is a popular way to spread a workflow over multiple CPUs. This example demonstrates how to assign multiple CPUs to a SLURM job and then utilize them in a multiprocessing python job. 

### [MPI4PY](MPI4PY)
The package ```mpi4py``` provides a Python interface to MPI that can be used to parallelize and utilize more than one node in a single workflow. 

---------
## Singularity/Apptainer Examples

### [Simple Tensorflow Example](../Apptainer-Examples/Tensorflow-Module-Job)
A machine learning example that shows how to run a Tensorflow script on an GPU node.

### [Nvidia Tensorflow Build](../Apptainer-Examples/Nvidia-Tensorflow-Build)
An example of how to build your own Tensorflow image using an Nvidia registry image as a base.

### [Tensorflow Build from Cuda](../Apptainer-Examples/Tensorflow-From-Cuda)
One option for running Tensorflow on HPC is to pull pre-built images from Nvidia's NGC repository. Another option is building your own container. This example shows how to build a Tensorflow image bootstrapping off a Cuda Docker image.



*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
