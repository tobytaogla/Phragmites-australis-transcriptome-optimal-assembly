## 1. Use rsem to build transcriptome reference and separately estimate transcript abundance of each tissue

      rsem-1.2.19/rsem-prepare-reference --transcript-to-gene-map Phragmites_RNA/transcript_to_gene_map.out --bowtie2 Phragmites_RNA/all_assemlies/renamed_for_abundance/cdhit.fa Phragmites_RNA/rsem/reference/cdhit
      rsem-1.2.19/rsem-calculate-expression -p 8 --paired-end --bowtie2 --estimate-rspd Phragmites_RNA/raw_data/skewer/PR-trimmed-pair1.fastq Phragmites_RNA/raw_data/skewer/PR-trimmed-pair2.fastq Phragmites_RNA/rsem/reference/cdhit Phragmites_RNA/rsem/rsemPR/rsemPR
	   
## 2. Use trinity script to run R command for DETs(Differential expressed transcripts) analyses and GO(Gene ontology) analyses

#### a. Generate Transcript Expression Matrices

     trinityrnaseq-Trinity-v2.8.4/util/abundance_estimates_to_matrix.pl --est_method RSEM --gene_trans_map ~/transcript_to_gene_map.out --out_prefix RSEM --name_sample_by_basedir ~/PF/rsemPF.isoforms.results ~/PR/rsemPR.isoforms.results ~/PM/rsemPM.isoforms.results ~/PL/rsemPL.isoforms.results

#### b. Counting Numbers of Expressed Transcripts

     trinityrnaseq-Trinity-v2.8.4/util/misc/count_matrix_features_given_MIN_TPM_threshold.pl ~/data/counts/RSEM.isoform.TPM.not_cross_norm | tee ~/data/counts/isoform_matrix.TPM.not_cross_norm.counts_by_min_TPM

#### c. Generate a heatmap showing the correlation among samples based on expression

     trinityrnaseq-Trinity-v2.8.4/Analysis/DifferentialExpression/PtR --save --matrix data/counts/RSEM.isoform.counts.matrix --samples data/counts/samples.txt --log2 --min_rowSums 10 --CPM --sample_cor_matrix --heatmap_colorscheme 'purple,black,yellow' 

#### d. Generate PCA plot showing the correlation among samples

     trinityrnaseq-Trinity-v2.8.4/Analysis/DifferentialExpression/PtR --save --matrix data/counts/RSEM.isoform.counts.matrix --samples data/counts/samples.txt --log2 --min_rowSums 10 --CPM --center_rows --prin_comp 3

#### e. DETs analyses, using dispersion 0.1, as all the samples do not have replicates

     trinityrnaseq-Trinity-v2.8.4/Analysis/DifferentialExpression/run_DE_analysis.pl --matrix data/counts/RSEM.isoform.counts.matrix --method edgeR --samples data/counts/samples.txt --output ~/DE_analysis_dp01 --dispersion 0.1		   

#### f. Generate gene_lengths.txt for the GO analyses

     trinityrnaseq-Trinity-v2.8.4/util/misc/TPM_weighted_gene_length.py --gene_trans_map ~/data/transcript_to_gene_map.out --trans_lengths ~/data/cdhit.fasta.seq_lens --TPM_matrix ~/data/counts/RSEM.isoform.TMM.EXPR.matrix > ~/data/cdhit.gene_lengths.txt
		
#### h. Extract and cluster DETs in pairwise comparison; perform GO analyses at the same time, with foldchnage > 2, pvalue 0.005

     trinityrnaseq-Trinity-v2.8.4/Analysis/DifferentialExpression/analyze_diff_expr_1.pl --matrix ~/data/counts/RSEM.gene.TMM.EXPR.matrix --sample ~/data/counts/samples.txt -P 0.005 -C 1 --examine_GO_enrichment --GO_annots ~/data/go_annotations.txt --gene_lengths ~/data/cdhit.gene_lengths.txt 1>dp01_p0005_c1.log

#### i. Partition transcripts into expression clusters

     trinityrnaseq-Trinity-v2.8.4/Analysis/DifferentialExpression/define_clusters_by_cutting_tree.pl --Ptree 60 -R ~/transcripts_DE_dp01_p0005_c1/diffExpr.P0.005_C1.matrix.RData

#### j. Use [Venny](https://bioinfogp.cnb.csic.es/tools/venny/) to find the tissue-specific DETs, which are detected in all three pairwise comparisons
     
#### k. GO analyses for tissue-specific DETs; tissue expressed transcripts list is generated from RSEM.isoform.counts.matrix

     trinityrnaseq-Trinity-v2.8.4/Analysis/DifferentialExpression/run_GOseq.pl --factor_labeling transcripts_list_DE_dp01_p0005_c1/PF_all_UP_go.txt --GO_assignments data/go_annotations.txt --lengths ~/data/cdhit.gene_lengths.txt --background data/tissue_background/PF_exp_list.txt 2>go_all_up.log 
