# Overview
The package [```mpi4py```](https://mpi4py.readthedocs.io/en/stable/) provides Python with MPI bindings, allowing it to use multiple CPUs and nodes on HPC. 

# Installing
To build ```mpi4py```, you will need to be in an interactive session. From there, you can load your desired Python module, create a virtual environment, and pip install the package. 

```console
[netid@i13n9 ~]$ module load openmpi3 gnu8 python/3.6
[netid@i13n9 ~]$ export MPICC=$(which mpicc)
[netid@i13n9 ~]$ virtualenv --system-site-packages mpi4py_env
[netid@i13n9 ~]$ source mpi4py_env/bin/activate
(mpi4py_env) [netid@i13n9 ~]$ pip install mpi4py
```

# Python Script
```python
#!/usr/bin/env python3

from mpi4py import MPI
import sys

size = MPI.COMM_WORLD.Get_size()
rank = MPI.COMM_WORLD.Get_rank()
name = MPI.Get_processor_name()

print("Hello World! I am process %s of %s running on %s"%(rank,size,name),flush=True)   
```

# Submission Script
```bash
#!/bin/bash

#SBATCH --job-name=mpi4py_example
#SBATCH --ntasks=10
#SBATCH --ntasks-per-node=1
#SBATCH --nodes=10             
#SBATCH --time=00:10:00   
#SBATCH --partition=standard
#SBATCH --account=hpcteam

module load gnu8 openmpi3 python/3.6
source mpi4py_env/bin/activate
srun python3 mpi4py_example.py
```

# Job Submission
```console
[netid@wentletrap ~]$ sbatch mpi4py.slurm 
Submitted batch job 185674
```

# Output
```console
[netid@wentletrap ~]$ cat slurm-185674.out 
Hello World! I am process 0 of 10 running on i8n0.ocelote.hpc.arizona.edu
Hello World! I am process 1 of 10 running on i8n2.ocelote.hpc.arizona.edu
Hello World! I am process 9 of 10 running on i13n9.ocelote.hpc.arizona.edu
Hello World! I am process 4 of 10 running on i8n14.ocelote.hpc.arizona.edu
Hello World! I am process 7 of 10 running on i8n21.ocelote.hpc.arizona.edu
Hello World! I am process 2 of 10 running on i8n4.ocelote.hpc.arizona.edu
Hello World! I am process 6 of 10 running on i8n20.ocelote.hpc.arizona.edu
Hello World! I am process 5 of 10 running on i8n17.ocelote.hpc.arizona.edu
Hello World! I am process 3 of 10 running on i8n12.ocelote.hpc.arizona.edu
Hello World! I am process 8 of 10 running on i8n22.ocelote.hpc.arizona.edu
```

-----------

[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
