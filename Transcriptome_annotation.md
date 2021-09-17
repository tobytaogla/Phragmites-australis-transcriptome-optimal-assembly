## 1. Use TransDecoder to extract coding region within transcriptome cd_hit

#### a. Extract the long open reading frames 
     
     TransDecoder-TransDecoder-v5.5.0/TransDecoder.LongOrfs -t cdhit.fa
     
#### b. Predict the likely coding region
    
     TransDecoder-TransDecoder-v5.5.0/TransDecoder.Predict -t cdhit.fa 2>predict.log
     
## 2. Preparation before annotation and build the index from the database including Pfam, Uniprot Swiss-Prot, COG, NR and *S italica* protein file 

#### a. Use Trinotate to create and populate a Trinotate boilerplate sqlite database 
   
     Trinotate-Trinotate-v3.2.1/admin/Build_Trinotate_Boilerplate_SQLite_db.pl Phragmites_RNA/Trinotate

#### b. Build the index 
	 	 
     hmmer-3.3/bin/hmmpress Pfam-A.hmm
     ncbi-blast-2.8.1+/bin/makeblastdb -in uniprot_sprot -dbtype prot -out Phragmites_RNA/count_full_length/uniprot_sprot.fa
     ncbi-blast-2.8.1+/bin/makeblastdb -in Phragmites_RNA/raw_data/Sitalica/v2.2/annotation/Sitalica_312_v2.2.protein.fa -dbtype prot -out Phragmites_RNA/Sitalica_transcript/Sitalica_protein
     diamond makedb -p 8 --in cog-20.fa -d cog/cog
     diamond makedb -p 8 --in nr -d nr_db/nr
     
## 3. Run the blastx and blastp to align the transcriptome and predicted protein file to Uniprot Swiss-Prot and *S italica* protein file 

#### a. Uniprot Swiss-Prot
     
     ncbi-blast-2.8.1+/bin/blastp -query Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep -db Phragmites_RNA/count_full_length/uniprot_sprot.fasta -max_target_seqs 1 -outfmt 6 -evalue 1e-3 -num_threads 8 -out Phragmites_RNA/decoder/blastp.outfmt6 2>Phragmites_RNA/decoder/blastp.log
     ncbi-blast-2.8.1+/bin/blastx -query Phragmites_RNA/decoder/cdhit.fa -db Phragmites_RNA/count_full_length/uniprot_sprot.fasta -num_threads 8 -max_target_seqs 1 -outfmt 6 -evalue 1e-3 > Phragmites_RNA/decoder/blastx.outfmt6 2>Phragmites_RNA/decoder/blastx.log
     
#### b. *S italica* protein file 

     ncbi-blast-2.8.1+/bin/blastp -query Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep -db Phragmites_RNA/Sitalica_transcript/Sitalica_protein -max_target_seqs 1 -outfmt 6 -evalue 1e-3 -num_threads 8 -out Phragmites_RNA/decoder/Sitalica_blastp.outfmt6 2>Phragmites_RNA/decoder/Sitalica_blastp.log
     ncbi-blast-2.8.1+/bin/blastx -query Phragmites_RNA/decoder/cdhit.fa -db Phragmites_RNA/Sitalica_transcript/Sitalica_protein -num_threads 8 -max_target_seqs 1 -outfmt 6 -evalue 1e-3 > Phragmites_RNA/decoder/Sitalica_blastx.outfmt6 2>Phragmites_RNA/decoder/Sitalica_blastx.log
     
## 4. Use hmmer and DIAMOND to run alignment against Pfam, NR, and COG database 

#### a. Pfam
   
     hmmer-3.3/bin/hmmscan --cpu 8 --domtblout Phragmites_RNA/decoder/hmm_pfam_e3.domtblout -E 1e-3 Pfam_database/Pfam-A.hmm Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep 2>Phragmites_RNA/decoder/pfam.log

