# Phragmites-australis-transcriptome-optimal-assembly
Workflow and scripts used for the optimal assembly of the *Phragmites australis* transcriptome.
![](https://github.com/tobytaogla/Phragmites-australis-transcriptome-optimal-assembly/blob/main/Flowchart.png)


## Introduction
Multiple types of tissue were used to construct forty-nine *P. australis* transcriptomes via different assembly tools and multiple parameter settings. The optimal transcriptome for functional annotation and downstream analyses was selected among these transcriptomes by comprehensive assessments. For a total of 422,589 transcripts assembled in this transcriptome, 319,046 transcripts (75.5%) have at least one functional annotation. Within the transcriptome, 1,495 transcripts showing tissue-specific expression pattern, 10,828 putative transcription factors, and 72,165 simple sequence repeats markers were further identified. With this optimal transcriptome and all relative information from downstream analyses, foundations for future studies on the mechanisms underlying the invasiveness of non-native *P. australis* were laid.

## Data
1. *Phragmites australis* was sampled by the plant phenotypic character from an open field in urban Detrot, MI, US, including four tissues: leaf, shoot meristem, rhizome and inflorescence. A total of 264 million reads of 100bp Illumina paired end RNA-seq data were obtained. PRJNA762483
3. [*Setaria italica* v2.2 from Phytozome](https://data.jgi.doe.gov/refine-download/phytozome/cladeId:416/All/proteomeId:312/list)
4. [*Phragmites australis* leaf culm and rhizome tip tissue transcriptome sequence reads from PRJNA314710](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA314710)
5. BUSCO library: [poales_odb10](https://busco-data.ezlab.org/v4/data/lineages/poales_odb10.2019-11-20.tar.gz) and [embryophyta_odb10](https://busco-data.ezlab.org/v4/data/lineages/embryophyta_odb10.2019-11-20.tar.gz)
6. Uniprot Swiss-Prot (ftp://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz)
7. Pfam (ftp://ftp.ebi.ac.uk/pub/databases/Pfam/current_release/Pfam-A.hmm.gz)
8. [COG (clusters of orthologous genes database)](https://ftp.ncbi.nih.gov/pub/COG/COG2020/data/)
9. NR (RefSeq non-redundant proteins database) (ftp://ftp.ncbi.nlm.nih.gov/blast/db/FASTA/nr.gz)
10. [*Setaria italica* transcription factors list from PlantTFDB v5.0](http://planttfdb.gao-lab.org/download/TF_list/Sit_TF_list.txt.gz)

## Software
skewer-0.22, Trinity-2.8.4, FastQC v0.11.8, STAR-2.5.2b, shannon-0.02, transabyss-2.0.1, SOAPdenovo-Trans-v1.03, GapCloser-v1.12, bowtie2-2.3.0, CD-HIT-4.8.1, EvidentialGene17dec14, kallisto-0.46.1, ncbi-blast-2.8.1+, DETONATE-1.11, hmmer-3.3, BUSCO 4.0.5, rnaQUAST-2.0.0, q30-master, TransDecoder-v5.5.0, Trinotate-v3.2.1, sqlite-snapshot-202101010144, signalp-4.1g, tmhmm-2.0c, DIAMOND v2.0.6, rsem-1.2.19, Venny, WEGO 2.0, GhostKOALA, MISA v2.1

## Data Analyses
### 1. [Before assembly](https://github.com/tobytaogla/Phragmites-australis-transcriptome-optimal-assembly/blob/main/Before_assemly.md)

    
### 2. [*De novo* transcriptome assembly using different tools and parameters](https://github.com/tobytaogla/Phragmites-australis-transcriptome-optimal-assembly/blob/main/De_novo_transcriptome_assembly.md)

### 3. [EvidentialGene pipeline treament](https://github.com/tobytaogla/Phragmites-australis-transcriptome-optimal-assembly/blob/main/EvidentialGene_pipeline_treatment.md)

### 4. [Transcriptome_quality_assessments](https://github.com/tobytaogla/Phragmites-australis-transcriptome-optimal-assembly/blob/main/Transcriptome_quality_assessments.md)

### 5. [Transcriptome annotation](https://github.com/tobytaogla/Phragmites-australis-transcriptome-optimal-assembly/blob/main/Transcriptome_annotation.md)




    


