# Array Jobs With Multiple Input Parameters

Demonstration of how to feed parameters to different subjobs in an array by pulling them from an input file, e.g.:

* job 1: ```./executable job1_variable1 job1_variable2 job1_variable3```
* job 2: ```./executable job2_variable1 job2_variable2 job2_variable3```
* etc...


## Script Example
[Click here to download example](Sample_Array_Input_Parameters.tar.gz)

#### PBS File
```
#!/bin/bash
#PBS -q standard
#PBS -W group_list=<GROUP NAME>
#PBS -j oe
#PBS -N Sample_Array_Input_Parameters
#PBS -l select=1:ncpus=1:mem=6gb:pcmem=6gb
#PBS -l walltime=00:00:10
#PBS -J 1-10

cd $PBS_O_WORKDIR
read first_parameter second_parameter third_parameter <<< $( sed "${PBS_ARRAY_INDEX}q;d" input_parameters )
echo "Job ID: $PBS_JOBID ; Host Node : $HOSTNAME ; Sample Command : ./executable $first_parameter $second_parameter $third_parameter"
```
#### Input File
```
job1_param1 job1_param2 job1_param3
job2_param1 job2_param2 job2_param3
job3_param1 job3_param2 job3_param3
job4_param1 job4_param2 job4_param3
job5_param1 job5_param2 job5_param3
job6_param1 job6_param2 job6_param3
job7_param1 job7_param2 job7_param3
job8_param1 job8_param2 job8_param3
job9_param1 job9_param2 job9_param3
job10_param1 job10_param2 job10_param3
```

## Script Breakdown

The line number that corresponds with the job's PBS_ARRAY_INDEX is read in an parsed to extract the input parameters. The parameters here should be space-delimited. There are three per line that are assigned to the variables on the left.
```
read first_parameter second_parameter third_parameter <<< $( sed "${PBS_ARRAY_INDEX}q;d" input_parameters )
```

A sample command is printed along with job information for demonstration purposes:

```
echo "Job ID: $PBS_JOBID ; Host Node : $HOSTNAME ; Sample Command : ./executable $first_parameter $second_parameter $third_parameter"
```


## Script Submission Command

```
$ qsub Sample_Array_Input_Parameters
3032549[].head1.cm.cluster
```

## Output Files

```
$ ls -lh *.o*
-rw------- 1 netid group 273 Feb 27 14:12 Sample_Array_Input_Parameters.o3032549.1
-rw------- 1 netid group 149 Feb 27 14:45 Sample_Array_Input_Parameters.o3032549.10
-rw------- 1 netid group 124 Feb 27 14:48 Sample_Array_Input_Parameters.o3032549.2
-rw------- 1 netid group 124 Feb 27 14:48 Sample_Array_Input_Parameters.o3032549.3
-rw------- 1 netid group 124 Feb 27 14:48 Sample_Array_Input_Parameters.o3032549.4
-rw------- 1 netid group 124 Feb 27 14:48 Sample_Array_Input_Parameters.o3032549.5
-rw------- 1 netid group 124 Feb 27 14:48 Sample_Array_Input_Parameters.o3032549.6
-rw------- 1 netid group 124 Feb 27 14:48 Sample_Array_Input_Parameters.o3032549.7
-rw------- 1 netid group 123 Feb 27 14:48 Sample_Array_Input_Parameters.o3032549.8
-rw------- 1 netid group 123 Feb 27 14:48 Sample_Array_Input_Parameters.o3032549.9
```

## File Contents

```
$ cat *.o*
Job ID: 3032549[1].head1.cm.cluster ; Host Node : i4n22 ; Sample Command : ./executable job1_param1 job1_param2 job1_param3
Job ID: 3032549[10].head1.cm.cluster ; Host Node : i8n3 ; Sample Command : ./executable job10_param1 job10_param2 job10_param3
Job ID: 3032549[2].head1.cm.cluster ; Host Node : i5n21 ; Sample Command : ./executable job2_param1 job2_param2 job2_param3
Job ID: 3032549[3].head1.cm.cluster ; Host Node : i5n21 ; Sample Command : ./executable job3_param1 job3_param2 job3_param3
Job ID: 3032549[4].head1.cm.cluster ; Host Node : i5n21 ; Sample Command : ./executable job4_param1 job4_param2 job4_param3
Job ID: 3032549[5].head1.cm.cluster ; Host Node : i5n21 ; Sample Command : ./executable job5_param1 job5_param2 job5_param3
Job ID: 3032549[6].head1.cm.cluster ; Host Node : i5n21 ; Sample Command : ./executable job6_param1 job6_param2 job6_param3
Job ID: 3032549[7].head1.cm.cluster ; Host Node : i5n21 ; Sample Command : ./executable job7_param1 job7_param2 job7_param3
Job ID: 3032549[8].head1.cm.cluster ; Host Node : i8n3 ; Sample Command : ./executable job8_param1 job8_param2 job8_param3
Job ID: 3032549[9].head1.cm.cluster ; Host Node : i8n3 ; Sample Command : ./executable job9_param1 job9_param2 job9_param3
```
