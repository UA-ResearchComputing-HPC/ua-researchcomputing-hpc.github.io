# Array Job With Text Filenames

## What problem does this help fix?
If you want to run multiple jobs where each opens a different file to analyze but the naming scheme isn't conducive to automating the process using simple array indices (i.e., 1.txt,2.txt,...)

## Script Example
[Click here to download example](Array-Read-Filenames.tar.gz)
### Submission Script
```
#!/bin/bash
#SBATCH --job-name=Array-Read-Filenames
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP
#SBATCH --array=1-4

CurrentFile="$( sed "${SLURM_ARRAY_TASK_ID}q;d" InputFiles )"
echo "JOB NAME: $SLURM_JOB_NAME, JOB ID: $SLURM_JOB_ID, EXAMPLE COMMAND: ./executable -o output${SLURM_ARRAY_TASK_ID} ${CurrentFile}"
```

### Input File
For this example, you'll want to have a file called InputFiles in your working directory. This will contain one filename per line. Contents:
```
SRR2309587.fastq
SRR3050489.fastq
SRR305356.fastq
SRR305p0982.fastq
```

## Script Breakdown
For each of the four subjobs, we'll make use of ```SLURM_ARRAY_TASK_ID``` to pull the line number (line numbers 1 to 4) from InputFiles:
```
CurrentFile="$( sed "${SLURM_ARRAY_TASK_ID}q;d" InputFiles )"
```
We will print a sample command that includes our filename to verify that everything is working as expected for demonstration purposes:
```
echo "JOB NAME: $SLURM_JOB_NAME, JOB ID: $SLURM_JOB_ID, EXAMPLE COMMAND: ./executable -o output${SLURM_ARRAY_TASK_ID} ${CurrentFile}"
```
To generate your own InputFile, you can either manually add your filenames or can automate the process, for example if you have all your files in a single location:
```
$ ls *fastq > InputFiles
```
## Script Submission Command:
```
(puma) [netid@junonia ~]$ sbatch Array-Read-Filenames.slurm 
Submitted batch job 1694071
```
## Output Files
Each of the subjobs in the array will output its own file of the form ```slurm-<job_id>_<array_id>.out``` as seen below:
```
(puma) [netid@junonia ~]$ ls *.out
slurm-1694071_1.out  slurm-1694071_2.out  slurm-1694071_3.out
slurm-1694071_4.out
```

## File Contents:
```
(puma) [netid@junonia ~]$ cat *.out | grep fastq
JOB NAME: Array-Read-Filenames, JOB ID: 1694072, EXAMPLE COMMAND: ./executable -o output1 SRR2309587.fastq
JOB NAME: Array-Read-Filenames, JOB ID: 1694073, EXAMPLE COMMAND: ./executable -o output2 SRR3050489.fastq
JOB NAME: Array-Read-Filenames, JOB ID: 1694074, EXAMPLE COMMAND: ./executable -o output3 SRR305356.fastq
JOB NAME: Array-Read-Filenames, JOB ID: 1694071, EXAMPLE COMMAND: ./executable -o output4 SRR305p0982.fastq
```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
