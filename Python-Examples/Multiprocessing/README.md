# Python Multiprocessing

## Overview

[![](/Images/Download-Button.png)](Multiprocessing.tar.gz)

The Python multiprocessing package is a popular way to distribute a workflow over multiple CPUs on a single node. In this example, we'll be spreading 1000 processes over multiple CPUs using a pool of workers. The example will be submitted multiple times with varying numbers of CPUs allocated to each workflow to demonstrate the speedup. This workflow is an example of an [embarrassingly parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel) calculation.  

For more information on the multiprocessing package, see the [Python documentation](https://docs.python.org/3/library/multiprocessing.html).

## Python Script
```
#!/usr/bin/env python3

import os, time
from multiprocessing import Pool

num_cpus=int(os.environ["SLURM_CPUS_ON_NODE"])
print("Running a pool with %s workers"%num_cpus)

def f(x):
    # The sleep will allow us to get a sense of the speedup given by using multiprocessing
    time.sleep(1)
    return x*x

if __name__=="__main__":
    with Pool(num_cpus) as p:
        data = range(1,1000)
        # p.map takes an interable, maps it onto a function, and executes the processes
        # distributed over the allocated CPUs using the pool of workers
        output = p.map(f,data)
```

## Submission Script
```
#!/bin/bash
#SBATCH --job-name=Sample_Multiprocessing
### Stick to 1 task for python multiprocessing and request
### CPUs using the --cpus-per-task option
#SBATCH --ntasks=1
### Set cpus-per-task below to the number of processes 
#SBATCH --cpus-per-task=1
#SBATCH --nodes=1             
#SBATCH --time=05:00:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

module load python/3.6
time python3 multiprocess.py
```

## Submitting the Job and Output
For this example, we'll submit the job a few times with different CPU allocations to get an idea of the speedup we get from using multiprocessing

In each case below, the submission script has been modified to set ```--cpus-per-task=N``` where ```N``` is the number of CPUs allocated to the job.

### One CPU

```
[netid@wentletrap ~]$ sbatch -o 1-process.out submit.slurm
Submitted batch job 2104919
[netid@wentletrap ~]$ cat 1-process.out 
Running a pool with 1 workers

real	16m40.404s
user	0m0.340s
sys	0m0.134s
```

### Five CPUs

```
[netid@wentletrap ~]$ sbatch -o 5-processes.out submit.slurm 
Submitted batch job 2104965
[netid@wentletrap ~]$ cat 5-processes.out 
Running a pool with 5 workers

real	3m20.701s
user	0m0.131s
sys	0m0.075s
```

### Twenty CPUs
```
[netid@wentletrap ~]$ sbatch -o 20-processes.out submit.slurm 
Submitted batch job 2104966
[netid@wentletrap ~]$ cat 20-processes.out 
Running a pool with 20 workers

real	0m52.485s
user	0m0.108s
sys	0m0.091s
```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
