# Genomics on the UK Biobank Research Analyses Platform

The G-series (**Genomics**) notebooks found in this repository focus on performing genomics analytics workflows on the RAP. A number of these analyses are contingent on data files created and uploaded to your RAP project in the A-series (**Accessing data**) notebooks (black arrows; figure 1). Figure 1 indicates which A-series notebooks are required to be run to create the requisite files for these genomics analyses. For example, the A-series notebook A0X is required to perform the G203 GWAS analysis and that A0X is required for G209 GWAS in Hail. Further, there is another solely genomics-focused preliminary notebook (G10X) contained in this repo that provides genomic input files for a number of workflows (figure 1). The notebooks running down the centre of figure 1 (G2xx) form the core repository of analytical workflows. As you run through them you will also create files required for subsequent workflows (see the yellow, red and blue arrows; figure 1).

![**Figure 1. Workflow order and input data file relationships between Jupyter notebooks on the RAP.**](genomics.jpg)

\


These notebooks illustrate how to perform many standard analyses (e.g, GWAS, population genomics, functional annotation) that are typically employed in bioinformatic studies. In order to instigate these notebooks on the RAP please see instructions at the bottom of this page. Please note, notebook **G107** is a key component of many subsequent analyses. It takes several hours to run as it processes the first eight chromosomes of final release joint call PLINK formatted files. It can be sped up to only process fewer files, e.g., the last three smaller chromosomes (20-22). The only downside to this is that occasional downstream analyses will not generate significant results. Please see comments in **G107** for instruction if you wish to change code for quicker file processing.

### 

### Please note

-   When working with phenotypic data a Spark cluster is often required (see *Table of Contents* for guidance)

-   Always terminate a kernel before starting a new notebook

### 

# Table of contents for Genomics workflows


**Genomics analytical workflows**

G201 Visualising and downloading hypertension participant data (R; Spark)

G202 Population structure (PCA) ethnicity (R; Spark)

G203 GWAS participant height (R; Single Node)

G204 GWAS hypertension (R; Single Node)

G205 Polygenic risk scores of participant height (R; Single Node)

G206 Polygenic risk scores for hypertension (R; Single Node)

G207 Annotate SNPs from dbSNP and profile ontologies (R; Single Node)

G208 Functional annotations of variants (R; Single Node)


### 

# Python and R libraries

### The following Python libraries are required (guidance on installation found within Notebooks):

*scipy; dxdata; matplotlib.pyplot; numpy; openpyxl; os; pandas; seaborn*

### The following R libraries are required:

*BSgenome.Hsapiens.UCSC.hg38; GenomicRanges; SNPlocs.Hsapiens.dbSNP155.GRCh38; TxDb.Hsapiens.UCSC.hg38.knownGene; VariantAnnotation; VennDiagram; arrow; bigparallelr; bigsnpr; dplyr; dxdata; ggplot2; gprofiler2; grid; hexbin; parallel; readr; readxl; reticulate; scales; skimr; tidyr; tidyverse*

# Notebooks details

## 

### 201 Visualising and downloading hypertension participant data (R; Spark)

**Scope:** Building on notebook **105** we will retrieve, visualise and organise phenotypic data for high blood pressure studies

**Notebook file:** 201_find_and_export_hypertension_participant_data_r.ipynb

**Dependency**

-   **A Spark instance**

**Run info:**

-   runtime: 20min

-   recommended instance: mem1_ssd1_v2_x16

-   estimated cost: \<£0.30

This notebook is a more advanced version of notebook **105** "Export participant data to R". In addition to fetching phenotypic data, we will investigate how to work with different sources and types of phenotypic data. The goal is to retrieve phenotypic annotations that we will use to perform GWAS (notebook **204**) and PRS (notebook **206**) studies of hypertension. We will use four sources:

-   Non-cancer illness code, self-reported

-   Diagnoses - ICD10

-   Underlying (primary) cause of death

-   Blood pressure measurement during the interview (systolic and diastolic)

Next, we will examine and visualize the data -- the empirical probability densities for systolic and diastolic blood pressures for self-reported hypertension vs. non-affected and systolic and diastolic as scatter plots with the colour-coded condition. We will also apply CDC blood pressure level cut-offs to find which patients measured above the high blood pressure (hypertension) threshold during the interview. Next, we will visualise the overlap between three sources of information (self-reported, ICD10 diagnoses and diastolic blood pressure) on the Venn diagram. Finally, we will prepare tabular file formats that will be input for further analyses.

## 202 Population structure (PCA) ethnicity (R; Spark)

**Scope:** This notebook show how to retrieve phenotype data, load genotype data and perform Principal Component Analysis (PCA) with packages bigstatsr and bigsnpr (Privé, Aschard, and Blum 2017). Additionally, it demonstrates why pruning and removing long-range Linkage Disequilibrium (LD) are two important steps before computing PCs in order to capture population structure (Abdellaoui et al. 2013).

**Notebook file:** 202_population_structure_PCA_ethnicity.ipynb

**Dependency:**

-   **A Spark instance**

<!-- -->

-   **107** - files prefixed *maf_flt_8chroms\**

**Run info:**

-   runtime: 30min

-   recommended instance: mem1_ssd1_v2_x16

-   cost: \~£0.70

## 203 GWAS participant height (R; Single Node)

**Scope:** We will use phenotypes produced by notebook **103**. This includes three sources of information about participant's high blood pressure status: self-reported, ICD10 diagnoses and systolic blood pressure cut-offs, as well as values of blood pressure measurement. The phenotype data file also includes the `eid` identifier column, which allows us to match the rows in the `phenotype` table to corresponding genotypes saved in PLINK format. We will use linear and logistic models required for GWAS analyses using `bigstatsr` and `bigsnpr` packages (Privé, Aschard, and Blum 2017) in R.

