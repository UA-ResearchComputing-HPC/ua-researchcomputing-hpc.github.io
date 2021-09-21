# Overview
You don't need to be in an interactive session to view and save figures. In a batch script you can save your figure to a specified directory (the default is your working directory), give it a custom name, control your image quality and dimensions, and choose your output format (e.g., pdf, png, jpg, etc.). An example is included below.

# Submission Script
[Click here to download example](r-plotting.tar.gz)
To run your R script as a batch job, use ```Rscript``` instead of ```R``` which is used for interactive sessions. 
```
#!/bin/bash
#SBATCH --job-name=R-Plotting-Job
#SBATCH --ntasks=1
#SBATCH --nodes=1 
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

module load R
Rscript example.r
```

# R Script
```
print ("In R Script. Plotting...")
x <- seq(-pi,pi,0.1)
png("rplot.png") 
plot(x, sin(x))
dev.off()
```

# Job Submission
```
[netid@wentletrap ~]$ sbatch submit_r_script.slurm 
Submitted batch job 53335
```
# Output
```
[netid@wentletrap ~]$ ls
slurm-53335.out  rplot.png  example.r  submit_r_script.slurm
[netid@wentletrap ~]$ cat slurm-53335.out 
[1] "In R Script. Plotting..."
null device 
          1 
```
![](/R-Examples/Plotting-In-R/rplot.png)

*****

[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
