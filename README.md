# WES
conda create phindhack_wes
conda activate phindhack_wes

# Install packages required for WES quality control - Bioconda Channel
conda install -c bioconda fastqc
conda install -c bioconda multiqc


# Check quality of raw data/reads
cd anaconda3/envs/phindhack_wes/
mkdir raw_data_quality_control
fastqc *.fastq.gz --outdir raw_data_quality_control 


conda install -c bioconda trimmomatic 

mkdir -p wes_data_trimming/wes_data_trimming_trimmomatic

trimmomatic PE raw_wes_1.fastq.gz raw_wes_2.fastq.gz wes_data_trimming/wes_data_trimming_trimmomatic/wes_trimmed_trimmomatic_paired_1.fastq.gz wes_data_trimming/wes_data_trimming_trimmomatic/wes_trimmed_trimmomatic_unpaired_1.fastq.gz wes_data_trimming/wes_data_trimming_trimmomatic/wes_trimmed_trimmomatic_paired_2.fastq.gz wes_data_trimming/wes_data_trimming_trimmomatic/wes_trimmed_trimmomatic_unpaired_2.fastq.gz  ILLUMINACLIP:/usr/share/trimmomatic/TruSeq3-PE.fa:2:30:10 SLIDINGWINDOW:4:15 MINLEN:36

# trimmomatic log output: "Quality encoding detected as phred33
Input Read Pairs: 40014460 Both Surviving: 35849619 (89,59%) Forward Only Surviving: 4024444 (10,06%) Reverse Only Surviving: 69718 (0,17%) Dropped: 70679 (0,18%)
TrimmomaticPE: Completed successfully"

fastqc wes_trimmed_trimmomatic_paired_*.fastq.gz

mkdir -p wes_data_trimming/wes_data_trimming_bbduk
bbduk.sh in1=raw_wes_1.fastq.gz in2=raw_wes_2.fastq.gz out=wes_data_trimming_bbduk/wes_trimmed#_fastq.gz trimq=15 qtrim=r minlen=36 ref=raw_data_quality_control/adapters.fa

bbduk.sh in1=raw_wes_1.fastq.gz in2=raw_wes_2.fastq.gz out=wes_data_trimming_bbduk/wes_trimmed_rl#_fastq.gz trimq=15 qtrim=rl minlen=36 ref=raw_data_quality_control/adapters.fa


mkdir wes_data_trimming
for file in ./*1.fastq.gz
do 
trimmomatic PE $file $file_2.fastq.gz wes_data_trimming_$file/$file_trimmed_paired_1.fastq.gz wes_data_trimming_$file/$file_trimmed_unpaired_1.fastq.gz wes_data_trimming$file/$file_trimmed_paired_2.fastq.gz wes_data_trimming_$file/$file_trimmed_unpaired_2.fastq.gz ILLUMINACLIP:/usr/share/trimmomatic/TruSeq3-PE.fa:2:30:10 SLIDINGWINDOW:4:15 MINLEN:36
done