**Notebook file:** 203_example_GWAS_participant_height.ipynb

**Dependency:**

-   **103** - file *pheno_height_sex_age_500k.xlsx*

-   **107** - *maf_flt_8chroms*\* prefixed files

**Run info:**

-   runtime: 30min

-   recommended instance: mem1_ssd1_v2_x16

-   cost: \~£0.70

## 204 GWAS hypertension (R; Single Node)

**Scope:** This notebook is a more advanced version of the GWAS workflow presented in notebook **203**. We will use phenotypes produced by **Notebook 201**. This includes three sources of information about participant's high blood pressure status: self-reported, ICD10 diagnoses and systolic blood pressure cut-offs, as well as values of blood pressure measurement. We will also use we use the partial SVD (or PCA) of a Filebacked Big Matrix to calculate covariates representing the population structure for the GWAS model. The phenotype data file also includes the `eid` column, which allows us to match the rows in the `phenotype` table to corresponding genotypes saved in PLINK format. We will linear and logistic models required for GWAS analyses using `bigstatsr` and `bigsnpr` packages (Privé, Aschard, and Blum 2017) in R.

**Notebook file:** 204_GWAS_hypertension.ipynb

**Dependency:**

-   **201** - pheno_data_hypertension.csv

-   **202** - ethnicity_processed.csv

**Run info:**

-   runtime: 1h

-   recommended instance: mem1_ssd1_v2_x16

-   estimated cost: \<£1.00

## 205 Polygenic risk scores of participant height (R; Single Node)

**Scope:** Polygenic scores are important tools for understanding complex genetic associations. In this notebook, we show how to derive polygenic scores based on summary statistics and a matrix of correlation between genetic variants. We will use R package `bigsnpr` that implements the LDpred2 method (<https://doi.org/10.1093/bioinformatics/btaa1029>). As input, we will use the same data as in the previous GWAS examples. This notebook focus on a linear model using participant height data. In the next one, we will use a more complex example with logistic regression and blood pressure data.

**Notebook file:** 205_example_polygenic_risk_scores_participant_height.ipynb

**Dependency:**

-   **107** - *maf_flt_8chroms*\* prefixed files

**Run info:**

-   runtime: 1h

-   recommended instance: mem1_ssd1_v2_x16

-   estimated cost: \<£1.00

## 206 Polygenic risk scores for hypertension (R; Single Node)

**Scope:** Polygenic risk scores are important tools for understanding complex genetic associations. In this notebook, we show how to derive polygenic scores based on summary statistics and a matrix of correlation between genetic variants. We will use R package bigsnpr that implements the LDpred2 method (<https://doi.org/10.1093/bioinformatics/btaa1029>). As input, we will use the hypertension example data used before for GWAS example. This notebook focus on a logistic regression model using simulated participant data.

**Notebook file:** 206_polygenic_risk_scores_for_hypertension.ipynb

**Dependency:**

-   **201** - *pheno_data_hypertension.csv*\*

-   **107** - *maf_flt_8chroms*\* prefixed files

**Run info:**

-   runtime: \>30m

-   recommended instance: mem1_ssd1_v2_x16

-   estimated cost: \>£0.50

## 207 Annotate SNPs from dbSNP and profile ontologies (R; Single Node)

**Scope:** In this notebook we will annotate SNPs using a dbSNP database and retrieve overrepresented GO terms, using the list of significant variants from the GWAS on participant height in **Notebook 203.** We will also retrieve and plot overrepresented GO terms.

**Notebook file:** 207_annotate_SNPs_to_dbSNP_and_profile_ontologies.ipynb

**Dependency:**

-   **BioConductor install**

<!-- -->

-   **203** - *height_signif_snp.csv*

**Run info:**

-   runtime: 30h

-   recommended instance: mem1_ssd1_v2_x16

-   estimated cost: \<£0.70

## 208 Functional annotations of variants (R; Single Node)

**Scope:** This notebook shows how to use the genome annotations and gene models to translate variant genomic coordinates into functional annotations.

**Notebook file:** 208_functional_annotations_for_variants.ipynb

**Dependency:**

-   **BioConductor install**

<!-- -->

-   **203** - *height_signif_snp.csv*

**Run info:**

-   runtime: 30m

-   recommended instance: mem1_ssd1_v2_x16

-   estimated cost: \<£0.70

### 

# How to run Jupyter notebooks on the RAP

Follow the steps below to run this Jupyter Notebook:

-   Click on the Tools menu and select "JupyterLab"
-   Click on the "New JupyterLab" button to start a JupyterLab instance.
-   Select a name and a project from the dropdown menu for your JupyterLab environment.
-   Select the priority for your JupyterLab environment; "High" is recommended.
-   Under "Cluster Configuration", select "Single Node" or "Spark" according to requirements listed in *Table of Contents*.
-   Set instance type (e.g. mem1_sdd1_v2_x16) and duration for you environment. See individual notebooks for recommendations.
-   Click on "Start Environment"
-   You will see your environment go from "Initialising" to "Launching" and then "Ready". This may take some time depending on the priority selected; at busy times, it may be necessary to select high priority to avoid long initialising times. Once the environment is ready, click on "Open".
-   A JupyterLab session will open. On the left side of the screen, you will see a a "DNA Nexus" tab, allowing you to open notebooks directly from your project environment. If you have saved this notebook under you project environment, just double click to open it.
-   Press "Ctrl" + "Enter" to run code cells. An hourglass icon on the JupyterLab tab in your browser indicates that the code is running. Please note that depending on number of chromosomes and SNPs and your instance type, code may take some time to run.
