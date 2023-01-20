# rWGCNA
resampled consensus WGCNA

# Introduction
# Expectations and Applications:
1) Find robust baseline modules of co-expressed genes in control group
   Modules NOT associated with biological or technical variation
2) Evaluate robustness of modules
   How consistent is gene-module assignment?
   Can we improve outcomes from small sample sizes?
3) Pipeline can be applied to other data sets

Can Weighted Correlation Network Analysis (WGCNA) performed on datasets resampled from a larger pool of gene expression data give us more robust modules?

# Methods
# Sample background
Samples were collected from two sites (iPS backgrounds), MGH and GM. Samples were exposed to gRNA as a part of control group in CRISPR or not exposed to gRNA (WT).

# Quality control and normalization
Quality control was performed on the samples to determine outliers using PCA. One sample was removed as an outlier based on PCA after library size normalization. A total of 99 samples were included in the data set. After cpm cutoff of 0.5 in 50% of samples was applied (n genes = 16,769), ComBat was applied to raw counts to correct for iPS background. Data was then normalized by scale factor (DESeq2) and log2 transformed.

For 26-sample WGCNA, 26 samples were chosen at random maintaining an evenly balanced iPS background. This same data set was used for both individual and consensus WGCNA.

# Pipeline Development
Potential WGCNA pipelines and data set sizes were explored to determine a computationally- and time-efficient method. Several stepwise pipelines were explored as well as blockwise functions with two block sizes (5,000 or 17,000), on data sets with either 5,000 or the full 16,769 genes. The larger block size of 17,000 was selected to ensure all genes were run in a single block. Different numbers of iterations were also tested. The effect of soft power threshold calculated on the iterations vs default threshold was also evaluated. Ultimately, the blockwise functions with larger block size were chosen to reduce computational resources and maintain consistency of results.

# WGCNA
WGCNA was run using three methods as follows:
# Individual WGCNA: 
1) Individual WGCNA was run on the full, 99-sample data set. The soft power threshold was calculated as 19, where R^2 reached 0.8. The blockwise() function was employed, with the maximum block size set to 17,000 in order to complete the analysis in a single block. The analysis used a signed network, a minimum module size of 30, and module merge threshold of 0.25. 
   a) Individual WGCNA was run on a 26-sample subset of the data, balanced by iPS background, using the same settings as above with the exception of soft power threshold which was calculated as 21.
Consensus WGCNA:
From the 99-sample data set, 100 data sets were generated by sampling 66% of the original data set (n = 65 eac) without replacement within each data set, following methods outlined by [Perslab and other source]. The soft power threshold was calculated for each subsampled data set, and the median threshold of 18 where R^2 reached 0.8 was chosen for the analysis. Note: if an R^2 of 0.8 is not reached during soft power threshold calculation for any given sampled data set, the maximum power (31) is chosen; this was not necessary for the 99-sample consensus WGCNA. Consensus WGCNA was performed using the blockwiseConsensusModules() function. Eigengenes were calculated across the full 99-sample dataset. Module-trait correlation was also calculated to find baseline modules.
Consensus WGCNA was run on a 26-sample subset of the data, balanced by iPS background, using the same settings as above with the exception of soft power threshold which was calculated as 20.
Bootstrapped individual WGCNA:
Individual WGCNA was performed on the 100 65-sample data sets sampled from the original 99-sample data set. A power threshold of 18 was chosen to reflect the power threshold in the consensus analysis. The same block size, minimum module size, and module merge thresholds were used as the previous WGCNA analyses.

Module number, size. and background distribution
Module number, size, and iPS background distribution between individual, consensus/bootstrapped datasets were assessed.

Enrichment, overlap, and correlation of individual modules for consensus modules: defining reciprocal modules
Gene overlap, enrichment, and correlation was performed to determine the relationship between modules determined in the individual analysis and modules determined in the consensus analysis. Using the consensus 99-sample WGCNA as baseline truth, reciprocal module pairs were determined between consensus 26-sample and individual 26-sample WGCNA as well as individual 99-sample WGCNA. Best-match reciprocal module pairs between the two analyses were defined as module pairs with the most significant nominal p value where each module mutually claims the other as the best match.

Precision and Recall of genes in modules
Using the individual WGCNA modules as baseline, precision and recall of genes in modules was calculated in the consensus WGCNA modules for best-match module pairs, as well as in the bootstrapped individual data sets, with the following definitions
TP: Gene from individual WGCNA module is present in best-match consensus (or bootstrap) module
FP: Gene from individual WGCNA module is present in a module other than the best-match consensus (or bootstrap) module, excluding grey module
TN: Gene from individual WGCNA grey module is present in grey module in consensus (or bootstrap)
FN: Gene from individual WGCNA non-grey module is in grey module in consensus (or bootstrap)
Precision and recall were visualized with histograms

Module membership
Module membership was calculated in individual WGCNA for all genes. To evaluate the strength of membership of genes in individual modules, for each module in individual, module membership was plotted and labeled by presence in best-match, other, or grey module in consensus. 

Module membership cutoff
To determine whether a module membership cutoff could improve the individual 26-sample WGCNA results, a module membership cutoff of 0.50 was applied to genes in each module.  Gene set enrichment for terms from GO, SynGO, KEGG, and Reactome were found for the modified modules.

Module-trait correlation
To determine baseline modules, module-trait correlation and significance was calculated with cor() for binary traits and ANOVA for guide RNA exposure

GO enrichment
Reciprocal module pairs, as well as modified modules from the individual 26-sample WGCNA (referred to as individual 26mod) were queried for gene set enrichment for terms from GO, SynGO, KEGG, and Reactome.
