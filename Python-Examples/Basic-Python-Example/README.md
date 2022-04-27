# Overview

[![](/Images/Download-Button.png)](Basic-Python-Example.tar.gz)

This example runs a basic Python script.


# Python Script

A basic python script to test which Python is being used and flushes its output.

> Python buffers input/output functions to improve efficiency. This means that output is stored in memory before printing. The result is that while your Python script is running in a batch job, you may not see any output appear in your SLURM file until the job has completed, even if you have print statements scattered throughout. If you'd like to force your data to be flushed to stdout during the job's execution, you can use a ```sys.stdout.flush()``` statement or set the parameter ```flush=True``` in your print statement. [More information here](https://www.delftstack.com/howto/python/python-print-flush/).

```python3
#!/usr/bin/env python3

import sys, os


print("\n ===== In Python Script =====\n")
print("Hello World!")
print("The python being used to execute this script is in: %s" %os.path.dirname(sys.executable))
print("The version of python being used is: %s"%sys.version)
print("\n == Leaving Python Script ===\n")
# Want your output printed before the job ends? Use a flush statement:
sys.stdout.flush()
```

# Submission Script
```bash
#!/bin/bash

#SBATCH --job-name=Sample_Python
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --time=00:10:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

# Let's run a few python tests 

#### Run Python script with python 3.6 module ####
echo "Let's try first using the python 3.6 module"
echo "Running: module load python/3.6"
module load python/3.6
python3 basic_example.py

#### Run script directly and use a different module ####
echo "Let's swap modules and run our script again"
echo "Running: module swap python/3.6 python/3.8"
module swap python/3.6 python/3.8
python3 basic_example.py
```

# Job Submission
```console
[netid@wentletrap ~]$ sbatch submit_python.slurm 
Submitted batch job 53710
```

# Output
```console
[netid@wentletrap ~]$ cat slurm-53710.out 
Let's try first using the python 3.6 module
Running: module load python/3.6

 ===== In Python Script =====

Hello World!
The python being used to execute this script is in: /opt/ohpc/pub/apps/python/3.6.5/bin
The version of python being used is: 3.6.5 (default, Mar 31 2021, 11:18:02) 
[GCC 8.3.0]

 == Leaving Python Script ===

Let's swap modules and run our script again
Running: module swap python/3.6 python/3.8

 ===== In Python Script =====

Hello World!
The python being used to execute this script is in: /opt/ohpc/pub/apps/python/3.8.2/bin
The version of python being used is: 3.8.2 (default, Mar 16 2021, 17:11:14) 
[GCC 8.3.0]

 == Leaving Python Script ===
```
*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
