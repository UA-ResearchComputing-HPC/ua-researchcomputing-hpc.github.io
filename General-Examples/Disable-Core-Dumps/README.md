# Disable Core Dumps

Sometimes when applications crash, they will save a snapshot of the working memory at the time of the crash to a core dump file. Core dump files can be handy for debugging, but they can also be quite large. In some cases, it may be preferable to disable core dumps to prevent your storage allocations from filling up. To do this, you can include the line ```ulimit -c 0``` in your batch script prior to executing your application. An example is shown below

# C Script
An example script used to trigger a segmentation fault that leads to a core dump.
```c
/* t.c */
#include <stdio.h>
void foo()
{
    int *ptr = 0;
    *ptr = 7;
}

int main()
{
    foo();
    return 0;
}
```

# Batch Script
This batch script compiles the code above and runs the resulting executable twice: once with the standard ulimit setting, once after setting ```ulimit -c 0```.
```bash
#!/bin/bash

#SBATCH --account=YOUR_GROUP
#SBATCH --partition=standard
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --time=00:02:00

# Function that tests for the existences of a core dump
function core_exists {
    if [ -f core* ]; then 
        printf "Core dump exists : $(ls core*)\n"
    else 
        printf "Core dump does not exist\n" 
    fi
}

printf "Current allowed core file size: $(ulimit -c)\n\n"

printf "Compiling fault.c\n"
gcc -o fault fault.c
printf "Executing ./fault\n\n"
./fault

printf "\nChecking for core dump...\n"
core_exists
printf "Removing core dumps...\n\n"
rm ./core*

printf "\nRe-checking for core dump\n\n"
core_exists

printf "Setting ulimit\n"
ulimit -c 0
printf "New value for allowed core file size: $(ulimit -c)\n\n"

printf "Executing ./fault\n"
./fault

printf "Checking for core dump...\n"
core_exists

printf "\n\nCleaning up"
rm core* fault &> /dev/null
```

# Job Submission
```console
(elgato) [netid@wentletrap core_dump]$ sbatch fault.slurm 
Submitted batch job 379921
```

# Output
```console
(elgato) [netid@wentletrap core_dump]$ cat slurm-379921.out 
Current allowed core file size: unlimited

Compiling fault.c
Executing ./fault

/var/spool/slurm/d/job379921/slurm_script: line 22:  3623 Segmentation fault      (core dumped) ./fault

Checking for core dump...
Core dump exists : core.3623
Removing core dumps...


Re-checking for core dump

Core dump does not exist
Setting ulimit
New value for allowed core file size: 0

Executing ./fault
/var/spool/slurm/d/job379921/slurm_script: line 37:  3628 Segmentation fault      ./fault
Checking for core dump...
Core dump does not exist


Cleaning upDetailed performance metrics for this job will be available at https://metrics.hpc.arizona.edu/#job_viewer?action=show&realm=SUPREMM&resource_id=4&local_job_id=379921 by 8am on 2022/09/01.
 ```
