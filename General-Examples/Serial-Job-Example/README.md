# Serial Job

[Click here to download example](Serial-Job-Example.tar.gz)

This script runs a serial, single-CPU job using the standard queue.

Any line beginning with #SBATCH issues a SLURM directive that controls aspects of your job such as job name, output filename, memory requirements, number of CPUs, number of nodes, etc.

To run the script, replace ```YOUR_GROUP``` with the name of your PI's group on HPC. To find this information, you can use the command ```va```. You can submit the job with ```sbatch <script_name>```. This job assumes there is a python script in your working directory (included in downloadable example above).


### Submission Script
```
#!/bin/bash

#SBATCH --job-name=Sample_Slurm_Job
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=hpcteam

module load python/3.8
python3 hello_world.py
```

### Example Python Script
```
#!/usr/bin/env python3
import os
print("Hello world! I'm running on compute node: %s"%os.environ["HOSTNAME"])
```

### Example Job Submission
```
(ocelote) [netid@junonia ~]$ ls
hello_world.py  serial-job.slurm
(ocelote) [netid@junonia ~]$ sbatch serial-job.slurm 
Submitted batch job 73224
```

### Output
```
(ocelote) [netid@junonia ~]$ ls
slurm-73224.out  hello_world.py  serial-job.slurm
(ocelote) [netid@junonia ~]$ cat slurm-73224.out 
Hello world! I'm running on compute node: i4n0
Detailed performance metrics for this job will be available at https://metrics.hpc.arizona.edu/#job_viewer?action=show&realm=SUPREMM&resource_id=5&local_job_id=73224 by 8am on 2021/08/05.
(ocelote) [netid@junonia ~]$ 
```
