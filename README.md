Differential Gene Expression (DGE) Pipeline with PyDESeq2

A Python pipeline for differential gene expression analysis on RNA-seq count matrices using pydeseq2, with diagnostic visualizations (volcano, heatmap, PCA) and optional Enrichr pathway enrichment via gseapy. Designed to run both as a CLI script and directly inside Google Colab.
Features
Synthetic or Custom Inputs: Generates negative binomial demo count and metadata tables (--demo) or loads user-supplied --counts and --metadata CSV files.
Sample & Count Filtering: Validates sample alignment between metadata and count columns, drops empty rows, and filters low-count genes (row sum >= --min-counts).
Differential Expression (pydeseq2): Fits DeseqDataSet (design=~<condition_col> with fallback to design_factors for older APIs) and runs DeseqStats for [condition_col, treated, control].
Visual Diagnostics: Exports a volcano plot, row-z-scored top-30 gene clustermap on log2(normed_counts + 1), and SVD-based sample PCA plot.
Optional Pathway Enrichment: Queries Enrichr (GO_Biological_Process_2023 and KEGG_2021_Human for "human") via gseapy when --enrich is passed and significant genes are found.


Installation (Google Colab):
!pip install -q pydeseq2 gseapy

Restart the runtime (Runtime -> Restart session) if prompted or if newly installed packages are not yet loaded.


Usage
In a Colab Notebook Cell:
if __name__ == "__main__":
    import sys
    if "ipykernel" in sys.modules:
        main(["--demo"])
    else:
        main()

To run on your own dataset in a notebook cell, pass your arguments instead:
main([
    "--counts", "counts.csv",
    "--metadata", "metadata.csv",
    "--condition-col", "condition",
    "--treated", "treated",
    "--control", "control",
    "--outdir", "results",
])

From the Command Line:
python dge_pipeline.py --demo --outdir results
python dge_pipeline.py \
  --counts counts.csv \
  --metadata metadata.csv \
  --condition-col condition \
  --treated treated \
  --control control \
  --lfc 1.0 \
  --padj 0.05 \
  --min-counts 10 \
  --enrich \
  --outdir results


Notes
Use real gene symbols (e.g., HGNC symbols) and ensure internet access when enabling --enrich; synthetic GENE0000 IDs from --demo will not map to human pathways.

counts values are rounded and cast to int prior to DeseqDataSet initialization.
