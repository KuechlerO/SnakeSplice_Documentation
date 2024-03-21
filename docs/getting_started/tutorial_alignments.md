# 1. Step: Quality control, pre-processing and alignment
The first step in our tutorial is to perform quality control, pre-processing and alignment of the input data.
This is done by the first module of SnakeSplice, which is called `module1_qc_preproc_alignment`.

We decide to use the following tools for this module:

- **Read trimming**: Trimmomatic
- **Alignment**: STAR
- **Quality control (Reads)**: Kraken2
- **Quality control (Reads)**: FastQC (after and before trimming)
- **Quality control (Alignment)**: Bamtools/Samtools
- **Quality control (Alignment)**: Qualimap
- **Quality control (summary)**: MultiQC

## 1.1 Main configuration file
In order to run the first module **and only the first module**, you need to adjust the main configuration file `config_files/config_main.yaml`.


You can choose an abitrary name for your project by setting the `run_identifier` variable.
Then set all module switches to `False` except for the first module.
An excerpt of an example configuration file is shown below:

``` yaml title="config_main.yaml" hl_lines="3 11"
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
  run_module4_splicing_pattern_analysis: False
[...]
```

The complete configuration file for this example can be downloaded here: [config_main.yaml](example_data/mod1/config_main.yaml).
Make sure to have this file saved under `config_files/config_main.yaml` in your project directory.

## 1.2 Module configuration file
In order to specify which tools and parameters are used for the first module, you need to adjust the module configuration file `config_files/config_module1_qc_preproc_alignment.yaml`.
This configuration file is located in the `config_files` directory.

Make sure to only switch on the tools of your choice.
Also, since we are using STAR for alignment and know that our data is sampled from the 21. chromosome of the human genome, we need to specify the reference genome build accordingly (GRCh38_chr21).
The other options can be left as they are.

An excerpt of the configuration file with the relevant settings is shown below:


``` yaml title="config_module1_qc_preproc_alignment.yaml" hl_lines="8 9 10 11 13 15 16 18 27"
[...]

# ------- 1.1 Module: Quality-Control, Preprocessing, and Alignment --------
module1_qc_preprocessing_and_alignment_settings:
  switch_variables:
    # Switch variables to decide which features are to be included
    run_check_of_strandedness: False      # Run python script to check strandedness of input read files
    run_trimmomatic: True                # Run trimmomatic: Quality trimming of input reads
    run_kraken2: True                    # Run Kraken2: Check for potential contamination via Kraken2
    run_fastqc_before_trimming: True      # Run fastqc: Quality control of input reads before trimming
    run_fastqc_after_trimming: True       # Run fastqc: Quality control of input reads after trimming
    run_alignment:
      use_star: True                     # Run STAR alignment: Align reads to reference genome
      use_olego: False                    # Run Olego alignment: Align reads to reference genome
    run_bamstats: True                  # Run bamstats: Quality control of aligned reads
    run_qualimap: True                   # Run Qualimap: Quality control of alignment results
    run_deeptools: False                  # Run deeptools: Quality control of alignment results
    run_multiqc: True                     # Run multiqc: Summarize all quality control results into one output-file
[...]

# 2.5 Alignments
  alignment_settings:
    # 2.5.1 STAR alignment: Align reads to reference genome
    star_alignment_settings:
      # Reference genome sequence file, that is used to create the STAR index
      reference_genome_build:
        "GRCh38_chr21"
[...]
```

To follow this tutorial you can use the `config_module1_qc_preproc_alignment.yaml` file as is provided through the SnakeSplice repository. However, the example configuration file can also be downloaded here: [config_module1_qc_preproc_alignment.yaml](example_data/mod1/config_module1_qc_preproc_alignment.yaml).


## 1.3 Execution
Now that we have set up the main and module configuration files, we can execute the first module by running the SnakeSplice pipeline.
But let's first make a quick check if everything is set up correctly.
To do so, navigate to the main directory (`snakesplice`) of SnakeSplice and execute a dry run:
``` bash title="Dry run"
snakemake --profile profiles/profile_local/ -n
```

You should see a list of all rules that would be executed if you would run the pipeline.
In total <del>152</del> rule executions should be listed.

If this is the case, you can now execute the pipeline.
To do so run the following command:

``` bash title="Run the pipeline"
snakemake --profile profiles/profile_local/

# Or if you want to use a specific number of cores
snakemake --profile profiles/profile_local/ --cores 4

# Run the pipeline in the background
nohup snakemake --profile profiles/profile_local/ &

# Run pipeline in background & write logs to a specified file
nohup snakemake --profile profiles/profile_local &> output-$(date +"%Y-%m-%dT%H-%M-%S").txt &
```

## 1.4 Results & HTML report
The results of the pipeline are stored in the directory `output`.
Each module has its own subdirectory (names corresponding to your settings in `config_main.yaml`)
in which the results of its respective tools (subdir names are defined in `config_module1_qc_preproc_alignment.yaml`)
are saved. Feel free to explore the results directly in your file system.
However, indeed the most convenient way to explore the results is to use our HTML report.

!!! tip
    Normally, the generation of the HTML report is performed at the very end when results from all modules are available.
    Though, if you already want to have a look at the results of the first module, you can generate the HTML report by 
    setting up and executing the 0. module.

    Otherwise you can skip this step and proceed with the second module.


To generate the HTML report, you need to simply switch on the 0. module in the main configuration file `config_main.yaml` and execute the pipeline again.

``` yaml title="config_main.yaml" hl_lines="5"
[...]

module_switches:
  # ------- 1.0 Module: Report Generation ---------
  run_module0_report_generation: True

  # ------- 1.1 Module: Quality-Control, Preprocessing, and Alignment --------
  run_module1_qc_preprocessing_and_alignment: True

  # ------- 1.2 Module: Detection of gene fusion events ---------
  run_module2_gene_fusion_detection: False
[...]
```

After the execution of the pipeline, you can find the HTML report in the output directory of the 0. module.
If you have kept the default settings, then the HTML report is located in the 
`output/module0_report_generation/output/snakesplice_reports/<condition>.html`.


## 1.5 Troubleshooting
Here are some issues that might occur during the execution of first module of the pipeline:

**Problems during Conda environment creation**  
Your might encounter an error message like this:

``` bash title="Error message"
[...]
CreateCondaEnvironmentException:
Could not create conda environment from /home/kuechleo/snakesplice/modules/SM1_module_qc_preproc_and_alignment/rules/../envs/check_strandedness_env.yaml
[...]
Your conda installation is not configured to use strict channel priorities. This is however crucial for having robust and correct environments (for details, see https://conda-forge.org/docs/user/tipsandtricks.html). Please consider to configure strict priorities by executing 'conda config --set channel_priority strict'.
```

**Possible solutions:**  

1. **Suggestion**: Please set strict channel priorities by executing `conda config --set channel_priority strict` as suggested in the error message.
2. **Error details**: `configure: error: zlib development files not found`  
**Solution**: Install zlib development files by running `sudo apt-get install zlib1g-dev` or `sudo dnf install zlib-devel`.
3. **Error details**: `configure: error: libbzip2 development files not found`:  
**Solution**: Install libbzip2 development files by running `sudo apt-get install libbz2-dev` or `sudo dnf install bzip2-devel`.



## 1.6 Celebrate
**Congratulations!**

You have successfully executed the first module of SnakeSplice and even inspected the results. 
You're on a good way to become a SnakeSplice expert!

Let's proceed with the second module: [Detection of gene fusion events](tutorial_gene_fusions.md).