#### b. NR database

     diamond blastx -p 8 -b 8 -c 1 -q Phragmites_RNA/decoder/cdhit.fa -d nr_db/nr --outfmt 6 -k 1 -e 1e-3 -o Phragmites_RNA/decoder/nr_blastx.outfmt6 2>Phragmites_RNA/decoder/nr_blastx.log
     diamond blastp -p 8 -b 8 -c 1 -q Phragmites_RNA/decoder/cdhit.fa -d nr_db/nr --outfmt 6 -k 1 -e 1e-3 -o Phragmites_RNA/decoder/nr_blastp.outfmt6 2>Phragmites_RNA/decoder/nr_blastp.log
    
#### c. COG database    
   
     diamond blastx -p 8 -q Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep -d cog/cog --outfmt 6 -k 1 -e 1e-3 -o Phragmites_RNA/decoder/cog_blastx.outfmt6 2>Phragmites_RNA/decoder/cog_blastx.log
     diamond blastp -p 8 -q Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep -d cog/cog --outfmt 6 -k 1 -e 1e-3 -o Phragmites_RNA/decoder/cog_blastp.outfmt6 2>Phragmites_RNA/decoder/cog_blastp.log 	   


## 5. Run other Trinotate default annotation via using signalp and tmhmm 
     
     signalp-4.1/signalp -f short -n Phragmites_RNA/decoder/singalp.out Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep
     tmhmm-2.0c/bin/tmhmm --short < Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep > Phragmites_RNA/decoder/tmhmm.out

## 6. Load above results into a Trinotate SQLite database

#### a. Generate gene to transcript relationship file via rsem, required by Trinotate 
     	
     rsem-1.2.19/extract-transcript-to-gene-map-from-trinity Phragmites_RNA/decoder/cdhit.fa Phragmites_RNA/transcript_to_gene_map.out 

#### b. Load transcript and coding region information to the Trinotate SQLite database

     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite init --gene_trans_map Phragmites_RNA/transcript_to_gene_map.out --transcript_fasta Phragmites_RNA/all_assemlies/cdhit/cdhit.fa --transdecoder_pep Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep
	
#### c. Load all the above results from each database to the the Trinotate SQLite database
	
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_swissprot_blastp Phragmites_RNA/decoder/blastp.outfmt6
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_swissprot_blastx Phragmites_RNA/decoder/blastx.outfmt6 
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_custom_blast --outfmt6 Phragmites_RNA/decoder/Sitalica_blastx.outfmt6 --prog blastx --dbtype Sitablastx 
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_custom_blast --outfmt6 Phragmites_RNA/decoder/Sitalica_blastp.outfmt6 --prog blastp --dbtype Sitablastp
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_pfam Phragmites_RNA/decoder/hmm_pfam_e3.domtblout
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_tmhmm Phragmites_RNA/decoder/tmhmm.out
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_signalp Phragmites_RNA/decoder/singalp.out
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_custom_blast --outfmt6 Phragmites_RNA/decoder/nr_blastx.outfmt6 --prog blastx --dbtype nrblastx
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_custom_blast --outfmt6 Phragmites_RNA/decoder/nr_blastp.outfmt6 --prog blastp --dbtype nrblastp
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_custom_blast --outfmt6 Phragmites_RNA/decoder/cog_blastx.outfmt6 --prog blastx --dbtype cogblastx
     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite LOAD_custom_blast --outfmt6 Phragmites_RNA/decoder/cog_blastp.outfmt6 --prog blastp --dbtype cogblastp
     
## 7. Generate an output of Transcript annotation report

     Trinotate-Trinotate-v3.2.1/Trinotate Phragmites_RNA/Trinotate.sqlite report -E 1e-3 > Phragmites_RNA/trinotate_annotation_report_Sitalica.xls 

## 8. Obtain KEGG result from [GhostKOALA](https://www.kegg.jp/ghostkoala/) via using the coding region file; use R script to combine the result with trinotate_annotation_report_Sitalica.xls, clean the data and output the final annotation csv file.  



     











