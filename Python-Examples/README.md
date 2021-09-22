# Python Examples
This page has a collection of various python examples that users may find helpful. For information on using python on HPC, see our [online documentation](https://public.confluence.arizona.edu/display/UAHPC/Using+and+Installing+Python).

## General Examples

### [Basic Python Example](Basic-Python-Example)
A basic python example to get started. 

### [Using a Virtual Environment](Using-Virtualenv)
Virtual environments are great ways to customize your own python environments and add user packages. This tutorial installs a non-standard package and makes use of it in a batch script. 

### [Memory Limits](Memory-Limits)
Sometimes jobs use more memory than they've been allotted and are killed by the job scheduler. Python has different methods for detecting memory usage that can help profile a workflow as part of the testing process, or kill a task once a memory threshold is met. This example shows how to kill a python process that exceeds a threshold set by the user. 

---------
## Parallelization and MPI Jobs

### [Multiprocessing](Multiprocessing)
The python ```multiprocessing``` package is a popular way to spread a workflow over multiple CPUs. This example demonstrates how to assign multiple CPUs to a SLURM job and then utilize them in a multiprocessing python job. 

### [MPI With Python](MPI4PY)
The package ```mpi4py``` provides a Python interface to MPI that can be used to parallelize and utilize more than one node in a single workflow. 

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
