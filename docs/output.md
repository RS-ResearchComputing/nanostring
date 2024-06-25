# nf-core/nanostring: Output

## Introduction

This document describes the output produced by the pipeline. Most of the plots are taken from the MultiQC report, which summarises results at the end of the pipeline.

The directories listed below will be created in the results directory after the pipeline has finished. All paths are relative to the top-level results directory.

## Pipeline overview

The pipeline is built using [Nextflow](https://www.nextflow.io/)
and processes data using the following steps:

- [Quality Control](#qualitycontrol) - compute QC metrics for NanoString data, assess data quality
- [Normalize](#normalize) - compute normalized gene counts for given RCC files
- [Annotate](#annotate) - annotate the normalized gene counts with metadata from samplesheet
- [Gene Score](#gene-scores) - computes gene scores with user supplied YAML formatted gene sets
- [Gene Heatmap](#gene-count-heatmaps) - visualizes gene counts in a heatmap format
- [MultiQC](#multiqc) - aggregate report, describing results of the whole pipeline
- [Pipeline information](#pipeline-information) - Report metrics generated during the workflow execution

### Quality Control

This step currently uses the NACHO NanoString analysis package to perform basic QC of the input RCC files. Several quality metrics are created and the majority of these are available in the MultiQC report. These have been created using the `bin/nacho_qc.R` script in the pipeline. In addition to this, the output also has two NACHO reports, once with outliers highlighted and once without highlighting outliers in the visualizations:

<details markdown="1">
<summary>Output files</summary>

- `QC/NACHO/`
  - `png/`: Directory containing the generated qc plots for the MultiQC report.
  - `hk_detected_mqc.txt`: Text file containing the housekeeping genes that have been detected in the data.
  - `NanoQC.html`: Basic Nacho QC report - a standalone HTML file that can be viewed in your web browser.
  - `NanoQC_with_outliers.html`: The same as above, but with highlighted outliers.
  - `normalized_qc_mqc.txt`: QC metrics from NACHO. This is also shown in table format in a MultiQC table.

</details>

### Normalize

This holds the normalized gene expression data, normalized using NACHO (`bin/nacho_norm.R`).

<details markdown="1">
<summary>Output files</summary>

- `normalized_counts/`
  - `*_normalized_counts.tsv`: Normalized count matrix, unmodified.
  - `*_normalized_counts_wo_HKnorm.tsv`: Normalized count matrix without Housekeeping-normalization applied (`housekeeping_norm=FALSE`), unmodified.

</details>

### Annotate

<details markdown="1">
<summary>Output files</summary>

This holds the normalized and non-housekeeping-normalized annotated gene expression data. There are two tables each - one for endogenous genes of interest, one for housekeeping genes. Annotation is performed using the custom script `bin/write_out_prepared_gex.R` in the pipeline. These tables are also part of the MultiQC report.

- `annotated_tables/`
- `*_normalized_counts_Norm_GEX_HK.tsv`: TSV table holding all normalized housekeeping gene expression values with annotation.
- `*_normalized_counts_Norm_GEX_ENDO.tsv`: TSV table holding the normalized endogenous gene expression values with annotation.
- `*_normalized_counts_wo_HKnorm_Norm_GEX_HK.tsv`: TSV table holding the non-HK-normalized endogenous gene expression values with annotation.
- `*_normalized_counts_wo_HKnorm_Norm_GEX_ENDO.tsv`: TSV table holding the non-HK-normalized endogenous gene expression values with annotation.

</details>

### Gene Scores

<details markdown="1">
<summary>Output files</summary>

This holds the outputs for any gene scores computed in the pipeline. The heatmap is also part of the MultiQC report as well as a small table that explains which genes were used by the algorithm of choice and which genes were not found in the input data.

- `gene_scores/`
- `signature_scores_mqc.txt`: Gene Scores for input data based on YAML configuration
- `signature_scores_qc_mqc.txt`: QC information for the computed gene scores - gives you a hint if e.g. genes were missing from the input data

</details>

### Gene-Count Heatmaps

<details markdown="1">
<summary>Output files</summary>

This holds the gene-count heatmaps generated for the normalized and non-housekeeping-normalized annotated gene expression data. These heatmaps are also part of the MultiQC report.

- `gene_heatmaps/`
- `gene_heatmap_mqc.png`: Gene-count heatmap for HK-normalized data.
- `wo_HKnorm_gene_heatmap_mqc.png`: Gene-count heatmap for non-HK-normalized data.

</details>

### MultiQC

<details markdown="1">
<summary>Output files</summary>

- `multiqc/`
  - `multiqc_report.html`: a standalone HTML file that can be viewed in your web browser.
  - `multiqc_data/`: directory containing parsed statistics from the different tools used in the pipeline.
  - `multiqc_plots/`: directory containing static images from the report in various formats.

</details>

[MultiQC](http://multiqc.info) is a visualization tool that generates a single HTML report summarising all samples in your project. Most of the pipeline QC results are visualised in the report and further statistics are available in the report data directory.

Results generated by MultiQC collate pipeline QC from supported tools e.g. FastQC. The pipeline has special steps which also allow the software versions to be reported in the MultiQC output for future traceability. For more information about how to use MultiQC reports, see <http://multiqc.info>.

### Pipeline information

<details markdown="1">
<summary>Output files</summary>

- `pipeline_info/`
  - Reports generated by Nextflow: `execution_report.html`, `execution_timeline.html`, `execution_trace.txt` and `pipeline_dag.dot`/`pipeline_dag.svg`.
  - Reports generated by the pipeline: `pipeline_report.html`, `pipeline_report.txt` and `software_versions.yml`. The `pipeline_report*` files will only be present if the `--email` / `--email_on_fail` parameter's are used when running the pipeline.
  - Reformatted samplesheet files used as input to the pipeline: `samplesheet.valid.csv`.
  - Parameters used by the pipeline run: `params.json`.

</details>

[Nextflow](https://www.nextflow.io/docs/latest/tracing.html) provides excellent functionality for generating various reports relevant to the running and execution of the pipeline. This will allow you to troubleshoot errors with the running of the pipeline, and also provide you with other information such as launch commands, run times and resource usage.
