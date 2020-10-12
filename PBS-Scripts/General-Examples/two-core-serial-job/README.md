# Serial Job Requesting Two Cores

[Click here to download example](two_core_serial_job.tar.gz)

This script runs a serial job using one core using the windfall queue. A three pound/hash character start to a line indicates a comment. Any line beginning with ```#PBS``` issues a PBS command/directive.

To run the script, replace <YOUR_GROUP> with the name of your PI's group on HPC (to find this, you can use the command ```va``` ) and submit with ```qsub <script_name>``` .

 ```
#!/bin/bash
#PBS -N serial-job-example
#PBS -W group_list=<YOUR_GROUP>
#PBS -q windfall
#PBS -l select=1:ncpus=2:mem=12gb
#PBS -l walltime=01:00:00
 
module load blast
cd $PBS_O_WORKDIR

date
blastn -help
date
 ```
