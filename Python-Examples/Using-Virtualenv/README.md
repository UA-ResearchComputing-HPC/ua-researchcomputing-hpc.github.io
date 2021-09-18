# Using Virtual Environments

## Overview
[Click here to download example](Using-Virtualenv.tar.gz)

Have you ever wanted to install custom Python packages on a cluster but received permission denied errors when you tried? Or maybe you want to create separate environments for different workflows so they don't interfere with one another. A good solution is to use ```virtualenv```. This is a way to create a local, unique environment that can be activated and deactivated as needed. For more in-depth information, see our [online Python documentation](https://public.confluence.arizona.edu/display/UAHPC/Using+and+Installing+Python).

In this example, we'll set up a virtual environment, activate it, install a non-standard package, and use it in a job submission.


## Environment Creation
> NOTE: This example uses python 3.6 to create the virtual environment. Python 3.8 uses different syntax. For more in-depth information, see our docs linked in the Overview.

To begin, start an interactive session. This can be easily done by running ```interactive``` from one of the login nodes. Once it's ready, load your desired version of python and create your virtual environment using ```virtualenv --system-site-packages /path/to/env```. The environment will be given the name included in the path. If only a name is provided without a path, your environment will be saved to your working directory. 

> NOTE: to run this example in your account, you'll either want to create your virtual environment in the same directory where your python and SLURM scripts are located, or change the SLURM script to point to the correct location.

Once the process is complete, a directory with its own nested bin and lib directories will be available in the path specified. In the bin, there's a script called ```activate``` that you can source to load the environment. Once your environment is active, you can pip install custom packages. Once they're installed, they will only be available while the environment is active. In this example, we'll install the emoji package for demonstration purposes.

```
[netid@r2u05n1 ~]$ module load python/3.6
[netid@r2u05n1 ~]$ virtualenv --system-site-packages my_environment
[netid@r2u05n1 ~]$ source my_environment/bin/activate
(my_environment) [netid@r2u05n1 ~]$ pip install emoji
```

## Python Script
A simple python script that tests whether a virtual environment is active and reports back to the user. 
```
#!/usr/bin/env python3

import os

def in_venv():
    try:
        os.environ["VIRTUAL_ENV"]
        return True
    except KeyError:
        return False

virtualenv = in_venv()
if virtualenv == True:
    try:
        import emoji
        print(emoji.emojize(":floppy_disk: Using a virtual environment! :floppy_disk:"))
    except ImportError:
        print("Virtual environment active! But it looks like you haven't installed the emoji package")
else:
    print("Not using a virtual environment, let's check if we can import emoji")
    try:
        import emoji
    except ImportError:
        print("oops, can't import emoji!")
```

## Submission Script
In this SLURM script, we'll try running our python script with and without our virtual environment activated.
```
#!/bin/bash
#SBATCH --job-name=Sample_venv_Job
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --mem=1gb                    
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

# Load the necessary module
module load python/3.6
echo -e "\nFirst, we'll test the script without activating our environment:\n"
python3 myscript.py

echo -e "\nNext, we'll activate our environment and rerun our script:\n" 
source my_environment/bin/activate
python3 myscript.py
```

## Submit the Job
```
[netid@junonia ~]$ sbatch myenv.slurm 
Submitted batch job 174168
```

## Output
```
[netid@wentletrap ~]$ cat slurm-174168.out 

First, we'll test the script without activating our environment:

Not using a virtual environment, let's check if we can import emoji
oops, can't import emoji!

Next, we'll activate our environment and rerun our script:

💾 Using a virtual environment! 💾
Detailed performance metrics for this job will be available at https://metrics.hpc.arizona.edu/#job_viewer?action=show&realm=SUPREMM&resource_id=5&local_job_id=174168 by 8am on 2021/09/18.
```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
