# Converting a Jupyter Notebook to Run in Batch

[![](/Images/Download-Button.png)](JupyterExample.tar.gz)

# Overview

Jupyter notebooks are available through our [Open OnDemand web interface](https://ood.hpc.arizona.edu/). This is a great, user-friendly tool and works well for many types of analyses. However, there may be some cases when it might be advantageous to run jobs in batch instead. For example, if a job takes several hours or days to complete it might be inconvenient to have to keep your computer and browser active for the duration of the run.

Batch jobs, on the other hand, are automated by the system and allow you to log out, close your computer, and walk away without interrupting your work. You can even request email notifications to be sent when your job is complete (do not do this for array jobs!). 

In this example, we'll start with a Jupyter notebook that generates an image. To convert this workflow into something that can be run in batch we will:
1. Edit the notebook to save the image to an output file instead of displaying it. 
2. Log into HPC using a terminal or SSH client.
3. Start an interactive session.
4. Convert the notebook to a Python script.
5. Write and submit a batch job.

# Edit the Notebook
> The code used to generate the histogram in this example was borrowed from [matplotlib's documentation](https://matplotlib.org/stable/gallery/lines_bars_and_markers/filled_step.html#sphx-glr-gallery-lines-bars-and-markers-filled-step-py).

Let's say we have a Jupyter notebook saved in one of our directories on HPC called ```Hist-Plot.ipynb```. We can open this file by starting a Jupyter Notebook session through Open OnDemand under [Interactive Apps](https://public.confluence.arizona.edu/display/UAHPC/Open+On+Demand#OpenOnDemand-InteractiveGraphicalApplications). 

<img src="Jupyter-file.png" alt="Jupyter-file" width="950"/>

The file in this example generates an image using matplotlib and displays it to the screen using ```plt.show()```


<img src="Example-output.png" alt="Example-output" width="950"/>


If we try running this script in an environment where images cannot be forwarded to our screen (e.g., in batch), we will not receive our image at the end of the run. 

To ensure that we get our results, we'll change ```plt.show()``` to ```plt.savefig('Hist-Plot.png', bbox_inches='tight')```. This will save our image to a PNG file with the name ```Hist-Plot.png```. The second argument ```bbox_inches='tight'``` is optional and trims excess whitespace. 

Once the changes have been made, we'll save the file using **Save and Checkpoint**. 


<img src="save-update.png" alt="save-update" width="450"/>

# Log into HPC and Start an Interactive Session

Logging into HPC can either be done from your [local workstation using an ssh client](https://public.confluence.arizona.edu/display/UAHPC/System+Access#SystemAccess-CommandLine/TerminalAccess) or through an [Open Ondemand terminal](https://public.confluence.arizona.edu/display/UAHPC/System+Access#SystemAccess-TerminalAccess). 

Next, start an [interactive session](https://public.confluence.arizona.edu/display/UAHPC/Running+Jobs+with+SLURM#RunningJobswithSLURM-interactive-jobsInteractiveJobs) by using the command ```interactive```. This will give you access to system modules (software) on a compute node. There is a contrib module that will give you access to the software needed to convert your notebook. Load it using:

```
[netid@cpu37 JupyterExample]$ module load contrib
[netid@cpu37 JupyterExample]$ module load bjoyce3/sarawillis/jupyter-nb-convert 
```

# Convert the Notebook to a Python Script

The module we loaded in the previous step gives us access to the command ```nbconvert```:
```
[netid@cpu37 JupyterExample]$ type nbconvert
nbconvert is aliased to `jupyter nbconvert --to script'
```

When you run this command on a ```.ipynb``` file, a second file will be created with the extension ```.py```. An example is shown below: 
```
[netid@cpu27 JupyterExample]$ ls
Hist-Plot.ipynb
[netid@cpu27 JupyterExample]$ nbconvert Hist-Plot.ipynb
[NbConvertApp] Converting notebook Hist-Plot.ipynb to script
[NbConvertApp] Writing 6075 bytes to Hist-Plot.py
[netid@cpu27 JupyterExample]$ ls
Hist-Plot.ipynb  Hist-Plot.py
```

# Create a Batch Script
Next, we need a batch script so we can submit the job. Create a file using ```touch Hist-Plot.slurm``` and open it in your favorite text editor. Add the contents (changing ```--account``` to match your group name):
```
#!/bin/bash
#SBATCH --job-name=Hist-Plot
#SBATCH --time=00:01:00
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --account=YOUR_GROUP
#SBATCH --partition=standard

module load python/3.6
python3 Hist-Plot.py
```

# Submit the Job
The job can be submitted using the standard [sbatch command](https://public.confluence.arizona.edu/display/UAHPC/Running+Jobs+with+SLURM#RunningJobswithSLURM-SLURMandSystemCommands). This will return an ID that you can use to track the job's status. 
```
[netid@cpu27 JupyterExample]$ sbatch Hist-Plot.slurm 
Submitted batch job 288817
```

# Check Output
You'll find the slurm output files in the directory where you submitted your job. In this example, the histogram we created will also be saved in the same place. Any text output from your Python script will be saved in the text file ```slurm-$SLURM_JOB_ID.out```.
```
[netid@cpu27 JupyterExample]$ ls
Hist-Plot.ipynb  slurm-288817.out  Hist-Plot.png  Hist-Plot.py  Hist-Plot.slurm
```

An easy method to view the image is through Open OnDemand using the [file browser](https://public.confluence.arizona.edu/display/UAHPC/Open+On+Demand#OpenOnDemand-FileBrowser). To view the histogram, find the relevant directory (either in your ```/home```, ```/groups``` or ```/xdisk```), click the three dots next to Hist-Plot.png, and select View.


<img src="view-png-in-ood.png" alt="view-png-in-ood.png" width="950"/>

This will open a new window where you should see the following:

<img src="batch-figure-output.png" alt="batch-figure-output" width="950"/>

**********

[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
