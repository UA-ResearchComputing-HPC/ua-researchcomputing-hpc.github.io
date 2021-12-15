# Overview

[![](/Images/Download-Button.png)](Memory-Limits.tar.gz)

Sometimes, jobs use more memory than they've been allotted and are killed by the job scheduler. If you would like more control over your memory usage, or are just looking for a way to profile your script, Python has different methods for detecting memory usage that can help. This example provides a way to kill a Python process that exceeds a threshold set by the user. 

# Python Script
This example will use the ```resource``` package to set the memory limits for the job. The ```tracemalloc``` package is also used to collected the memory usage information which is then reported back to the user 
```
#!/usr/bin/env python3

import resource, sys, tracemalloc, time

def memory_limit():
    soft, hard = 5368706371,5368706371 # 5 GB as an example
    resource.setrlimit(resource.RLIMIT_AS, (soft, hard))

if __name__ == '__main__':
    memory_limit()
    try:
        tracemalloc.start()
        for j in [int(1e4), int(1e5), int(1e6),int(1e7), int(1e8), int(1e9)]:
            print("populating list with %s elements"%j)
            test = list(range(0,j))
            current, peak = tracemalloc.get_traced_memory()
            print(f"Current memory usage is {current / 10**6}MB; Peak was {peak / 10**6}MB\n")
            sys.stdout.flush()
    except MemoryError:
        sys.stderr.write('\n\nERROR: Memory Exception\n')
        current, peak = tracemalloc.get_traced_memory()
        print(f"Current memory usage is {current / 10**6}MB; Peak was {peak / 10**6}MB\n")
        sys.stdout.flush()
        sys.exit(1)
```

# Submission Script
```
#!/bin/bash
#SBATCH --job-name=Sample_Mem_Job
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --mem=5gb                    
#SBATCH --time=00:10:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

module load python/3.6
python3 memory-limits.py
```

# Submit the Job
```
[netid@wentletrap ~]$ sbatch memory-limits.slurm 
Submitted batch job 2105076
```

# Output
```
[netid@wentletrap ~]$ cat slurm-2105076.out 
populating list with 10000 elements
Current memory usage is 0.363322MB; Peak was 0.363446MB

populating list with 100000 elements
Current memory usage is 3.693515MB; Peak was 4.056491MB

populating list with 1000000 elements
Current memory usage is 36.99358MB; Peak was 40.686556MB

populating list with 10000000 elements
Current memory usage is 369.993645MB; Peak was 406.986621MB

populating list with 100000000 elements
Current memory usage is 369.993621MB; Peak was 2276.216106MB



ERROR: Memory Exception
```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
