# Array Jobs and Parallel Tasks

## About
Figuring out effective ways to submit large numbers of jobs can be frustrating. The script examples below are is designed to help implement different techniques to run jobs as arrays or in parallel.

Each script is designed to print commands and information specific to that job for better visualization/demonstration purposes. 

HPC users should be able to run each script with minor modifications (i.e. adding their PI's group name) to experiment.

# Scripts

## Array Jobs

An array job is a way to easily submit multiple jobs using the same PBS script with a single ```qsub```. When an array job is submitted, it will create multiple subjobs, each using the same PBS directives and same commands as written in the main script.


### [Basic Array Job](Basic-Array-Job)
If you have never used array jobs before, start here.

Submit multiple jobs using the same script with only one ```qsub``` command.


### [Array Jobs with Text Filenames](Array-Read-Filenames)

Run multiple jobs where each opens a different file but the naming scheme isn't conducive to automating the process using simple array indices as shown in Basic_Array_Job (i.e. 1.txt, 2.txt, ...). This technique is better for smaller array jobs.

### [Sample Array With Input Parameters](Array-Read-Parameters)

Run multiple jobs where each uses a distinct combination of input parameters. Very similar to reading text filenames above. This technique is better for smaller array jobs.



## Parallel Jobs

### [Basic GNU Parallel Job](Basic-Parallel-Job)

Run multiple tasks on a single node without using an array job. Tasks that are not run immediately due to space restrictions are queued behind the running jobs and are automatically executed as space becomes available.

## Combining Parallel and Array Jobs

### [Array Job with GNU Parallel](Array-and-Parallel)

This script is roughly an extension of Sample_Array_Job.pbs, but with the additional step of parallelizing tasks within each subjob. The parallelization was accomplished using GNU Parallel (https://www.gnu.org/software/parallel/). This allows for running a tremendous number of tasks.

---
[back](../)
