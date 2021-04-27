# Array Job with Multiple Input Parameters
Demonstration of how to feed parameters to different subjobs in an array by pulling them from an input file, e.g.:
* job 1: ```./executable job1_variable1 job1_variable2 job1_variable3```
* job 2: ```./executable job2_variable1 job2_variable2 job2_variable3```
* etc...

## Script Example
[Click here to download example](Sample_Array_Input_Parameters.tar.gz)
### Slurm File
```bash
#!/bin/bash
#SBATCH --partition=standard
#SBATCH --account=<YOUR_GROUP>
#SBATCH --job-name=Sample_Input_Parameters
#SBATCH --output=%x-%A.out
#SBATCH --open-mode=append
#SBATCH --mem-per-cpu=4gb
#SBATCH --ntasks=1
#SBATCH --time=00:01:00
#SBATCH --array 1-10

read first_parameter second_parameter third_parameter <<< $( sed "${SLURM_ARRAY_TASK_ID}q;d" input_parameters )

echo "Job ID: $SLURM_JOB_ID ; Host Node : $HOSTNAME ; Sample Command : ./executable $first_parameter $second_parameter $third_parameter"
```
### Input File
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
The line number that corresponds with the job's ```SLURM_ARRAY_TASK_ID``` is read in and parsed to extract the input parameters. The parameters in this example should be space-delimited (the bash script can be modified to use whichever delimiter you'd like). There are three per line that are assigned to the variables on the left.
```bash
read first_parameter second_parameter third_parameter <<< $( sed "${SLURM_ARRAY_TASK_ID}q;d" input_parameters )
```
A sample command is printed along with job information for demonstration purposes:
```bash
echo "Job ID: $SLURM_JOB_ID ; Host Node : $HOSTNAME ; Sample Command : ./executable $first_parameter $second_parameter $third_parameter"
```
## Script Submission Command
```bash
$ sbatch Sample_Array_Input_Parameters.slurm 
Submitted batch job 898932
```
## Output Files
In this example, the SLURM option ```--open-mode=append``` was used to pipe the output from every subjob into a single file. This may not be ideal behavior in every case. Removing this option and modifying the output filename will separate the output from each subjob into its own individual file. For more information on SLURM output files and naming conventions, see our [SLURM documentation](https://public.confluence.arizona.edu/pages/viewpage.action?pageId=93160866)

The contents of our output file are:
```
$ cat Sample_Input_Parameters-898932.out 
Job ID: 898936 ; Host Node : r1u03n1 ; Sample Command : ./executable job1_param1 job1_param2 job1_param3
Job ID: 898943 ; Host Node : r1u03n2 ; Sample Command : ./executable job8_param1 job8_param2 job8_param3
Job ID: 898932 ; Host Node : r1u03n2 ; Sample Command : ./executable job10_param1 job10_param2 job10_param3
Job ID: 898937 ; Host Node : r1u03n2 ; Sample Command : ./executable job2_param1 job2_param2 job2_param3
Job ID: 898939 ; Host Node : r1u03n2 ; Sample Command : ./executable job4_param1 job4_param2 job4_param3
Job ID: 898940 ; Host Node : r1u03n2 ; Sample Command : ./executable job5_param1 job5_param2 job5_param3
Job ID: 898938 ; Host Node : r1u03n2 ; Sample Command : ./executable job3_param1 job3_param2 job3_param3
Job ID: 898942 ; Host Node : r1u03n2 ; Sample Command : ./executable job7_param1 job7_param2 job7_param3
Job ID: 898941 ; Host Node : r1u03n2 ; Sample Command : ./executable job6_param1 job6_param2 job6_param3
Job ID: 898944 ; Host Node : r1u03n2 ; Sample Command : ./executable job9_param1 job9_param2 job9_param3
```

---
[back](../)
