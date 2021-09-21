# Overview

# Submission Script

# R Script

# Job Submission
```
[netid@wentletrap ~]$ sbatch submit_r_script.slurm 
Submitted batch job 53329
```
# Output
```
[netid@wentletrap ~]$ ls
slurm-53329.out  example.r  submit_r_script.slurm
[netid@wentletrap ~]$ cat slurm-53329.out 
[1] "In R Script. Plotting..."
null device 
          1 
```
![](./rplot.png)

*****

[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
