# BLAST Example

This blastp script constructs a blast database from a fasta file. It then uses 4 threads to blast an input sequence against the database. This script can be easily modified to run blastn, blastx, etc. by changing the executable program and database. 

The included reference fasta file was downloaded from [an NCBI ftp repository](ftp://ftp.ncbi.nih.gov/refseq/H_sapiens/mRNA_Prot/). 

For more information on BLAST, see: 
* [BLAST Quick Start](https://www.ncbi.nlm.nih.gov/books/NBK279680/)
* [BLAST Software and Databases](https://blast.ncbi.nlm.nih.gov/Blast.cgi?PAGE_TYPE=BlastDocs&DOC_TYPE=Download)


# Script Example
[Click here to download example](blast_example.tar.gz)

```
#!/bin/bash
#PBS -N blast_example
#PBS -W group_list=<group_name>
#PBS -q standard
#PBS -j oe
#PBS -l select=1:ncpus=4:mem=24gb:pcmem=6gb
#PBS -l walltime=00:10:00

module load blast
cd $PBS_O_WORKDIR

makeblastdb -in human.1.protein.faa  -dbtype prot
time blastp -db ./human.1.protein.faa -query query.faa -out sample_blast_run.out -evalue 1e-30 -num_descriptions 10 -num_alignments 10 -num_threads 4
```
Top run this example, click the link above to download the tar file and unpack it in your HPC account. Inside, you will find blast_example.pbs. This script requires minimal edits to run. Replace ```group_name``` above with your own group's name (this can be found running the command ```va``` if you are unsure). To submit the job, run ```qsub blast_example.pbs```. 

[back](../)
