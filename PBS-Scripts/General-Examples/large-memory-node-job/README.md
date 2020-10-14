# Large Memory Node Job
Script to run a single job using 42GB RAM on Ocelote using a single core. Up to 2TB of RAM can be requested from this node. Because there is only a single large memory node available on Ocelote, the wait time may be longer than for standard nodes. 

## Script 
[Click here to download example](large_memory_node.tar.gz)

```
#!/bin/bash
#PBS -N large-memory-node-job
#PBS -j oe
#PBS -W group_list=<YOUR_GROUP>
#PBS -q standard
#PBS -l select=1:ncpus=1:mem=42gb:pcmem=42gb
#PBS -l walltime=00:20:00

module load python/3.6/3.6.5
cd $PBS_O_WORKDIR
date
echo "Host Node: $HOSTNAME"
python3 sieve.py
date
```

[back](../)
