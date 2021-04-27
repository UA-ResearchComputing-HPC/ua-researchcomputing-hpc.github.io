# Array Jobs and Parallel Tasks
## About
Figuring out effective ways to submit large numbers of jobs can be frustrating. The script examples below are is designed to help implement different techniques to run jobs as arrays or in parallel.

Each script is designed to print commands and information specific to that job for better visualization/demonstration purposes.

HPC users should be able to run each script with minor modifications (i.e. adding their PI’s group name) to experiment.

# Scripts
## Array Jobs
An array job is a way to easily submit multiple jobs using the same SLURM script with a single ```sbatch```. When an array job is submitted, it will create multiple subjobs, each using the same SLURM directives and same commands as written in the main script.

### [Basic Array Job](Basic-Array-Job)
Submit multiple jobs using the same script with only one ```sbatch``` command.

### [Array Jobs with Text Filenames](Array-Read-Filenames)
Run multiple jobs where each opens a different file but the naming scheme isn't conducive to automating the process using simple array indices as shown in Basic_Array_Job (i.e. 1.txt, 2.txt, ...).

-------------
[back](../)
