# Using an Anaconda Environment in a Batch Script

If you've ever initialized Anaconda in your account, you may have turned off Anaconda's auto-activation feature using:

```console
conda config --set auto_activate_base false
```

This keeps some Anaconda features from being present in your environment unless ```conda activate``` is explicitly used. We highly encourage doing this since it gives you more control over your environment 

## Setting up your environment
In this example, we'll use the popular [bioinformatics software Qiime2](https://docs.qiime2.org/2022.2/about/) as an example environment. Qiime2 comes with its own yml file that defines the and has [comprehensive install instructions available](https://docs.qiime2.org/2022.2/install/native/#install-qiime-2-within-a-conda-environment). 

#### Step 1: Start an interactive session
To get access to system software, you'll need to be on a compute need. These can be accessed by requesting [an interactive session](https://public.confluence.arizona.edu/display/UAHPC/Running+Jobs+with+SLURM#RunningJobswithSLURM-interactive-jobsInteractiveJobs) using the command ```interactive``` from a login node. 

#### Step 2: Initialize Anaconda (if necessary)

Once your session starts, load the anaconda module including version:

```console
[netid@cpu4 ~]$ module avail anaconda | grep anaconda
   anaconda/2020.02    anaconda/2020.11    anaconda/2022.05 (D)
[netid@cpu4 ~]$ module load anaconda/2022.05
```
If you have never used Anaconda in your account before, you'll need to initialize it using ```conda init bash```:

```console
[netid@cpu4 ~]$ conda init bash
no change     /opt/ohpc/pub/apps/anaconda/2022.05/condabin/conda
no change     /opt/ohpc/pub/apps/anaconda/2022.05/bin/conda
no change     /opt/ohpc/pub/apps/anaconda/2022.05/bin/conda-env
no change     /opt/ohpc/pub/apps/anaconda/2022.05/bin/activate
no change     /opt/ohpc/pub/apps/anaconda/2022.05/bin/deactivate
no change     /opt/ohpc/pub/apps/anaconda/2022.05/etc/profile.d/conda.sh
no change     /opt/ohpc/pub/apps/anaconda/2022.05/etc/fish/conf.d/conda.fish
no change     /opt/ohpc/pub/apps/anaconda/2022.05/shell/condabin/Conda.psm1
no change     /opt/ohpc/pub/apps/anaconda/2022.05/shell/condabin/conda-hook.ps1
no change     /opt/ohpc/pub/apps/anaconda/2022.05/lib/python3.9/site-packages/xontrib/conda.xsh
no change     /opt/ohpc/pub/apps/anaconda/2022.05/etc/profile.d/conda.csh
modified      /home/u00/netid/.bashrc

==> For changes to take effect, close and re-open your current shell. <==

[netid@cpu4 ~]$ source ~/.bashrc
(base) [netid@cpu4 ~]$ 
```

The ```source ~/.bashrc``` keeps you from having to close and re-open your current shell so you can ignore these instructions from anaconda. 

#### Step 3: Turn off auto-activiation feature
By default, Anaconda activates itself if your environment whenever you log in. This is not ideal since it can result in a number of errors when working with other software packages
