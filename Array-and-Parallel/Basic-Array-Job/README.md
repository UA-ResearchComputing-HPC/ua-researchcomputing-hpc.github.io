# Basic Array Job
Array jobs are used to execute the same script multiple times with different input.
## What problem does this help fix?
To execute multiple analyses, a user may be tempted to submit jobs with a scripted loop, e.g.:
```
for i in $( seq 1 10 ); do sbatch script.slurm <submission options> ; done
```
 This isn't a good solution because it submits too many jobs too quickly and overloads the scheduler. Instead, an array job can be used to achieve the same ends.

## Script Example
 
 [Click here to download example](Basic-Array-Job.tar.gz)
 ```
#!/bin/bash
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP
#SBATCH --array 1-5

echo "./sample_command input_file_${SLURM_ARRAY_TASK_ID}.in"
 ```
 
## Script Breakdown
 
What differentiates the script above from standard submissions is the ```--array``` directive. This is what tells SLURM  that you're submitting an array. Following this flag, you will specify the number of jobs you wish to run. In this case, we're running 5:
```
#SBATCH --array 1-5
```
Each job in the array has its own associated environment variable ```SLURM_ARRARY_TASK_ID``` that can be used to differentiate subjobs. To demonstrate how we can use each of these to read in different input files, we'll print a sample command:
```
echo "./sample_command input_file_${SLURM_ARRAY_TASK_ID}.in"
```

## Script Submission
```
(ocelote) [netid@junonia ~]$ sbatch basic_array_job.slurm 
Submitted batch job 73958
```
## Output Files
Each of the subjobs in the array will produce its own output file of the form ```slurm_jobid_arrayid.out``` as seen below:
```
(ocelote) [netid@junonia ~]$ ls
slurm-73958_1.out  slurm-73958_2.out      slurm-73958_3.out  slurm-73958_4.out
slurm-73958_5.out  basic_array_job.slurm
```
For more information on naming SLURM files, see our [online documentation](https://public.confluence.arizona.edu/display/UAHPC/Running+Jobs+with+SLURM#RunningJobswithSLURM-SLURMOutputFilenamePatterns)

## File Contents:
Below is a concatenation of the job's output files. Notice how the array indices function to differentiate the input files in the sample command:
```
(ocelote) [netid@junonia ~]$ cat slurm-73958_* | grep sample
./sample_command input_file_1.in
./sample_command input_file_2.in
./sample_command input_file_3.in
./sample_command input_file_4.in
./sample_command input_file_5.in
```

*************
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
