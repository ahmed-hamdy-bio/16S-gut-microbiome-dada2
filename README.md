# 16S rRNA amplicon analysis: DADA2 to phyloseq

End-to-end, reproducible 16S workflow from raw paired-end FASTQ to taxonomy,
diversity statistics and differential abundance.

**Question:** does the gut microbiome of a mouse differ between early (days 0-9)
and late (days 141-150) post-weaning?

**Data:** public mothur MiSeq SOP dataset (Kozich et al. 2013), 2x250 bp, V4 region.
This is a well-known benchmark dataset. The project demonstrates the pipeline, not a new finding.

## Pipeline
1. Quality inspection, filtering and trimming (DADA2)
2. Error learning, denoising to ASVs, paired-read merging, chimera removal
3. Read tracking through every step
4. Accuracy check against the mock community
5. Taxonomy with SILVA v138.1
6. phyloseq object: composition, alpha diversity (Observed, Shannon), beta diversity (Bray-Curtis PCoA, PERMANOVA)
7. Differential abundance with DESeq2

## Run it
```r
install.packages(c("BiocManager", "ggplot2", "dplyr", "vegan", "rmarkdown"))
BiocManager::install(c("dada2", "phyloseq", "Biostrings", "DESeq2"))
rmarkdown::render("analysis.Rmd")
```
Data and the SILVA database are downloaded automatically on the first run.

## Results
- **Input and QC:** 152,360 raw read pairs across 20 samples (19 faecal + 1 mock). 124,249 pairs (81.5%) remained after filtering, denoising, merging and chimera removal; 96.4% of merged reads were non-chimeric.
- **Mock community:** DADA2 inferred 20 sequence variants and all 20 matched the reference sequences exactly (no spurious variants).
- **Final dataset:** 217 bacterial ASVs in 19 samples (Mock, non-bacterial, mitochondrial and chloroplast sequences removed).
- **Alpha diversity:** no significant difference between early and late samples (Shannon p = 0.133, observed ASVs p = 0.127; Wilcoxon).
- **Beta diversity:** community composition differs clearly between periods (PERMANOVA on Bray-Curtis: R2 = 0.43, F = 12.8, p = 0.001).
- **Dispersion:** group dispersion also differs (betadisper p = 0.006), so the PERMANOVA result reflects a difference in spread as well as in centroid.
- **Differential abundance (DESeq2, adjusted p < 0.01):** 40 ASVs changed between periods. All five significant Muribaculaceae ASVs were higher in late samples; Lachnospiraceae ASVs changed in both directions.

The full code, plots and session information are in `analysis.Rmd` and the rendered `analysis.html`.

## Limitations
- All samples come from one mouse over time, so they are not independent. P-values are descriptive, not population-level inference.
- Taxonomy comes from SILVA v138.1 and is limited at genus level for a ~250 bp V4 region.
- Differential abundance is based on relative (compositional) data.
