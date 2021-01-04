# Two Core Serial Job

## About
This script runs a two-core serial job using the standard queue. A three pound/hash character start to a line indicates a comment. Any line beginning with ```#SBATCH``` issues a SLURM command/directive.

## Script
[Click here to download example](two_core_serial_job.tar.gz)

To run the script, replace ```YOUR_GROUP``` with the name of your PI's group on HPC (to find this, you can use the command ```va```) and submit with ```sbatch ```.
```
#!/bin/bash

#SBATCH --job-name=Sample_Slurm_Job
#SBATCH -o two_core_serial.%j.out
#SBATCH --ntasks=2       
#SBATCH --mem=1gb 
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=<YOUR_GROUP>
 
pwd; hostname; date
module load python/3.6
python3 --version
```

## Script Submission
```
[netid@wentletrap ~]$ sbatch two_core_serial_job.slurm 
Submitted batch job 202142
```
------
[back](../_
