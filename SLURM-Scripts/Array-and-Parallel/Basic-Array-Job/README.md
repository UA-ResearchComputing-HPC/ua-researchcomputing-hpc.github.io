# Basic Array Job
Array jobs are used to execute the same script multiple times with different input.

## What problem does this help fix?
To execute multiple analyses, a user may be tempted to submit jobs with a scripted loop, e.g.:
```
$ for i in $( seq 1 10 ); do sbatch script.slurm <submission options> ; done
```
This is *not* a good solution because it submits too many jobs too quickly and can overload the scheduler. Instead, an array job can be used to achieve the same ends. 

## Script Example

[Click here to download example](basic_array_job.tar.gz)

 ```
#!/bin/bash
#SBATCH --output=Sample_SLURM_Job-%a.out
#SBATCH --ntasks=1             
#SBATCH --mem=1gb                    
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=<group_name>   
#SBATCH --array 1-5
 
# SLURM Inherits your environment. cd $SLURM_SUBMIT_DIR not needed

echo "./sample_command input_file_${SLURM_ARRAY_TASK_ID}.in"
 ```
 
 # Script Breakdown

What differentiates the script above is the use of the ```--array``` directive. This is what tells SLURM that you're submitting an array. Following this flag, you will specify the number of jobs you wish to run. In this case, we're running 5:
 ```
 #SBATCH --array 1-5
 ```
 Each job in the array has its own associated environment variable ```$SLURM_ARRAY_TASK_ID``` that is used to differentiate the subjobs. To demonstrate how we can use each of these to read in different input files, we'll print a sample command:
 ```
 echo "./sample_command input_file_${SLURM_ARRAY_TASK_ID}.in"
 ```
 
 ## Script Submission Command
 ```
 $ sbatch basic_array_job.slurm
 Submitted batch job 361951
 ```
 
 ## Output Files
*Important:* When submitting jobs with named output files (i.e. with the line #SBATCH -o=Job.out) as arrays, SLURM will write every array element to that filename leaving you with only the output of the last completed job in the array. Use one of the following SLURM directives in your script to prevent this behavior:

1. Differentiate output files using array indices. Similar to PBS default. For example:
```
#SBATCH --output=Job-%a.out
```
2. Append the output from all tasks in an array to the same output file. Warning: if a file exists with that name prior to running your job, the output will be appended to that file
```
#SBATCH --open-mode=append
```

You may also omit the option to name your output files and SLURM with automatically generate names of the form ```slurm-<job_id>-<array_id>.out```. For more information on SLURM naming schemes, see our [Online Documentation](https://public.confluence.arizona.edu/pages/viewpage.action?pageId=93160866#RunningJobswithSLURM(Puma)-SLURMOutputFilenamePatterns). 

In our case, the output files generated are:
```
$ ls -lh *.out
-rw-r--r-- 1 netid group 33 Jan  4 16:01 Sample_SLURM_Job-1.out
-rw-r--r-- 1 netid group 33 Jan  4 16:01 Sample_SLURM_Job-2.out
-rw-r--r-- 1 netid group 33 Jan  4 16:01 Sample_SLURM_Job-3.out
-rw-r--r-- 1 netid group 33 Jan  4 16:01 Sample_SLURM_Job-4.out
-rw-r--r-- 1 netid group 33 Jan  4 16:01 Sample_SLURM_Job-5.out
```

## File Contents
Below is a concatenation of all the output files. Notice how the array indices function to differentiate the input files in the same command.
```
$ cat *.out
./sample_command input_file_1.in
./sample_command input_file_2.in
./sample_command input_file_3.in
./sample_command input_file_4.in
./sample_command input_file_5.in
```
----------
[back](../)
