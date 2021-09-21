# Overview
To run an R job as a batch submission, you can include all of your R commands in a single file and execute it with ```Rscript```.

# Submission Script
[Click here to download example](basic-r-job.tar.gz)
```
#!/bin/bash
#SBATCH --job-name=R-Plotting-Job
#SBATCH --ntasks=1
#SBATCH --nodes=1 
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

module load R/4.0.0
Rscript hello_world.r
```

# R Script
```
#!/usr/bin/env Rscript

hello_string <- "Hello World! "
print (hello_string, quote=FALSE)
```

# Job Submission
```
[netid@wentletrap ~]$ sbatch submit_r_script.slurm 
Submitted batch job 53341
```

# Output
```
[netid@wentletrap ~]$ cat slurm-53341.out 
[1] Hello World! 
```


------------

[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
