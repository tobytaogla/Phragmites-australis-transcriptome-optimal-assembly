# Phragmites-australis-transcriptome-optimal-assembly
Workflow and scripts used for the optimal assembly of the *Phragmites australis* transcriptome.

## Introduction
Multiple types of tissue were used to construct forty-nine *P. australis* transcriptomes via different assembly tools and multiple parameter settings. The optimal transcriptome for functional annotation and downstream analyses was selected among these transcriptomes by comprehensive assessments. For a total of 422,589 transcripts assembled in this transcriptome, 319,046 transcripts (75.5%) have at least one functional annotation. Within the transcriptome, 1,495 transcripts showing tissue-specific expression pattern, 10,828 putative transcription factors, and 72,165 simple sequence repeats markers were further identified. With this optimal transcriptome and all relative information from downstream analyses, foundations for future studies on the mechanisms underlying the invasiveness of non-native *P. australis* were laid.

## Data
1. *Phragmites australis* was sampled by the plant phenotypic character from an open field in urban Detrot, MI, US, including four tissues: leaf, shoot meristem, rhizome and inflorescence. A total of 264 million reads of 100bp Illumina paired end rna_seq data were obtained.  
2. [*Setaria italica* v2.2 from Phytozome](https://data.jgi.doe.gov/refine-download/phytozome/cladeId:416/All/proteomeId:312/list)
3. [*Phragmites australis* leaf culm and rhizome tip tissue transcriptome sequence reads from PRJNA314710](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA314710)
4. BUSCO library: [poales_odb10](https://busco-data.ezlab.org/v4/data/lineages/poales_odb10.2019-11-20.tar.gz) and [embryophyta_odb10](https://busco-data.ezlab.org/v4/data/lineages/embryophyta_odb10.2019-11-20.tar.gz)
5. Uniprot Swiss-Prot (ftp://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz)
6. Pfam (ftp://ftp.ebi.ac.uk/pub/databases/Pfam/current_release/Pfam-A.hmm.gz)
7. [COG (clusters of orthologous genes database)](https://ftp.ncbi.nih.gov/pub/COG/COG2020/data/)
8. NR (RefSeq non-redundant proteins database) (ftp://ftp.ncbi.nlm.nih.gov/blast/db/FASTA/nr.gz)
9. [*Setaria italica* transcription factors list from PlantTFDB v5.0](http://planttfdb.gao-lab.org/download/TF_list/Sit_TF_list.txt.gz)

## Software
skewer-0.22, Trinity-2.8.4, FastQC v0.11.8, STAR-2.5.2b, shannon-0.02, transabyss-2.0.1, SOAPdenovo-Trans-v1.03, GapCloser-v1.12, bowtie2-2.3.0, CD-HIT-4.8.1, EvidentialGene17dec14, kallisto-0.46.1, ncbi-blast-2.8.1+, DETONATE-1.11, hmmer-3.3, BUSCO 4.0.5, rnaQUAST-2.0.0, q30-master, TransDecoder-v5.5.0, Trinotate-v3.2.1, sqlite-snapshot-202101010144, signalp-4.1g, tmhmm-2.0c, DIAMOND v2.0.6, rsem-1.2.19, Venny, WEGO 2.0, GhostKOALA, MISA v2.1

## Data Analyses
### 1. Before assembly
- Use skewer to trim data from each tissue
    
    `skewer-master/skewer -m pe -l 50 Phragmites_RNA/raw_data/PR_R1.fastq Phragmites_RNA/raw_data/PR_R2.fastq -o Phragmites_RNA/raw_data/skewer/PR`
- Use FastQC to assess the quality of trimmed reads
  
    ```FastQC/fastqc -o Phragmites_RNA/fastqc -f fastq Phragmites_RNA/raw_data/skewer/PF_trimmed_pair2.fastq Phragmites_RNA/raw_data/skewer/PF_trimmed_pair1.fastq Phragmites_RNA/raw_data/skewer/PL-trimmed-pair1.fastq Phragmites_RNA/raw_data/skewer/PL-trimmed-pair2.fastq Phragmites_RNA/raw_data/skewer/PM-trimmed-pair1.fastq Phragmites_RNA/raw_data/skewer/PM-trimmed-pair2.fastq Phragmites_RNA/raw_data/skewer/PR-trimmed-pair1.fastq Phragmites_RNA/raw_data/skewer/PR-trimmed-pair2.fastq ```

- Use q30-master to caculate the nucleotide percentage with quality higher than 30
 
    `q30-master/q30.py Phragmites_RNA/raw_data/skewer/PR-trimmed-pair1.fastq > Phragmites_RNA/q30/PR_1_q30.stat
    
### 2. *DE novo* transcriptome assembly using different tools and parameters
- Combined all the trimmed reads
  
    ```cat Phragmites_RNA/raw_data/*trimmed-pair1.fastq > Phragmites_RNA/combined_read/skewer_all_P1.fq```
    
    ```cat Phragmites_RNA/raw_data/*trimmed-pair2.fastq > Phragmites_RNA/combined_read/skewer_all_P2.fq```
- Use Trinity *de novo* transcriptome assembly
    
    ```Trinity --seqType fq --left Phragmites_RNA/combined_read/skewer_all_P1.fq --right Phragmites_RNA/combined_read/skewer_all_P2.fq --SS_lib_type FR --max_memory 100G --CPU 8 --output Phragmites_RNA/trinity_skewer```
    
- Use Trinity *S. italica* genome-guided *de novo* transcriptome assembly

    - Use STAR to build *S. italica* genome index, align trimmed data
        
        ```STAR-2.5.2b/bin/Linux_x86_64/STAR --runThreadN 8 --runMode genomeGenerate --genomeDir Phragmites_RNA/raw_data/Sitalica_index2/ --genomeFastaFiles Phragmites_RNA/raw_data/Sitalica/v2.2/Sitalica_312_v2.fa```
        
        ```STAR-2.5.2b/bin/Linux_x86_64/STAR --runThreadN 8 --runMode alignReads --genomeDir Phragmites_RNA/raw_data/Sitalica_index2 --readFilesIn Phragmites_RNA/combined_read/skewer_all_P1.fq Phragmites_RNA/combined_read/skewer_all_P2.fq --outSAMtype BAM SortedByCoordinate --outFileNamePrefix Phragmites_RNA/star_align/skewer --limitBAMsortRAM 62000000000```
     
    - Using the generated bam file to perfrom the *S. italica* genome-guided *de novo* transcriptome assembly
    
        ```Trinity --genome_guided_bam Phragmites_RNA/star_align/skewer_Sitalica_align/skewerAligned.sortedByCoord.out.bam --genome_guided_max_intron 10000 --seqType fq --left Phragmites_RNA/combined_read/skewer_all_P1.fq --right Phragmites_RNA/combined_read/skewer_all_P2.fq --SS_lib_type FR --max_memory 50G --CPU 8 --output Phragmites_RNA/trinity_all_skewer_genome_guide```
        
- 




    


