## 1. Use bowtie2 and data from this study to assess the read composition of the transcriptome

#### a. Build a bowtie2 index for the transcriptome
     
     bowtie2-2.3.0/bowtie2-build -f Phragmites_RNA/all_assemlies/cdhit/publicset/all_reformat.mrna_pub.fa Phragmites_RNA/assessing_assembly/read_content/cdhit/cdhit.fasta
     
#### b. Mapping the combined reads to the transcriptome and check the mapping rate
     
     bowtie2-2.3.0/bowtie2 -p 8 -q --no-unal -k 20 -x Phragmites_RNA/assessing_assembly/read_content/cdhit/cdhit.fasta -1 Phragmites_RNA/combined_read/skewer_all_P1.fq -2 Phragmites_RNA/combined_read/skewer_all_P2.fq 2>Phragmites_RNA/assessing_assembly/read_content/cdhit/cdhit_stats.txt | samtools-1.9/samtools view -@8 -Sb -o Phragmites_RNA/assessing_assembly/read_content/cdhit/cdhit_bowtie2.bam  
     
## 2. Use bowtie2 and data from [PRJNA314710](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA314710) to assess the read composition of the transcriptome

#### a. Trim the data using the same parameter with skewer and check the quality with FastQC
      
     skewer-master/skewer -m pe -l 50 Phragmites_RNA/austrialian_data/after_dump/PL1_1.fastq Phragmites_RNA/austrialian_data/after_dump/PL1_2.fastq -o Phragmites_RNA/austrialian_data/skewer/PL1
     FastQC/fastqc -o Phragmites_RNA/austrialian_data/fastqc -f fastq Phragmites_RNA/austrialian_data/skewer/*.fastq 
     
#### b. Mapping the austrialian phragmites data to to the transcriptome and check the mapping rate

     bowtie2-2.3.0/bowtie2 -p 8 -q --no-unal -k 20 -x  Phragmites_RNA/assessing_assembly/read_content/cdhit/cdhit.fasta -1 Phragmites_RNA/austrialian_data/skewer/PL1_1_paired.fq -2 Phragmites_RNA/austrialian_data/skewer/PL1_2_paired.fq 2>Phragmites_RNA/assessing_assembly/read_content/austria/cdhit/cdhit_align_stats.txt | samtools-1.9/samtools view -@8 -Sb -o Phragmites_RNA/assessing_assembly/read_content/austria/cdhit/cdhit_bowtie2.bam
     
## 3. Calculate N50 statistics
    
     trinityrnaseq-Trinity-v2.8.4/util/TrinityStats.pl ~/Phragmites_RNA/all_assemlies/cdhit/publicset/all_reformat.mrna_pub.fa > Phragmites_RNA/assessing_assembly/N50/cdhit_Stats.txt
     
## 4. Compute Ex90N50 statistics
    
#### a. Use Trinity script and ultra-fast alignment free method kalliso to perfrom transcript abundanc estimation 
     
     trinityrnaseq-Trinity-v2.8.4/util/align_and_estimate_abundance.pl --transcripts Phragmites_RNA/all_assemlies/cdhit/cdhit.fasta --seqType fq --left Phragmites_RNA/combined_read/skewer_all_P1.fq --right Phragmites_RNA/combined_read/skewer_all_P2.fq --est_method kallisto --aln_method Phragmites_RNA/assessing_assembly/read_content/cdhit/bowtie2.bam --thread_count 8 --trinity_mode --prep_reference --output_dir Phragmites_RNA/assessing_assembly/ExN50/cdhit/
   
#### b. Use the estimates to construct a matrix of count and a matrix of normalized expression values 
     
     trinityrnaseq-Trinity-v2.8.4/util/abundance_estimates_to_matrix.pl --est_method kallisto --gene_trans_map none --out_prefix kallisto --name_sample_by_basedir Phragmites_RNA/assessing_assembly/ExN50/cdhit/abundance.tsv
     
#### c. Generate the ExN50 table 
     
     trinityrnaseq-Trinity-v2.8.4/util/misc/contig_ExN50_statistic.pl Phragmites_RNA/assessing_assembly/ExN50/cdhit/kallisto.isoform.counts.matrix Phragmites_RNA/all_assemlies/cdhit/cdhit.fasta | tee Phragmites_RNA/assessing_assembly/ExN50/cdhit/cdhit_ExN50.stats
     
## 5. Use BUSCO and two separate library to assess the completeness of the transcriptomes

#### a. set up the path for busco config
      
     busco/scripts/busco_configurator.py busco/backup/config.ini busco/config/myconfig.in
  
#### b. Use poales_odb10 

     busco/bin/busco -m transcriptome -i Phragmites_RNA/all_assemlies/cdhit/cdhit.fasta -o OUTPUT -l poales_odb10 --config $HOME/busco/config/myconfig.in -f
     
#### c. use embryophyta_odb10
      
     busco/bin/busco -m transcriptome -i Phragmites_RNA/all_assemlies/cdhit/cdhit.fa -o OUTPUT -l embryophyta_odb10 --config $HOME/busco/config/myconfig.ini -f






     






    

