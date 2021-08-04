# Basic Array Job

Array jobs are used to execute the same script multiple times with different input. 

## What problem does this help fix?
To execute multiple analyses, a user may be tempted to submit jobs with a scripted loop, e.g.:

```
$ for i in $( seq 1 10 ); do qsub script.pbs <submission options> ; done
```

This is **not** a good solution. This submits too many too quickly and overloads the scheduler. Instead, an array job can be used to achive the same ends. 

## Script Example
[Click here to download example](Basic_Array_Job.tar.gz)

```
#PBS -q standard
#PBS -j oe
#PBS -W group_list=<group_name>
#PBS -N Basic_Array_Job
#PBS -l select=1:ncpus=1:mem=6gb:pcmem=6gb
#PBS -l walltime=00:00:30
#PBS -j oe
#PBS -J 1-10

echo "Job Name: $PBS_JOBID, Reading File: input_file_${PBS_ARRAY_INDEX}.txt"
```

##  Script Breakdown

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
3538538[].head1.cm.cluster
```

## Output Files
Each of the subjobs in the array will produce its own output file of the form ```<job_name>.o<job_ID>.<array_index>``` as seen below:

```
$ ls -lh *.o*
-rw------- 1 netid group 70 Sep 29 12:37 Basic_Array_Job.o3538538.1
-rw------- 1 netid group 72 Sep 29 12:37 Basic_Array_Job.o3538538.10
-rw------- 1 netid group 70 Sep 29 12:37 Basic_Array_Job.o3538538.2
-rw------- 1 netid group 70 Sep 29 12:37 Basic_Array_Job.o3538538.3
-rw------- 1 netid group 70 Sep 29 12:37 Basic_Array_Job.o3538538.4
-rw------- 1 netid group 70 Sep 29 12:37 Basic_Array_Job.o3538538.5
-rw------- 1 netid group 70 Sep 29 12:37 Basic_Array_Job.o3538538.6
-rw------- 1 netid group 70 Sep 29 12:37 Basic_Array_Job.o3538538.7
-rw------- 1 netid group 70 Sep 29 12:37 Basic_Array_Job.o3538538.8
-rw------- 1 netid group 70 Sep 29 12:37 Basic_Array_Job.o3538538.9
```

## File Contents

Below is a concatenation of all the job's output files. Notice how the array indices function to differentiate the input files in the sample command. 

```
$ cat *.o*
Job Name: 3538538[1].head1.cm.cluster, Reading File: input_file_1.txt
Job Name: 3538538[10].head1.cm.cluster, Reading File: input_file_10.txt
Job Name: 3538538[2].head1.cm.cluster, Reading File: input_file_2.txt
Job Name: 3538538[3].head1.cm.cluster, Reading File: input_file_3.txt
Job Name: 3538538[4].head1.cm.cluster, Reading File: input_file_4.txt
Job Name: 3538538[5].head1.cm.cluster, Reading File: input_file_5.txt
Job Name: 3538538[6].head1.cm.cluster, Reading File: input_file_6.txt
Job Name: 3538538[7].head1.cm.cluster, Reading File: input_file_7.txt
Job Name: 3538538[8].head1.cm.cluster, Reading File: input_file_8.txt
Job Name: 3538538[9].head1.cm.cluster, Reading File: input_file_9.txt
```

[back](../)
