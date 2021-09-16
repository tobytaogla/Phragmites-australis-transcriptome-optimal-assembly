## 1. Generate one transcriptome from all transcriptomes with CD-HIT before EvidentialGene pipeline treatment

#### a. Combine all the transcriptomes generated via the same tool but different kmer settings

#### b. Use CD-HIT too reduce the transcript redundancy for the combined trancriptomes from the previous step
      
     cd_hit/cd-hit-est -i Phragmites_RNA/combined_assemblies/transabyss_combined.fa -o Phragmites_RNA/cdhit/transabyss_combined_0.98 -n 11 -c 0.98 -p 1 -g 1 -M 20000000000 -T 4 -d 40

#### c. Move all the transcriptomes (Trinity.fasta, Trinity-GG.fasta, transabyss_combined_0.98, shannon_combined_0.98, GapClosed_combined_0.98) to one folder and combnine them as one file

#### d. reformat the renamed file using EvidentialGene script trformat.pl 
     
     perl a/scripts/rnaseq/trformat.pl -output Phragmites_RNA/all_assemlies/cdhit/all_reformat.tr -input Phragmites_RNA/all_assemlies/cdhit/all_renamed.fasta

#### d. Use the EvidentialGene pipeline to identify high-quality transcripts from the combined file cd_hit
    
     perl evigene/scripts/prot/tr2aacds.pl -mrnaseq Phragmites_RNA/all_assemlies/cdhit/all_reformat.tr -NCPU=8 -MAXMEM 200000 1>Phragmites_RNA/all_assemlies/cdhit/tr2aacds.log 2>Phragmites_RNA/all_assemlies/cdhit/tr2aacds.err
     cd Phragmites_RNA/all_assemlies/cdhit/
     perl $HOME/evigene/scripts/evgmrna2tsa.pl -onlypubset -idprefix PaustralisEVm -class $HOME/Phragmites_RNA/all_assemlies/cdhit/all_reformat.trclass 1>$HOME/Phragmites_RNA/all_assemlies/cdhit/pub.log 2>$HOME/Phragmites_RNA/all_assemlies/cdhit/pub.err
     


## 2. Generate one transcriptome from all transcriptomes without CD-HIT before EvidentialGene pipeline treatment

#### a. Move all the transcriptomes to one folder and Combine them as one file

#### b. reformat the renamed file using EvidentialGene script trformat.pl 

     perl evigene/scripts/rnaseq/trformat.pl -output Phragmites_RNA/all_assemlies/no_cdhit/all_reformat.tr -input Phragmites_RNA/all_assemlies/no_cdhit/all_renamed.fasta
     
#### c. Use the EvidentialGene pipeline to identify high-quality transcripts from the combined file no_cd_hit

     perl evigene/scripts/prot/tr2aacds.pl -mrnaseq Phragmites_RNA/all_assemlies/no_cdhit/all_reformat.tr -NCPU=8 -MAXMEM 200000 1>Phragmites_RNA/all_assemlies/no_cdhit/tr2aacds.log 2>Phragmites_RNA/all_assemlies/no_cdhit/tr2aacds.err
     cd Phragmites_RNA/all_assemlies/no_cdhit/
     perl $HOME/evigene/scripts/evgmrna2tsa.pl -onlypubset -idprefix PaustralisEVm -class $HOME/Phragmites_RNA/all_assemlies/no_cdhit/all_reformat.trclass 1>$HOME/Phragmites_RNA/all_assemlies/no_cdhit/pub.log 2>$HOME/Phragmites_RNA/all_assemlies/no_cdhit/pub.err
