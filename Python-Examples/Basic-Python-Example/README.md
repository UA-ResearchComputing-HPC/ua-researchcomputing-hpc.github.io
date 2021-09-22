# Overview
[Click here to download example](Basic-Python-Example.tar.gz)

This example runs a basic Python script. Below, and in the script, are a couple of tips and tricks for running your python jobs

# Tips

### Why the shebang (```#!```)?
While not strictly necessary, the shebang allows you to execute your script directly using ```./my_script.py``` without needing to explicitly call ```python3```. When you include ```#!/usr/bin/env python3```, the system will use whichever python3 interpreter is in your environment. This makes your script more portable since you won't need the system-specific path to the relevant python3 executable. Note that you'll need to make your script executable to do this. You can add the execute permission using something like: ```chmod u+x your_script.py```.

### Why is my output not being printed until the job ends?
Python buffers input/output functions to improve efficiency. This means that output is stored in memory before printing. The result is that while your Python script is running in a batch job, you may not see any output appear in your SLURM file until the job has completed, even if you have print statements scattered throughout. If you'd like to force your data to be flushed to stdout during the job's execution, you can use a ```sys.stdout.flush()``` statement or set the parameter ```flush=True``` in your print statement. [More information here](https://www.delftstack.com/howto/python/python-print-flush/).


### Why do I get python 2 when running ```python``` when I have the python 3 module loaded?
The executable ```python``` calls python 2 which is the system version stored in ```/usr/bin```. To call python 3, use ```python3```. There are exceptions to this, such as when using Anaconda or a virtual environment. 


# Python Script
A basic python script to test which Python is being used and flushes its output.
```
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
```
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
```
[netid@wentletrap ~]$ sbatch submit_python.slurm 
Submitted batch job 53710
```

# Output
```
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
