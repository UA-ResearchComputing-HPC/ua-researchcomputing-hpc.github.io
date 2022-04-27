# Overview

[![](/Images/Download-Button.png)](r-plotting.tar.gz)

You don't need to be in an interactive session to view and save figures. In a batch script you can save your figure to a specified directory (the default is your working directory), give it a custom name, control your image quality and dimensions, and choose your output format (e.g., pdf, png, jpg, etc.). An example is included below.

# Submission Script

To run your R script as a batch job, use ```Rscript``` instead of ```R``` which is used for interactive sessions. 
```console
#!/bin/bash
#SBATCH --job-name=R-Plotting-Job
#SBATCH --ntasks=1
#SBATCH --nodes=1 
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

module load R/4.0.0
Rscript example.r
```

# R Script
```R
print ("In R Script. Plotting...")
x <- seq(-pi,pi,0.1)
png("rplot.png") 
plot(x, sin(x))
dev.off()
```

# Job Submission
```console
[netid@wentletrap ~]$ sbatch submit_r_script.slurm 
Submitted batch job 53337
```
# Output
```console
[netid@wentletrap ~]$ ls
slurm-53337.out  rplot.png  example.r  submit_r_script.slurm
[netid@wentletrap ~]$ cat slurm-53337.out 
[1] "In R Script. Plotting..."
null device 
          1 
```
![](/R-Examples/Plotting-In-R/rplot.png)

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
