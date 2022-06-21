# Hybrid_Assembly_Plato
Hybrid Assembly Script for Plato HPC at USask

Required Packages (already available ars modules or python wheel for Compute Canada):
Nanostat
Porechop
Filtlong
Flye
Quast
Fastp
FastQC
Unicycler
bwa
samtools
pilon 
mummer
snippy
vcftools
tabix

It is designed to have a preparation file that will look something like this:  

#!/bin/bash
cat text.txt |while read line; do
echo "Submitting job for file $line"
	sbatch hybridassembly.sh "$line"
done

That allows you to create a list of filenames in "text.txt" that corresponds to a variable screated with each line of the .txt file. In the hybridassembly.sh file each line will be "$line". This prep file will submit the sbatch for each name in each line of text.txt and feed the line in as "$line" in the hybridassembly.sh file. I usually have everything in one directory and have subdirectories named for each isolate with the Long and Short reads in that subdirectory.

(for example:<main folder with scripts>/"$line"/"$line"_(SR_1/LR_ALL).fastq)
	
Replace you HPC/slurm parameters at the top of the page.
First the script does the QC, trimming and filtering of the Nanopore long reads then assembles these reads using Flye. Then it does the QC, trimming and filtering of the Illumina short reads. The Nanopore and Illumina read are then assembled into a hybrid genome using Unicycler and the quality of the assembly is determined using Quast. Next, a folder is made to hold the polishing files and we make that file that current directory. Then the polishing steps begin with 15 pilon polishing steps. Then the remaining snps, if there are any, are removed using Snippy and that is followed by 15 additional polishing steps. Then the Illumina reads are mapped to the polished assembly and Quast is run on the final assembly. The final assembly is renames as "$line"_assembly_complete.fasta.
  
  I hope to eventually make this a Nextflow pipeline but it works for now.
