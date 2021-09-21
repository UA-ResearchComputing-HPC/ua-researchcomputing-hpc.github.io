# Overview
[Click here to download example](Basic-Python-Example.tar.gz)

This example runs a basic Python script. Below, and in the script, are a couple of tips and tricks for running your python jobs

# Tips

### Why the shebang (```#!```)?
While not strictly necessary, the shebang allows you to execute your script directly using ```./my_script.py``` without needing to explicitly call ```python3```. When you include ```#!/usr/bin/env python3```, the system will use whichever python3 interpreter is in your environment. This makes your script more portable since you won't need the system-specific path to the relevant python3 executable. 

### Why is my output not being printed until the job ends?
Python buffers input/output functions to improve efficiency. This means that output is stored in memory before printing. The result is that while your Python script is running in a batch job, you may not see any output appear in your SLURM file until the job has completed, even if you have print statements scattered throughout. If you'd like to force your data to be flushed to stdout during the job's execution, you can use a ```sys.stdout.flush()``` statement or set the parameter ```flush=True``` in your print statement. [More information here](https://www.delftstack.com/howto/python/python-print-flush/).


### Why do I get python 2 when running ```python``` when I have the python 3 module loaded?
The executable ```python``` calls python 2 which is the system version stored in ```/usr/bin```. To call python 3, use ```python3```. There are exceptions to this, such as when using Anaconda or a virtual environment. 


# Python Script
Here, we'll implement some ways to test the tips listed above in a simple python script.

```
#!/usr/bin/env python3

import sys, os


print("\n ===== In Python Script =====\n")
print("The python being used to execute this script is in: %s" %os.path.dirname(sys.executable))
print("The version of python being used is: %s"%sys.version)
# Want your output printed before the job ends? Use a flush statement:
sys.stdout.flush()

# Did you pass variables into the script, e.g. using "python3 <script> [arg]"?
print("\nChecking if any variables have been passed to the script")
try:
    input_var = sys.argv[1]
except IndexError:
    input_var = None
if input_var == None:
    print("Guess not, oh well!")
else:
    print("Looks like you've included the input variable: %s"%input_var)

print("\nLet's also grab some environment variables")
print("We're running on node: %s"%os.environ["SLURM_NODELIST"])
print("Our SLURM Job ID is: %s"%os.environ["SLURM_JOB_ID"])

print("\n===== Leaving Python Script =====\n")
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

# Defining some variables so it's easy to change if necessary
target_script="basic_example.py"
exec_python_statement="python3 $target_script"
direct_exec="./$target_script"

# Let's run a few python tests now

#### Run Python script with python 3.6 module ####
echo "Let's try first using the python 3.6 module"
module load python/3.6
echo -e "Running command: $exec_python_statement \n"
$exec_python_statement

#### Run script directly and use a different module ####
echo "Let's see what happens when we try executing our script directly with a different module:"
echo "Running: module swap python/3.6 python/3.8"
module swap python/3.6 python/3.8
echo -e "Now trying: $direct_exec"
$direct_exec

#### Check the exit status of last bash command (given by variable $?) ####
if [[ $? != 0 ]]; then
    echo "Oops, looks like $direct_exec didn't work. Let's try changing permissions"
    IFS=' ' read -ra PERMS <<< $(ls -l basic_example.py); PERMISSIONS=${PERMS[0]}
    echo "Previous file permissions: $PERMISSIONS"
    echo "Executing: chmod u+x $target_script"
    wait
    chmod u+x $target_script
    IFS=' ' read -ra PERMS <<< $(ls -l basic_example.py); PERMISSIONS=${PERMS[0]}
    echo "New file permissions: $PERMISSIONS"
    echo "Executing: $direct_exec"
    $direct_exec
    chmod u-x $target_script
fi

#### Try passing an argument to our python script, and use anaconda ####
echo "Lastly, we'll feed our python script an input variable. For fun, we'll get rid of python 3.8 and will use anaconda"
echo "Running: module swap python/3.8 anaconda/2020.11"
module swap python/3.8 anaconda/2020.11
echo "Running $exec_python_statement INPUT"
$exec_python_statement INPUT
```

# Job Submission
```
[netid@wentletrap ~]$ sbatch submit_python.slurm 
Submitted batch job 53325
```

# Output
```
[netid@wentletrap ~]$ cat slurm-53325.out 
Let's try first using the python 3.6 module
Running command: python3 basic_example.py 


 ===== In Python Script =====

The python being used to execute this script is in: /opt/ohpc/pub/apps/python/3.6.5/bin
The version of python being used is: 3.6.5 (default, Mar 31 2021, 11:18:02) 
[GCC 8.3.0]

Checking if any variables have been passed to the script
Guess not, oh well!

Let's also grab some environment variables
We're running on node: cpu1
Our SLURM Job ID is: 53325

===== Leaving Python Script =====

Let's see what happens when we try executing our script directly with a different module:
Running: module swap python/3.6 python/3.8
Now trying: ./basic_example.py
/var/spool/slurm/d/job53325/slurm_script: line 28: ./basic_example.py: Permission denied
Oops, looks like ./basic_example.py didn't work. Let's try changing permissions
Previous file permissions: -rw-r--r--
Executing: chmod u+x basic_example.py
New file permissions: -rwxr--r--
Executing: ./basic_example.py

 ===== In Python Script =====

The python being used to execute this script is in: /opt/ohpc/pub/apps/python/3.8.2/bin
The version of python being used is: 3.8.2 (default, Mar 16 2021, 17:11:14) 
[GCC 8.3.0]

Checking if any variables have been passed to the script
Guess not, oh well!

Let's also grab some environment variables
We're running on node: cpu1
Our SLURM Job ID is: 53325

===== Leaving Python Script =====

Lastly, we'll feed our python script an input variable. For fun, we'll get rid of python 3.8 and will use anaconda
Running: module swap python/3.8 anaconda/2020.11
Running python3 basic_example.py INPUT

 ===== In Python Script =====

The python being used to execute this script is in: /opt/ohpc/pub/apps/anaconda/2020.11/bin
The version of python being used is: 3.8.5 (default, Sep  4 2020, 07:30:14) 
[GCC 7.3.0]

Checking if any variables have been passed to the script
Looks like you've included the input variable: INPUT

Let's also grab some environment variables
We're running on node: cpu1
Our SLURM Job ID is: 53325

===== Leaving Python Script =====
```
*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
