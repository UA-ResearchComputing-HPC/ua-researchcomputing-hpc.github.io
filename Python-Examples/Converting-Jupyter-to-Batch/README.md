# Converting a Jupyter Notebook to Run in Batch

[![](/Images/Download-Button.png)](JupyterExample.tar.gz)

# Overview
> If you have never run a batch job before, see our [Quick Start Guide](https://public.confluence.arizona.edu/display/UAHPC/Puma+Quick+Start) for a walkthrough. We also have a [video recording of our Intro to HPC workshop](https://public.confluence.arizona.edu/display/UAHPC/Training#Training-IntroductiontoHPC) that goes over system use and batch scripts. Intro to HPC also comes with a [companion page](https://ua-researchcomputing-hpc.github.io/Intro-to-HPC/).

Jupyter notebooks are available to HPC users through our [Open OnDemand web interface](https://ood.hpc.arizona.edu/). This is a great, user-friendly tool and works well for many types of analyses. However, there may be some cases when it might be advantageous to run jobs in batch instead. For example, if a job takes several hours or days to complete, it might be inconvenient to have to keep your computer and browser active for the duration of the run. Even worse, if your job has been running for a long time and you lose your connection, you might have to start all over again. 

Batch jobs, on the other hand, are automated by the system and allow you to log out of HPC, close your computer, and walk away without interrupting your work. You can even request email notifications to be sent when your job is complete (do not do this for array jobs!). 

In this example, we'll start with a Jupyter notebook that generates an image. To convert this workflow into something that can be run in batch:
1. Edit your notebook to save images to output files instead of displaying them (if applicable)
2. Install Jupyter notebook extensions in an interactive terminal session
3. Convert your notebook to a python script
4. Write a batch script
5. Submit your job

# Editing the Notebook
> The code used to generate the histogram in this example was borrowed from [matplotlib's documentation](https://matplotlib.org/stable/gallery/lines_bars_and_markers/filled_step.html#sphx-glr-gallery-lines-bars-and-markers-filled-step-py).

Let's say we have a Jupyter notebook saved in one of our directories on HPC called ```Hist-Plot.ipynb```. We can open this file by starting a Jupyter Notebook session through Open OnDemand under [Interactive Apps](https://public.confluence.arizona.edu/display/UAHPC/Open+On+Demand#OpenOnDemand-InteractiveGraphicalApplications). 

<img src="Jupyter-file.png" alt="Jupyter-file" width="950"/>

The file in this example generates an image using matplotlib and displays it to the screen using ```plt.show()```


<img src="Example-output.png" alt="Example-output" width="950"/>


If we try running this script in an environment where images cannot be forwarded to our screen (e.g., in batch), we will not receive our image at the end of the run. 

To ensure that we get our results, we'll change ```plt.show()``` to ```plt.savefig('Hist-Plot.png', bbox_inches='tight')```. This will save our image to a PNG file with the name ```Hist-Plot.png```. The second argument ```bbox_inches='tight'``` is optional and trims excess whitespace. 

Once the changes have been made, we'll save the file using **Save and Checkpoint**. 


<img src="save-update.png" alt="save-update" width="450"/>

# Install Jupyter Notebook Extensions

The next step is to install Jupyter Notebook Extensions. This gives us a handy command we can use to convert Jupyter Notebooks into plain Python scripts. 

To do this log into a terminal session and connect to HPC. This can either be done from your [local workstation using an ssh client](https://public.confluence.arizona.edu/display/UAHPC/System+Access#SystemAccess-CommandLine/TerminalAccess) or through an [Open Ondemand terminal session](https://public.confluence.arizona.edu/display/UAHPC/System+Access#SystemAccess-TerminalAccess). 

Next, start an [interactive session](https://public.confluence.arizona.edu/display/UAHPC/Running+Jobs+with+SLURM#RunningJobswithSLURM-interactive-jobsInteractiveJobs) by using the command ```interactive```. This will give you access to system modules. To install the Jupyter extensions, use the following:

```
(elgato) [netid@wentletrap JupyterExample]$ interactive
[netid@cpu27 JupyterExample]$ module load python/3.6
[netid@cpu27 JupyterExample]$ pip install jupyter_contrib_nbextensions
[netid@cpu27 JupyterExample]$ pip install jupyter_nbextensions_configurator
[netid@cpu27 JupyterExample]$ jupyter contrib nbextension install --user
[netid@cpu27 JupyterExample]$ jupyter nbextensions_configurator enable --user
```
This will install the relevant files and dependencies in the hidden directories ```~/.local``` and ```~/.jupyter```.

# Convert Notebook to Python Script

The Jupyter extensions we installed in the previous step give us access to the command ```jupyter nbconvert --to script``` which allows us to convert notebook files to plain python syntax. When you convert a ```.ipynb``` file, a second file will be created with the extension ```.py```. An example is shown below: 
```
[netid@cpu27 JupyterExample]$ ls
Hist-Plot.ipynb
[netid@cpu27 JupyterExample]$ jupyter nbconvert --to script Hist-Plot.ipynb
[NbConvertApp] Converting notebook Hist-Plot.ipynb to script
[NbConvertApp] Writing 6075 bytes to Hist-Plot.py
[netid@cpu27 JupyterExample]$ ls
Hist-Plot.ipynb  Hist-Plot.py
```

# Create a Batch Script
Next, we need a batch script to execute our script. Create a file using ```touch Hist-Plot.slurm``` and open it in your favorite text editor. 
```
#!/bin/bash
#SBATCH --job-name=Hist-Plot
#SBATCH --time=00:01:00
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --account=hpcteam
#SBATCH --partition=standard

module load python/3.6
python3 Hist-Plot.py
```

# Submit Job
```
[netid@cpu27 JupyterExample]$ sbatch Hist-Plot.slurm 
Submitted batch job 288817
```

# Check Output
```
[netid@cpu27 JupyterExample]$ ls
Hist-Plot.ipynb  slurm-288817.out  Hist-Plot.png  Hist-Plot.py  Hist-Plot.slurm
```
<img src="check-files-in-ood.png" alt="check-files-in-ood" width="950"/>

<img src="view-png-in-ood.png" alt="view-png-in-ood.png" width="950"/>

<img src="batch-figure-output.png" alt="batch-figure-output" width="950"/>
