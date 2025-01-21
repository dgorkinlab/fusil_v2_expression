Code for the gene expression analysis of FUSIL genes in Cacheiro et al.

All code is written in R, and provided as jupyter notebooks run with an R kernel. A conda environment with the necessary packages is provided (environment.yml).

# Input files 
(availabile exteranlly, not provided in repo)

### FUSIL gene categorizations from this paper
(store in input_data/FUSIL/)

### GENCODE gene annotations
(store in input_data/GENCODE/)
- Human: [https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_47/gencode.v47.annotation.gtf.gz](url)
- Mouse: [https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_mouse/release_M36/gencode.vM36.annotation.gtf.gz](url)

### Gene expression data from GTEx
(store in input_data/GTEx/)
- A file containing median TPM values per gene is available from the [GTEx portal](https://www.gtexportal.org/home/downloads/adult-gtex/bulk_tissue_expression):
GTEx_Analysis_v10_RNASeQCv2.4.2_gene_median_tpm.gct.gz

### Gene expression data from Cardoso-Moreira, et al. Nature. 2019. PMID:31243369
(store in input_data/Cardoso-Moreira/)
- Human.RPKM.tsv is available from [https://www.ebi.ac.uk/biostudies/arrayexpress/studies/E-MTAB-6814](url) 
- Mouse.RPKM.tsv is available from [https://www.ebi.ac.uk/biostudies/arrayexpress/studies/E-MTAB-6798](url)

### Gene expression data from ENCODE (He, et al. Nature 2020. PMID:32728245; Moore, et al. Nature 2020. PMID:32728249)
(tsv in input_data/ENCODE/)
- This data was accessed through the [ENCODE portal](https://www.encodeproject.org/search/?type=Experiment&control_type%21=%2A&status=released&perturbed=false&replicates.library.biosample.donor.organism.scientific_name=Mus+musculus&biosample_ontology.classification=tissue&assay_title=polyA+plus+RNA-seq&limit=200&lab.title=Barbara+Wold%2C+Caltech&lab.title=Bing+Ren%2C+UCSD&lab.title=Michael+Snyder%2C+Stanford
). The input file here is a metadata file that contains the accession numbers and other metadata about the mouse RNA-seq experiments. This metadata file is processed as described below in the next section.

# Pre-formatting													

Before performing GSEA or other expression analyses, we first standardize the format of each data series, as follows: We create a universal "rowdata" data frame from GENCODE reference annotations where each row is a gene, and the columns contain metadata for each gene including FUSIL bin calls. We are using GENCODE annotations here as a reference gene list onto which we can map expression data from different studies (v47 for human, vM36 for mouse). This is done in the following code books:

- For Human: format_expression_data/format_rowdata_human_gencode.ipynb
- For Mouse: format_expression_data/format_rowdata_mouse_gencode.ipynb

Then, for each data series (i.e. GTEx, Cardoso-Moreira et al, and ENCODE) we create:
1. A "coldata" data frame where each row is a sample in the data series, and the columns contain metadata for each sample. This matrix is created in the following codebooks:
2. An expression matrix (“exprmat”) with numerical expression values (e.g. TPMs) for each gene, measured in each sample. In this matrix, each row is one gene, with row names and order matching those of the rowdata matrix described above. Each column is one sample with column names and order matching the row names of the coldata matrix described above. 
3. And finally, we create a layout matrix with the desired layout of samples in our output heatmaps (e.g. where samples are ordered by tissue and stage). 

The tasks 1-3 above are performed in these codebooks:
- GTEx: format_expression_data/GTEx/format_expression_data_gtex.ipynb
- Cardoso-Moreira et al human: format_expression_data/Cardoso-Moreira/format_expression_data_c-m_human.ipynb
- Cardoso-Moreira et al mouse: format_expression_data/Cardoso-Moreira/format_expression_data_c-m_mouse.ipynb
- ENCODE: format_expression_data/ENCODE/format_expression_data_encode.ipynb (note that this codebook is a bit different than those above becuase it uses the ENCODE metadata file to fetch the RNA-seq data from the ENCODE database, rather than just reformat local data)

# Tissue specificity analysis												

To quantify the tissue specificity of each gene we calculated Tau values for each as previously described Yanai I, et al. Bioinformatics. 2005. PMID:15388519, and benchmarked in Kryuchkova-Mostacci N, et al. Brief Bioinform. 2017. PMID:26891983. This is performed in the codebooks:
- GTEx: characterize_expression/characterize_gtex.ipynb
- Cardoso-Moreira et al human: characterize_expression/characterize_c-m_human.ipynb
- Cardoso-Moreira et al mouse: characterize_expression/characterize_c-m_mouse.ipynb
- ENCODE: characterize_expression/characterize_encode.ipynb

# Gene Set Enrichment Analysis (GSEA)

We performed GSEA using gene expression values in a given tissue-stage to rank genes and testing for enrichment of FUSIL gene sets at either end of the rank distribution. That is performed in the following code books:
- Cardoso-Moreira et al human: perform_GSEA/gsea_c-m_human.ipynb
- Cardoso-Moreira et al mouse: perform_GSEA/gsea_c-m_mouse.ipynb
- ENCODE: perform_GSEA/gsea_encode.ipynb

Note: We've also included code used to determine the highest epxressed tissue for each gene for use in downstream analysis of disease assocaitions. These codebooks are availabile here: highest_tissue/*.ipynb
