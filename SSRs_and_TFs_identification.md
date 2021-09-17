## 1. SSRs(Single sequence repeats) identification 

#### a. Use MISA to find SSRs with default settings

	 perl misa.pl ~/Phragmites_RNA/all_assemlies/renamed_for_abundance/cdhit_mrna_renamed.fa
   
#### b. Find the CDS-SSRs by comparing the SSRs list with tissue-specific DETs lists and coding region file

## 2. TFs(Transcription factors) identification

#### a. By comparing [the TFs list of *Setaria italica* from PlantTFDB v5.0](http://planttfdb.gao-lab.org/download/TF_list/Sit_TF_list.txt.gz) with the annotation result from the alignment between the predicted peptide sequence file and S. italica annotated protein file via blastp.

#### b. Screen TFs with tissue-specific expression patterns, based on the lists of tissue-specific DETs from the previous step.
