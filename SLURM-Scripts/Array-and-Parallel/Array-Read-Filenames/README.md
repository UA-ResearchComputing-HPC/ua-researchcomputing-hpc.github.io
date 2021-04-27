# Array Job with Text Filenames
## What problem does this help fix?
If you want to run multiple jobs where each opens a different file to analyze but the naming scheme isn't conducive to automating the process using simple array indices (e.g. 1.txt, 2.txt, ...)

If you're unfamiliar with array jobs, it's recommended you read [Basic Array Jobs](../Basic-Array-Job) first.

## Script Example
[Click here to download example]()

### SLURM File
 ```bash
 #!/bin/bash
#SBATCH --partition=standard
#SBATCH --account=<YOUR_GROUP>
#SBATCH -o %x_%A.out
#SBATCH --open-mode=append
#SBATCH --ntasks=1
#SBATCH --mem-per-cpu=4gb
#SBATCH --time=00:01:00
#SBATCH --array 1-4

### Pull filename from line number = SLURM_ARRAY_TASK_ID
CurrentFastaFile="$( sed "${PBS_ARRAY_INDEX}q;d" InputFiles )"

### Prints job number and possible command for demonstration purposes
echo "JOB ID: $SLURM_JOB_ID, EXAMPLE COMMAND: ./executable -o output${SLURM_ARRAY_TASK_ID} ${CurrentFastaFile}"
```
### Input File
For this example, you will want to have a file called InputFiles in your SLURM working directory. This will contain one filename per line. Contents:
```bash
SRR2309587.fastq
SRR3050489.fastq
SRR305356.fastq
SRR305p0982.fastq
```

## Script Breakdown
The script is of the same for as Basic Array Job where ```-J 1-4``` tells SLURM we want to submit four jpbs as an array.

For each of these jobs, we'll make use of ```$SLURM_ARRAY_TASK_ID``` to pull the line number from InputFiles that corresponds to the index number:
```bash
CurrentFile="$( sed "${SLURM_ARRAY_TASK_ID}q;d" InputFiles )"
```
We will print a sample commands that includes our filename to verify that everything is working as expected for demonstration purposes:
```bash
echo "JOB ID: $SLURM_JOB_ID, EXAMPLE COMMAND: ./executable -o output${SLURM_ARRAY_TASK_ID} ${CurrentFastaFile}"
```
To generate your own InputFile, either manually add your filenames, or, if you have all your files in a single location, you can use ```ls``` to pipe them into a text document, e.g.:
```bash
$ ls *.fasta >InputFiles
```

## Script Submission Command
```bash
$ sbatch Sample_Array_Read_Filenames.slurm 
Submitted batch job 898769
```

## Output File(s)
In this example, the SLURM option ```--open-mode=append``` was used to pipe the output from every subjob into a single file. This may not be ideal behavior in every case. Removing this option and modifying the output filename will separate the output from each subjob into its own individual file. For more information on SLURM output files and naming conventions, see our [SLURM documentation](https://public.confluence.arizona.edu/pages/viewpage.action?pageId=93160866).

The contents of our output file are: 
```bash
$ cat Sample_Array_Read_Filenames_898769.out 
JOB ID: 898769, EXAMPLE COMMAND: ./executable -o output4 SRR2309587.fastq
JOB ID: 898782, EXAMPLE COMMAND: ./executable -o output2 SRR2309587.fastq
JOB ID: 898781, EXAMPLE COMMAND: ./executable -o output1 SRR2309587.fastq
JOB ID: 898783, EXAMPLE COMMAND: ./executable -o output3 SRR2309587.fastq
```
