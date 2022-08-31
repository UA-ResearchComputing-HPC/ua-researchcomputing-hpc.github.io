# General Script Examples

## About
These examples demonstrate how to run jobs and request resources on HPC using the job scheduler SLURM. Each example comes with downloadable scripts that can be run with minor modifications.

## Scripts
### [Serial Job](Serial-Job-Example)
A basic example script that requests a single CPU on one node.

### [High Memory Node Job](High-Memory-Node)
A basic example demonstrating how to request a high memory node on either Ocelote or Puma.

### [Using a Pipefail](Pipefail)
This script uses a pipefail to kill a SLURM job in the event of a failure at any point in the pipeline

### [Disabling Core Dumps](Disable-Core-Dumps)
Sometimes when applications crash, they will save a snapshot of the working memory at the time of the crash to a core dump file. Core dump files can be handy for debugging, but they can also be quite large. In some cases, it may be preferable to disable core dumps to prevent your storage allocations from filling up. To do this, you can include the line ulimit -c 0 in your batch script prior to executing your application. An example is shown below

### [Retrieving Scratch Files](Cleanup-Tmp-Files)
Example of using a compute node's local storage, or scratch space, during a job's execution and retrieving the output before the job's walltime limit is reached.



*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
