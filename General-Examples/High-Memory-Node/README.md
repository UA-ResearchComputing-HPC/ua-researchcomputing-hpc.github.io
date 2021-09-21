# Overview
The clusters Puma and Ocelote both have high memory nodes available that offer more memory per CPU than standard nodes. For more information, see [our system documentation](https://public.confluence.arizona.edu/display/UAHPC/Compute+Resources).

# Submission Script
[Click here to download example](High-Memory-Node-Example.tar.gz)
```
#!/bin/bash
#SBATCH --job-name=High-Memory-Node-Job
#SBATCH --ntasks=1
#SBATCH --nodes=1 
#SBATCH --constraint=hi_mem            
#SBATCH --time=00:00:30   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

echo "Running job on a high memory node!"
echo "Hostname: $(hostname)"
echo "Memory per CPU: $SLURM_MEM_PER_CPU"
```

# Job Submission
```
[netid@wentletrap ~]$ sbatch High-Memory-Node.slurm 
Submitted batch job 2106313
```

# Output
```
[netid@wentletrap ~]$ cat slurm-2106313.out 
Running job on a high memory node!
Hostname: r5u19n1.puma.hpc.arizona.edu
Memory per CPU: 32768
```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
