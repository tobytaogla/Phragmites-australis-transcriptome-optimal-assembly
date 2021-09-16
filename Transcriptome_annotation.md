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
     
#### c. Run the blastx and blastp to align the transcriptome and predicted protein file to Uniprot Swiss-Prot and *S italica* protein file 
     
     ncbi-blast-2.8.1+/bin/blastp -query Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep -db Phragmites_RNA/count_full_length/uniprot_sprot.fasta -max_target_seqs 1 -outfmt 6 -evalue 1e-3 -num_threads 8 -out Phragmites_RNA/decoder/blastp.outfmt6 2>Phragmites_RNA/decoder/blastp.log
     ncbi-blast-2.8.1+/bin/blastx -query Phragmites_RNA/decoder/cdhit.fa -db Phragmites_RNA/count_full_length/uniprot_sprot.fasta -num_threads 8 -max_target_seqs 1 -outfmt 6 -evalue 1e-3 > Phragmites_RNA/decoder/blastx.outfmt6 2>Phragmites_RNA/decoder/blastx.log
     ncbi-blast-2.8.1+/bin/blastp -query Phragmites_RNA/decoder/cdhit.fa.transdecoder.pep -db Phragmites_RNA/Sitalica_transcript/Sitalica_protein -max_target_seqs 1 -outfmt 6 -evalue 1e-3 -num_threads 8 -out Phragmites_RNA/decoder/Sitalica_blastp.outfmt6 2>Phragmites_RNA/decoder/Sitalica_blastp.log
     ncbi-blast-2.8.1+/bin/blastx -query Phragmites_RNA/decoder/cdhit.fa -db Phragmites_RNA/Sitalica_transcript/Sitalica_protein -num_threads 8 -max_target_seqs 1 -outfmt 6 -evalue 1e-3 > Phragmites_RNA/decoder/Sitalica_blastx.outfmt6 2>Phragmites_RNA/decoder/Sitalica_blastx.log







