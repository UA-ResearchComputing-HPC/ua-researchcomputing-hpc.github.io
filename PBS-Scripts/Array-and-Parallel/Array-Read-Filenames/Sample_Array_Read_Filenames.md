# Array Job With Text Filenames

## What problem does this help fix?

If you want to run multiple jobs where each opens a different file to analyze but the naming scheme isn't conducive to automating the process using simple array indices (i.e. 1.txt, 2.txt, ...). 

If you're unfamiliar with array jobs, it's recommended you read [Basic Array Jobs](../Basic-Array-Job/Basic_Array_Job.md) first.

## Script Example

#### PBS File

```
#!/bin/bash
#PBS -q standard
#PBS -W group_list=<group name>
#PBS -N Sample_Array_Read_Filenames
#PBS -l select=1:ncpus=1:mem=6gb:pcmem=6gb
#PBS -l walltime=00:00:30
#PBS -J 1-4

cd $PBS_O_WORKDIR
CurrentFile="$( sed "${PBS_ARRAY_INDEX}q;d" InputFiles )"
echo "JOB NAME: $PBS_JOBID, EXAMPLE COMMAND: ./executable -o output${PBS_ARRAY_INDEX} ${CurrentFile}"
```

#### Input File
For this example, you will want to have a file called InputFiles in your PBS working directory. This file will contain one filename per line. You can download the example file by [clicking here](InputFiles). Input file contents:
```
SRR2309587.fastq
SRR3050489.fastq
SRR305356.fastq
SRR305p0982.fastq
```

## Script Breakdown

The script is of the same form as Basic Array Job where ```-J 1-4``` tells PBS we want to submit four jobs as an array. 

For each of these jobs, we'll make use of ```$PBS_ARRAY_INDEX``` to pull the line number from InputFiles that corresponds to the index number: 
```
CurrentFile="$( sed "${PBS_ARRAY_INDEX}q;d" InputFiles )"
```
We will print a sample command that includes our filename to verify that everything is working as expected for demonstration purposes:
```
echo "JOB NAME: $PBS_JOBID, EXAMPLE COMMAND: ./executable -o output${PBS_ARRAY_INDEX} ${CurrentFile}"
```

To generate your own InputFile, either manually add your filenames or, if you have all your files in a single location, you can use ```ls``` to pipe them into a text document:
```
$ ls *.fastq > InputFiles
```

## Script Submission Command
```
$ qsub Sample_Array_Read_Filenames.pbs 
3029881[].head1.cm.cluster # Job ID specific to my case
```

## Output Files
Each of the subjobs in the array will output its own file of the form ```<job_name>.o<job_ID>.<array_index>``` as seen below:
```
$ ls -lh Sample_Array_Read_Filenames.o3029881.*
-rw------- 1 netid group 97 Feb 26 11:28 Sample_Array_Read_Filenames.o3029881.1
-rw------- 1 netid group 97 Feb 26 11:28 Sample_Array_Read_Filenames.o3029881.2
-rw------- 1 netid group 96 Feb 26 11:28 Sample_Array_Read_Filenames.o3029881.3
-rw------- 1 netid group 98 Feb 26 11:28 Sample_Array_Read_Filenames.o3029881.4
```

## File Contents

```
$ cat Sample_Array_Read_Filenames.o*
JOB NAME: 3029881[1].head1.cm.cluster, EXAMPLE COMMAND: ./executable -o output1 SRR2309587.fastq
JOB NAME: 3029881[2].head1.cm.cluster, EXAMPLE COMMAND: ./executable -o output2 SRR3050489.fastq
JOB NAME: 3029881[3].head1.cm.cluster, EXAMPLE COMMAND: ./executable -o output3 SRR305356.fastq
JOB NAME: 3029881[4].head1.cm.cluster, EXAMPLE COMMAND: ./executable -o output4 SRR305p0982.fastq
```
