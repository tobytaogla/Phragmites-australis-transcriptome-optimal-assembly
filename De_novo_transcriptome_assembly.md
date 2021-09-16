### Combined all the trimmed reads
  
         cat Phragmites_RNA/raw_data/*trimmed-pair1.fastq > Phragmites_RNA/combined_read/skewer_all_P1.fq
         cat Phragmites_RNA/raw_data/*trimmed-pair2.fastq > Phragmites_RNA/combined_read/skewer_all_P2.fq
### Use Trinity to perfrom *de novo* transcriptome assembly
    
         Trinity --seqType fq --left Phragmites_RNA/combined_read/skewer_all_P1.fq --right Phragmites_RNA/combined_read/skewer_all_P2.fq --SS_lib_type FR --max_memory 100G --CPU 8 --output Phragmites_RNA/trinity_skewer
    
### Use Trinity to perform *S. italica* genome-guided *de novo* transcriptome assembly

   - Use STAR to build *S. italica* genome index, align trimmed data

         STAR-2.5.2b/bin/Linux_x86_64/STAR --runThreadN 8 --runMode genomeGenerate --genomeDir Phragmites_RNA/raw_data/Sitalica_index2/ --genomeFastaFiles Phragmites_RNA/raw_data/Sitalica/v2.2/Sitalica_312_v2.fa
        
         STAR-2.5.2b/bin/Linux_x86_64/STAR --runThreadN 8 --runMode alignReads --genomeDir Phragmites_RNA/raw_data/Sitalica_index2 --readFilesIn Phragmites_RNA/combined_read/skewer_all_P1.fq Phragmites_RNA/combined_read/skewer_all_P2.fq --outSAMtype BAM SortedByCoordinate --outFileNamePrefix Phragmites_RNA/star_align/skewer --limitBAMsortRAM 62000000000
       
   - Using the generated bam file to perfrom the *S. italica* genome-guided *de novo* transcriptome assembly
    
         Trinity --genome_guided_bam Phragmites_RNA/star_align/skewer_Sitalica_align/skewerAligned.sortedByCoord.out.bam --genome_guided_max_intron 10000 --seqType fq --left Phragmites_RNA/combined_read/skewer_all_P1.fq --right Phragmites_RNA/combined_read/skewer_all_P2.fq --SS_lib_type FR --max_memory 50G --CPU 8 --output Phragmites_RNA/trinity_all_skewer_genome_guide
   
### Use shannnon to perform *de novo* transcriptome assembly
    
   - Change the parameter -K to include all 14 kmer (21, 23, 25, 27, 29, 31, 33, 35, 45, 55, 65, 75, 85, 95)
     
         shannon.py -o Phragmites_RNA/Shannon/dir95 --left Phragmites_RNA/combined_read/skewer_all_P1.fq --right Phragmites_RNA/combined_read/skewer_all_P2.fq -K 95 -p 4

### Use transbyss to perform *de novo* transcriptome assembly
   - Change the parameter --kmer to include all 14 kmer (21, 23, 25, 27, 29, 31, 33, 35, 45, 55, 65, 75, 85, 95)

         transabyss --pe Phragmites_RNA/combined_read/skewer_all_P1.fq Phragmites_RNA/combined_read/skewer_all_P2.fq --kmer 27 --length 200 --outdir Phragmites_RNA/transabyss/dir27 --threads 8

### Use SOAPdenovo-Trans to perform *de novo* transcriptome assembly and GapCloser to remove strings of "N" 
  
  - Use SOAPdenovo-Trans, change the parameter -K to include all 14 kmer (21, 23, 25, 27, 29, 31, 33, 35, 45, 55, 65, 75, 85, 95)
   
        for ((n=21; n<35; n=n+2)); do SOAP/SOAPdenovo-Trans-31mer all -L 200 -K $n -p 8 -s SOAP/SOAP.config -o Phragmites_RNA/SOAP_output/SOAP_k$n 1>Phragmites_RNA/SOAP_output/SOAP_k$n.log 2>Phragmites_RNA/SOAP_output/SOAP_k$n.err; done
		for ((n=35; n<95; n=n+10)); do SOAP/SOAPdenovo-Trans-127mer all -L 200 -K $n -p 16 -s SOAP/SOAP.config -o Phragmites_RNA/SOAP_output/SOAP_k$n 1>Phragmites_RNA/SOAP_output/SOAP_k$n.log 2>Phragmites_RNA/SOAP_output/SOAP_k$n.err; done

  - Copy all the SOAPdenovo-Trans constructed assemblies to a new folder and renamed them to the fasta file
  
        for f in SOAP*; do cp $f.scafSeq ~/Phragmites_RNA/SOAP_fasta/; done
		for f in Phragmites_RNA/SOAP_fasta/*.scafSeq; do mv $f $(echo $f | sed 's/.scafSeq/.fasta/g'); done
    
  - Use GapCloser to remove strings of "N" introduced in transcriptome by SOAPdenovo-Tran

        for ((n=21; n<35; n=n+2)); do GapCloser -b Phragmites_RNA/GapCloser/GapCloser.config -a Phragmites_RNA/SOAP_fasta/SOAP_k$n.fasta -o Phragmites_RNA/GapCloser/GapClosed_K$n -t 24 1>Phragmites_RNA/GapCloser/GapClosed_K$n.log 2>Phragmites_RNA/GapCloser/GapClosed_K$n.err; done
		for ((n=35; n<105; n=n+10)); do GapCloser -b Phragmites_RNA/GapCloser/GapCloser.config -a Phragmites_RNA/SOAP_fasta/SOAP_k$n.fasta -o Phragmites_RNA/GapCloser/GapClosed_K$n -t 24 1>Phragmites_RNA/GapCloser/GapClosed_K$n.log 2>Phragmites_RNA/GapCloser/GapClosed_K$n.err; done 

