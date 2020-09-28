# Array Jobs With Text Filenames

Run multiple jobs where each opens a different file but the naming scheme isn't conducive to automating the process using simple array indices as shown in Basic_Array_Job (i.e. 1.txt, 2.txt, ...).

## Script Breakdown

Standard PBS Directives.

```
#!/bin/bash

#PBS -q standard
#PBS -W group_list=<GROUP NAME>

#PBS -N Array_Job_Input_Parameters

#PBS -l select=1:ncpus=1:mem=6gb:pcmem=6gb
#PBS -l walltime=00:00:10
```

Asks for an array job with 10 elements:
```
#PBS -J 1-10
```

Change to directory where pbs file is located. The input parameters file should be here, wherever you're navigating, or its path should be specified.
```
cd $PBS_O_WORKDIR
```

We read in the line number that corresponds with the job's PBS_ARRAY_INDEX. The parameters here should be space-delimited. There are three parameters per line that are assigned to the variables on the left.
```
read first_parameter second_parameter third_parameter <<< $( sed "${PBS_ARRAY_INDEX}q;d" input_parameters )
```

Prints a possible command the user could execute as well as other information about the job

```
echo "Job ID: $PBS_JOBID ; Host Node : $HOSTNAME ; Sample Command : ./executable $first_parameter $second_parameter $third_parameter"
```



## Script Submission Command

```
$ qsub Array_Job_Input_Parameters.pbs
3032549[].head1.cm.cluster
```

## Output Files

```
$ ls -lh Array_Job_Input_Parameters.o*
-rw------- 1 netid group_name 273 Feb 27 14:12 Array_Job_Input_Parameters.o3032549.1
-rw------- 1 netid group_name 149 Feb 27 14:45 Array_Job_Input_Parameters.o3032549.10
-rw------- 1 netid group_name 124 Feb 27 14:48 Array_Job_Input_Parameters.o3032549.2
-rw------- 1 netid group_name 124 Feb 27 14:48 Array_Job_Input_Parameters.o3032549.3
-rw------- 1 netid group_name 124 Feb 27 14:48 Array_Job_Input_Parameters.o3032549.4
-rw------- 1 netid group_name 124 Feb 27 14:48 Array_Job_Input_Parameters.o3032549.5
-rw------- 1 netid group_name 124 Feb 27 14:48 Array_Job_Input_Parameters.o3032549.6
-rw------- 1 netid group_name 124 Feb 27 14:48 Array_Job_Input_Parameters.o3032549.7
-rw------- 1 netid group_name 123 Feb 27 14:48 Array_Job_Input_Parameters.o3032549.8
-rw------- 1 netid group_name 123 Feb 27 14:48 Array_Job_Input_Parameters.o3032549.9
```
