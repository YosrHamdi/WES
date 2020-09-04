#!/usr/bin/bash

#creation of conda environment
#conda create "environment name"
#conda activate environment name

#path_wes_data=pwd

#install packages required for wes quality control using conda from the bioconda channel
#conda install -c bioconda fastqc
#conda install -c bioconda trimmomatic
#conda install -c bioconda multiqc

##Create a directory for raw data
#mkdir raw_data
#cd /home/.../raw_data

#creation of directories for outputs
mkdir wes_data_qc
mkdir wes_data_trimmed
mkdir wes_data_trimmed_qc

#Run quality control on raw data
for file in *.fastq.gz
do
fastqc $file --outdir wes_data_qc
done

#Run trimmomatic command on raw data
for f1 in *1.fastq.gz
do 
f2=${f1%%1.fastq.gz}
trimmomatic PE $f1 $f2"2.fastq.gz" wes_data_trimmed/$f2"trimmed_paired_1.fastq.gz" wes_data_trimmed/$f2"trimmed_unpaired_1.fastq.gz" wes_data_trimmed/$f2"trimmed_paired_2.fastq.gz" wes_data_trimmed/$f2"trimmed_unpaired_2.fastq.gz" ILLUMINACLIP:/usr/share/trimmomatic/TruSeq3-PE.fa:2:30:10 SLIDINGWINDOW:4:15 MINLEN:36
done

#Run quality control on trimmed data
for file in $(ls wes_data_trimmed/*trimmed_paired*)
do
fastqc $file --outdir wes_data_trimmed_qc
done


#trimmomatic PE raw_wes_1.fastq.gz raw_wes_2.fastq.gz wes_data_trimming/wes_data_trimming_trimmomatic/#wes_trimmed_trimmomatic_paired_1.fastq.gz wes_data_trimming/wes_data_trimming_trimmomatic/#wes_trimmed_trimmomatic_unpaired_1.fastq.gz wes_data_trimming/wes_data_trimming_trimmomatic/#wes_trimmed_trimmomatic_paired_2.fastq.gz wes_data_trimming/wes_data_trimming_trimmomatic/#wes_trimmed_trimmomatic_unpaired_2.fastq.gz  ILLUMINACLIP:/usr/share/trimmomatic/TruSeq3-PE.fa:2:30:10 SLIDINGWINDOW:4:15 MINLEN:36

##trimmomatic log output: "Quality encoding detected as phred33
#Input Read Pairs: 40014460 Both Surviving: 35849619 (89,59%) Forward Only Surviving: 4024444 (10,06%) Reverse Only Surviving: #69718 (0,17%) Dropped: 70679 (0,18%)
#TrimmomaticPE: Completed successfully"

#mkdir -p wes_data_trimming/wes_data_trimming_bbduk
#bbduk.sh in1=raw_wes_1.fastq.gz in2=raw_wes_2.fastq.gz out=wes_data_trimming_bbduk/wes_trimmed#_fastq.gz trimq=15 qtrim=r #minlen=36 ref=raw_data_quality_control/adapters.fa

#bbduk.sh in1=raw_wes_1.fastq.gz in2=raw_wes_2.fastq.gz out=wes_data_trimming_bbduk/wes_trimmed_rl#_fastq.gz trimq=15 qtrim=rl #minlen=36 ref=raw_data_quality_control/adapters.fa


#for file in *1.fastq.gz
#do 
#filename=$(echo $file | cut -d"1" -f1)
#filename2=$filename$"2"
#trimmomatic PE $file $filename2.fastq.gz wes_data_trimming/$filename"trimmed_paired_1.fastq.gz" wes_data_trimming/#$filename"trimmed_unpaired_1.fastq.gz" wes_data_trimming/$filename"trimmed_paired_2.fastq.gz" wes_data_trimming/#$filename"trimmed_unpaired_2.fastq.gz" ILLUMINACLIP:/usr/share/trimmomatic/TruSeq3-PE.fa:2:30:10 SLIDINGWINDOW:4:15 MINLEN:36
#done



