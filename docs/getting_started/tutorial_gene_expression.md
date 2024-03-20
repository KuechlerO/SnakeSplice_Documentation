# 3. Step: Gene Expression Analysis
Now we get to the third module of SnakeSplice, which is called `module3_gene_expression_quantification_and_analysis`.

Here we want to identify and quantify differentially expressed genes and perform a basic analysis of the gene expression data.
Additionally, SnakeSplice includes the possibility to extract enriched or depleted gene sets by performing a gene set enrichment analysis (GSEA).

!!! warning "Required input"  
    While the pseudo-alignment tools (i.e. Salmon, Kallisto, and RSEM) do not rely on BAM files 
    (the first module's output), the expression outlier detection tool **OUTRIDER** does!

    Thus, if you want to include OUTRIDER be aware that you need to have successfully executed the first module.
    Alternatively you can provide the necessary input BAM files and specify their location in the configuration file.

    **Required input files for OUTRIDER**: BAM files (aligned reads) from the first module.

In this part of the tutorial we will use the following tools for this module:

- **Salmon**: Salmon is a tool for quantifying the expression of transcripts from RNA-Seq data.
- **DESeq2**: DESeq2 is a tool for differential gene expression analysis.
- **GSEA**: Gene Set Enrichment Analysis (GSEA) is a computational method that determines whether an a priori defined set of genes shows statistically significant, concordant differences between two biological states.

## 3.1 Main configuration file
Same procedure as every time:
In order to run the third module, you need to adjust the main configuration file `config_files/config_main.yaml`.
Simply switch on the third module.

``` yaml title="config_main.yaml" hl_lines="17"
[...]
global_variable:
  run_identifier: "tutorial_run"

[...]
module_switches:
  # ------- 1.0 Module: Report Generation ---------
  run_module0_report_generation: False

  # ------- 1.1 Module: Quality-Control, Preprocessing, and Alignment --------
  run_module1_qc_preprocessing_and_alignment: True

  # ------- 1.2 Module: Detection of gene fusion events ---------
  run_module2_gene_fusion_detection: False
  
  # ------- 1.3 Module: Detection and Quantification of gene products (transcripts) and analysis ---------
  run_module3_gene_expression_quantification_and_analysis: True

  # ------- 1.4 Module: Splicing Patterns Analysis ---------
  run_module4_splicing_pattern_analysis: False
[...]
```

The complete configuration file for this example can be downloaded here: [config_main.yaml](example_data/mod3/config_main.yaml).
Make sure to have this file saved under `config_files/config_main.yaml` in your project directory.


## 3.2 Module configuration file
Let's specify the parameters for the third module by adjusting the module configuration file `config_files/config_module3_gene_expression.yaml`:
Make sure to switch on __Salmon__ and the __overall summary__, which summarizes this module's results.


!!! note "Reference transcriptome build"
    **Remember**: We only need to reference the 21. chromosome (GRCh38_chr21).
    Thus, we can set "GRCh38_chr21" under `reference_transcriptome_build`.

!!! tip "Use trimmed FASTQ files"
    Since we have used __Trimmomatic__ in our first pipeline's module, we can set `use_trimmed_fastq_files` to `True`.
    SnakeSplice will then automatically find the trimmed FASTQ files and use them as input for Salmon.

An excerpt of the configuration file with the relevant settings is shown below:


```yaml title="config_module3_gene_expression.yaml" hl_lines="11 16 23 28 36"
[...]
# =============== Configuration file for module: Detection and quantification =============
# Use this config-file to switch on/off the features that you need for your personal research.
# Furthermore, you can set the detailed parameters for the respective features.


module3_gene_expression_quantification_and_analysis_settings:
  # =============== 1.1 Switch variables =============
  switch_variables:
    run_outrider_analysis:    False   # Run Outrider analysis: Identifies gene expression outliers
    run_salmon_analysis:      True    # Run salmon analysis: Quantifies transcripts
    run_kallisto_analysis:    False   # Run kallisto analysis: Quantifies transcripts
    run_rsem_analysis:        False   # Run rsem analysis: Quantifies transcripts & gene expression
    run_mae_analysis:         False   # Run mae analysis: Detects mono-allelic expression

    run_overall_summary:      True

[...]

  # Reference transcritpome build
  # ATTENTION: Currently, only GRCh38 is supported
  reference_transcriptome_build:
    "GRCh38_chr21"          # "GRCh37" or "GRCh38" or "GRCh38_chr21" (for testing purposes)

  # Whether to use the trimmed FASTQ files as input for the quantification tools
  # If yes: The trimmed FASTQ files created by the QC & Preprocessing module are used as input [Trimmomatic output]
  use_trimmed_fastq_files:  
    True                # Either True or False

[...]

  # --------- 2.2 Salmon settings ---------
  salmon_settings:
    # Whether to include a Gene Set Enrichment Analysis (GSEA)
    run_gse_analysis:
      True
[...]
```

To follow this tutorial you can use the `config_module3_gene_expression.yaml` file as is provided through the SnakeSplice repository. However, the example configuration file can also be downloaded here: [config_module3_gene_expression.yaml](example_data/mod3/config_module3_gene_expression.yaml).



## 3.3 Execution
Superb!
We have set up the main and module configuration files for the third module.
Now we can continue with the execution of the SnakeSplice pipeline.

But let's first make a quick check if everything is set up correctly.
To do so, navigate to the main directory (`snakesplice`) of SnakeSplice and execute a dry run:

``` bash title="Dry run"
snakemake --profile profiles/profile_local/ -n
```

You should see a list of all rules that would be executed if you would run the pipeline.
If the dry run was successful, you can execute the pipeline by running the following command:

``` bash title="Execution"
snakemake --profile profiles/profile_local/

# Alternative: Run pipeline in background & write logs to a specified file
nohup snakemake --profile profiles/profile_local &> output-$(date +"%Y-%m-%dT%H-%M-%S").txt &
```

## 3.4 Results & HTML report
By default the results of the second module are stored in the `output/module3_gene_expression_quantification_and_analysis/output/` directory.
Feel free to explore the results directly in your file system.


## 3.5 Troubleshooting
Here are some issues that might occur during the execution of first module of the pipeline:

<span style="color:red;">TODO</span>


## 3.6 Celebrate
__**Yippie!**__
The third module of SnakeSplice is now running and will provide you with an analysis of the gene expression data.
Wonderful!

Please continue with the tutorial to learn how to use SnakeSplice for splicing pattern analysis:
[Analysis of splicing patterns](tutorial_splicing.md).
