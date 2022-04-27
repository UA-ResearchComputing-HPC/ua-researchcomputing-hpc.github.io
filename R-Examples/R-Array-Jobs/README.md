# Overview

[![](/Images/Download-Button.png)](r_array_example.tar.gz)

In this example, we'll create an R script that generates 1000 randomized 1s and 0s, stores them as a dataframe, then saves the dataframe to an output file. We'll run this R script as an array job to simulate what a researcher might do if they were performing multiple independent simulations using the same R script.

Since we're using the same file for each simulation, if we were to create a single static output filename for our ```save()``` command, each simulation would overwrite the one that came before it. To deal with this issue, we'll make use of the SLURM environment variable ```$SLURM_ARRAY_TASK_ID``` to differentiate them. 

As a note: if you haven't worked with array jobs before, [Basic Array Jobs](https://ua-researchcomputing-hpc.github.io/Array-and-Parallel/Basic-Array-Job/) is a great place to get started and provides more in-depth information on their use. 


# R Script
This script is designed to be excuted using the following syntax:
```console
Rscript r_array_example.R $SLURM_ARRAY_TASK_ID
```
The use of ```commandArgs()``` is to pull in that task ID to our R script so that we can use it in our output filenames.
```R
# We'll pull in any command line arguments used in executing this script.
# This is to grab the SLURM_ARRAY_TASK_ID that's associated with this particular
# array subjob. We'll use this integer to differentiate save files so multiple
# simulations don't overwrite one another.
args<-commandArgs(TRUE)

# An example of a workflow is included below

# Generate sample of 1000 random 0s and 1s
a <-sample(0:1, 1000, rep = TRUE)
# Save as data.fame
df <- data.frame(replicate(10,sample(0:1,1000,rep=TRUE)))

# Now we'll save our dataframe to a unique filename. If we run our job with:
# Rscript r_array_example.R $SLURM_ARRAY_TASK_ID
# then args[1] will be set to the job's unique integer. Using paste, we can
# add this into our save filename.
filename <- paste("random_sample_df_run", args[1], ".rda", sep = "", collapse = NULL)

# For demonstration purposes, we'll print out the expected filename
sprintf("Simulation complete. Saving dataframe to filename: %s", filename)

save(df, file = filename)
```

# Submission Script
```bash
#!/bin/bash
#SBATCH --account=your_group_here
#SBATCH --partition=standard
#SBATCH --time=00:01:00
#SBATCH --ntasks=1
#SBATCH --nodes=1
#SBATCH --array=1-2

module load R/4.1.0

Rscript save_example.R $SLURM_ARRAY_TASK_ID
```

# Job Submission
```console
[netid@cpu4 ~]$ sbatch r_array_example.slurm 
Submitted batch job 260764
```

# Output
```console
[netid@cpu4 ~]$ cat slurm-260764_* | grep Simulation
[1] "Simulation complete. Saving dataframe to filename: random_sample_df_run1.rda"
[1] "Simulation complete. Saving dataframe to filename: random_sample_df_run2.rda"
[netid@cpu4 ~]$ ls random_sample_df_run*
random_sample_df_run1.rda  random_sample_df_run2.rda
```

*************
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
