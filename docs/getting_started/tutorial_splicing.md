# 4. Step: Splicing Patterns Analysis
Welcome to the fourth and most important module, `module4_splicing_pattern_analysis`.
This module is the core of SnakeSplice and provides a comprehensive analysis of the splicing patterns in your RNA-Seq data.


!!! warning "Required input"
    The splicing pattern analysis requires the output of the first module (BAM files).
    Alternatively you can provide the necessary input BAM files and specify their location in the configuration file.

     **Required input**: BAM files (aligned reads) from the first module.

In this part of the tutorial we will use the following tools for this module:

- **rMATS**: rMATS is a tool for differential splicing analysis.
- **LeafCutter**: LeafCutter is a tool for splicing quantitative trait loci (sQTL) analysis.
- **PJD-Tool**: PJD-Tool is a tool for the identification of private splice junctions.
- **Whippet**: Whippet is a tool for the identification of alternative splicing events.


## 4.1 Main configuration file
The known procedure applies:
To run the fourth module, you need to adjust the main configuration file `config_files/config_main.yaml`.
Simply switch on the fourth module.

``` yaml title="config_main.yaml" hl_lines="20"
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
  run_module3_gene_expression_quantification_and_analysis: False

  # ------- 1.4 Module: Splicing Patterns Analysis ---------
  run_module4_splicing_pattern_analysis: True
[...]
```

The complete configuration file for this example can be downloaded here: [config_main.yaml](example_data/mod4/config_main.yaml).
Make sure to have this file saved under `config_files/config_main.yaml` in your project directory.


## 4.2 Module configuration file
In addition to switching the respective tools on, SnakeSplice provides the possibility to adjust the parameters for the splicing pattern analysis in the module configuration file `config_files/config_module4_splicing_pattern_analysis.yaml`.

So let's specifcy the parameters for the fourth module!
Make sure to switch on __rMATS__, __LeafCutter__, __PJD-Tool__, __Whippet__, and the __overall summary__, which summarizes this module's results.


!!! note "Reference transcriptome build"
    **Remember**: We only need to reference the 21. chromosome (GRCh38_chr21).
    Thus, we can set "GRCh38_chr21" under `reference_transcriptome_build`.

!!! tip "BAM files"
    The integrated splice analysis tools rely on BAM files (aligned reads) from the first module.
    If you have not executed the first module, you can provide the necessary input BAM files and specify their location in the configuration file.

    If you followed the tutorial from the beginning, you can use the BAM files from the first module.
    Thus: `input_dir_of_bam_files: "star"`


An excerpt of the configuration file with the relevant settings is shown below:

```yaml title="config_module4_splicing_pattern_analysis.yaml" hl_lines="11 13 14 15 17 22 28"
[...]

module4_splicing_patterns_analysis_settings:
  # =============== 1. Module wide settings =============

  # =============== 1.1 Switch variables =============
  switch_variables:
    run_dexseq_analysis: False
    run_fraser_analysis: False
    run_irfinder_analysis: False
    run_leafcutter_analysis: True
    run_leafcutter_md_analysis: False
    run_private_junction_detection: True
    run_rmats: True
    run_whippet: True

    run_overall_summary: True

[...]

  input_dir_of_bam_files:
    "star"

  # Attributes of aligned files (input BAM files)
  bam_files_attributes:
    # Reference genome build that was used to create the input BAM files
    reference_genome_build:
      "GRCh38_chr21"          # "GRCh37" or "GRCh38" or "GRCh38_chr21" (for testing purposes)

    filename_extension:   # File extension of the input BAM files
      ".sorted.bam"
    sorted:
      "position"      # Either position (for position-sorted) or name (for name-sorted)

[...]
```
To follow this tutorial you can use the `config_module4_splicing_pattern_analysis.yaml` file as is provided through the SnakeSplice repository. However, the example configuration file can also be downloaded here: [config_module4_splicing_pattern_analysis.yaml](example_data/mod4/config_module4_splicing_pattern_analysis.yaml).


!!! tip "Adjust tool parameters"
    You want chagne the false discovery rate (FDR) for the rMATS analysis or increase the minimum number of reads for the LeafCutter analysis?
    No problem! You can adjust the parameters for the respective tools in the module configuration file.



## 4.3 Execution
**Magnificent!**
We managed to set up the main and module configuration files for the fourth module.
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

## 4.4 Results & HTML report
The results of the splicing pattern analysis are stored in the `output/module4_splicing_pattern_analysis/output/` directory.
Feel free to explore the results directly in your file system.


## 4.5 Troubleshooting
Did you encounter any issues during the execution of the SnakeSplice pipeline?
Here are some common issues and possible solutions:

**1. Problems with Leafcutter container: User namespace** 
``` bash title="Error message"
FATAL: while extracting [...].snakemake/singularity/6a375c3a043145beefdd13e360f324de.simg:  
root filesystem extraction failed:  
extract command failed:  
ERROR : Failed to create user namespace: user namespace disabled : exit status 1
```

The error message you're seeing suggests that user namespaces are disabled on your system. 
User namespaces are a feature of the Linux kernel that Singularity uses to provide container functionality.

**Possible Solution**: 
Was singularity installed correctly? 
Simply installing it with conda **does not** guarantee that it will work correctly.  
**Solution**: Install Singularity from the official website: [Singularity Installation](https://sylabs.io/singularity/) or use `sudo dnf install singularity-ce`.


**2. Problems with Leafcutter container: Missing files** 
``` bash title="Error message"
IOError: [Errno 2] No such file or directory: 'output/module4_splicing_patterns_analysis/output/leafcutter/leafcutter_output/Bud13_Variant/juncfiles.txt' 
```

Normally Snakemake takes care of providing all required files to the docker container (usually the whole working directory is made available).
However, corner cases exists: 
- Soft links are not supported

**Suggestion**: 
Do not use soft links as input our output!


**3. Problems with Leafcutter container: No space left** 
``` bash title="Error message"
FATAL ERROR: write_file: failed to create file /image/root/usr/local/lib/R/site-library/BH/include/boost/thread/once.hpp, because No space left on device: exit status 1' 
```

Sometimes the docker daemon will accumulate a lot of data without really needing it.
Thus, the system is blocked with useless data garbage.

**Suggestion**: 
You can use pruning to delete all dangling data (containers, networks, and images), which are not under current usage:
 `docker system prune --all`

## 4.6 Celebrate
__**Hooray!**__
The SnakeSplice pipeline has been executed successfully and the results of the splicing pattern analysis are ready for exploration.

**Actually,...**  
...we are not quite done yet.
The last module of SnakeSplice is a special one.
It is the module that provides you with the __final report__ of the SnakeSplice pipeline.
So, no time to rest yet!

Please continue with the tutorial to discover the creation of final reports with SnakeSplice:
[Report Generation](tutorial_report_generation.md).
