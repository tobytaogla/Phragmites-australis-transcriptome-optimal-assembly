## 1. Use skewer to trim data from each tissue
  
    skewer-master/skewer -m pe -l 50 Phragmites_RNA/raw_data/PR_R1.fastq Phragmites_RNA/raw_data/PR_R2.fastq -o Phragmites_RNA/raw_data/skewer/PR

## 2. Use FastQC to assess the quality of trimmed reads
  
    FastQC/fastqc -o Phragmites_RNA/fastqc -f fastq Phragmites_RNA/raw_data/skewer/PF_trimmed_pair2.fastq Phragmites_RNA/raw_data/skewer/PF_trimmed_pair1.fastq Phragmites_RNA/raw_data/skewer/PL-trimmed-pair1.fastq Phragmites_RNA/raw_data/skewer/PL-trimmed-pair2.fastq Phragmites_RNA/raw_data/skewer/PM-trimmed-pair1.fastq Phragmites_RNA/raw_data/skewer/PM-trimmed-pair2.fastq Phragmites_RNA/raw_data/skewer/PR-trimmed-pair1.fastq Phragmites_RNA/raw_data/skewer/PR-trimmed-pair2.fastq

## 3. Use q30-master to caculate the nucleotide percentage with quality higher than 30
 
    q30-master/q30.py Phragmites_RNA/raw_data/skewer/PR-trimmed-pair1.fastq > Phragmites_RNA/q30/PR_1_q30.stat
