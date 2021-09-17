# Using Virtual Environments

## Overview
[Click here to download example]()

Have you ever wanted to install custom Python packages on a cluster but received permission denied errors when you tried? Or maybe you want to create separate environments for different workflows don't interfere with one another. A good solution to these issues is to use ```virtualenv```. This is a way to create an isolated environment that can be turned off and on allowing you to add local packages and create unique development environments. For more in-depth information, see our [online Python documentation](https://public.confluence.arizona.edu/display/UAHPC/Using+and+Installing+Python).

In this example, we'll set up a virtual environment, activate it, install a non-standard package, and use it in a job submission.


## Environment Creation
> NOTE: This example uses python 3.6 to create the virtual environment. Python 3.7 uses different syntax. For more information, see our docs linked in the Overview.
To begin, start an interactive session. Once it's ready, load your desired version of python and create your virtual environment. Once it's created, it will show up as a directory with its own nested bin and lib directories. In the bin, there's a script called ```activate``` you can source to turn on your environment. Once your environment is active, you can pip install and use your packages. In this example, we'll install the emoji package.

```
[netid@r2u05n1 ~]$ module load python/3.6
[netid@r2u05n1 ~]$ virtualenv --system-site-packages my_environment
[netid@r2u05n1 ~]$ source my_environment/bin/activate
(my_environment) [netid@r2u05n1 ~]$ pip install emoji
```

## Python Script
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
echo "First, we'll test the script without activating our environment:"; echo
python3 myscript.py

echo; echo "Next, we'll activate our environment and rerun our script:" ; echo
source my_environment/bin/activate
python3 myscript.py
```

## Submit the Job
```
[netid@junonia ~]$ sbatch myenv.slurm 
Submitted batch job 2082962
```

## Output
```
[netid@junonia ~]$ cat slurm-2082962.out 

First, we'll test the script without activating our environment:

Not using a virtual environment, let's check if we can import emoji
oops, can't import emoji!

Next, we'll activate our environment and rerun our script:

💾 Using a virtual environment! 💾
Detailed performance metrics for this job will be available at https://metrics.hpc.arizona.edu/#job_viewer?action=show&realm=SUPREMM&resource_id=73&local_job_id=2082962 by 8am on 2021/09/18.
```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
