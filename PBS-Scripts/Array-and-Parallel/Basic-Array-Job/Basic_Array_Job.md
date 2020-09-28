# Basic Array Job

Array jobs are used to execute the same script multiple times with different input. 

## What problem does this help fix?
To execute multiple analyses, a user may be tempted to submit jobs with a scripted loop, e.g.:

```
$ for i in $( seq 1 10 ); do qsub script.pbs <submission options> ; done
```

This is **not** a good solution. This submits too many too quickly and overloads the scheduler. Instead, an array job can be used to achive the same ends. 

## Script Example
[Click to download script](Basic_Array_Job.pbs)
_This script was written and executed on elgato, but can be run on Ocelote with minor modifications to the resource requests_

```
#PBS -q standard
#PBS -W group_list=<group_name>
#PBS -N Basic_Array_Job
#PBS -l select=1:ncpus=1:mem=4gb:pcmem=4gb
#PBS -l walltime=00:00:30
#PBS -j oe
#PBS -J 1-10

echo "Job Name: $PBS_JOBID, Reading File: input_file_${PBS_ARRAY_INDEX}.txt"
```

## Script Breakdown

What differentiates the script above is the use of the ```-J``` directive. This is what tells PBS that you're submitting an array. Following this flag, you will specify the number of jobs you wish to run. In this case, we're running 10:

```
#PBS -J 1-10
```

Each job in the array has its own associated environment variable ```$PBS_ARRAY_INDEX``` that can be used to differentiate subjobs. To demonstrate how we can use each these to read in different input files, we'll print a sample command:
```
echo "Job Name: $PBS_JOBID, Reading File: input_file_${PBS_ARRAY_INDEX}.txt"
```

## Script Submission Command
```
$ qsub Basic_Array_Job.pbs
```

## Output Files
Each of the subjobs in the array will produce its own output file of the form ```<job_name>.o<job_ID>.<array_index>``` as seen below:

```
ls -lh
total 0
-rw------- 1 netid netid  62 Feb 18 10:00 Basic_Array_Job.o87274.1
-rw------- 1 netid netid  64 Feb 18 10:00 Basic_Array_Job.o87274.10
-rw------- 1 netid netid  62 Feb 18 10:00 Basic_Array_Job.o87274.2
-rw------- 1 netid netid  62 Feb 18 10:00 Basic_Array_Job.o87274.3
-rw------- 1 netid netid  62 Feb 18 10:00 Basic_Array_Job.o87274.4
-rw------- 1 netid netid  62 Feb 18 10:00 Basic_Array_Job.o87274.5
-rw------- 1 netid netid  62 Feb 18 10:00 Basic_Array_Job.o87274.6
-rw------- 1 netid netid  62 Feb 18 10:00 Basic_Array_Job.o87274.7
-rw------- 1 netid netid  62 Feb 18 10:00 Basic_Array_Job.o87274.8
-rw------- 1 netid netid  62 Feb 18 10:00 Basic_Array_Job.o87274.9
```

## File Contents

Below is a concatenation of all the job's output files. Notice how the array indices function to differentiate the input files in the sample command. 

```
$ cat Basic_Array_Job.o87274.*
Job Name: 87274[1].elgato-adm, Reading File: input_file_1.txt
Job Name: 87274[10].elgato-adm, Reading File: input_file_10.txt
Job Name: 87274[2].elgato-adm, Reading File: input_file_2.txt
Job Name: 87274[3].elgato-adm, Reading File: input_file_3.txt
Job Name: 87274[4].elgato-adm, Reading File: input_file_4.txt
Job Name: 87274[5].elgato-adm, Reading File: input_file_5.txt
Job Name: 87274[6].elgato-adm, Reading File: input_file_6.txt
Job Name: 87274[7].elgato-adm, Reading File: input_file_7.txt
Job Name: 87274[8].elgato-adm, Reading File: input_file_8.txt
Job Name: 87274[9].elgato-adm, Reading File: input_file_9.txt
```
