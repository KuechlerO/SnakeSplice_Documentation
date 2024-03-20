# The last step: Report generation
We are almost done!
The last module of SnakeSplice is a special one.
It is the module that provides you with the __final report__ of the SnakeSplice pipeline.
Ain't that exciting?

This module is referenced as `module0_report_generation`.
Its job is to collect the results of the previous modules and to create a comprehensive report that summarizes the findings of the SnakeSplice pipeline.

!!! warning "Required input"  
    While this module can be run independently, it is important to note that it makes only sense to run this module if you have successfully executed (some) other modules.

    **Required input files**: None required, yet as many as possible recommended.


## 5.1 Main configuration file
Here we go again:
In order to run the last module, you need to adjust the main configuration file `config_files/config_main.yaml`.
Simply switch on the last/first module and keep all other modules switched on as well.

!!! note "Module selection"
    All modules that you want to include in the report need to be switched on in the main configuration file.

    **Note**: The report can only be generated if the respectively selected modules have been executed before.
    Also make sure that the summary reports are switched on in the respective module configuration files.


``` yaml title="config_main.yaml" hl_lines="8 11 14 17 20"
[...]
global_variable:
  run_identifier: "tutorial_run"

[...]
module_switches:
  # ------- 1.0 Module: Report Generation ---------
  run_module0_report_generation: True

  # ------- 1.1 Module: Quality-Control, Preprocessing, and Alignment --------
  run_module1_qc_preprocessing_and_alignment: True

  # ------- 1.2 Module: Detection of gene fusion events ---------
  run_module2_gene_fusion_detection: True
  
  # ------- 1.3 Module: Detection and Quantification of gene products (transcripts) and analysis ---------
  run_module3_gene_expression_quantification_and_analysis: True

  # ------- 1.4 Module: Splicing Patterns Analysis ---------
  run_module4_splicing_pattern_analysis: True
[...]
```

The complete configuration file for this example can be downloaded here: [config_main.yaml](example_data/mod0/config_main.yaml).
Make sure to have this file saved under `config_files/config_main.yaml` in your project directory.


## 5.2 Module configuration file
This module does not require a module configuration file.



## 5.3 Execution
__Hyperfantastic!__
We are ready to generate our final summary reports.

**But wait!**
To guarantee that the report is generated correctly, we need to make sure that no old reports and 
HTML files are blocking the generation of the new report.
To do so, please navigate to the main directory (`snakesplice`) of SnakeSplice and execute 
the following command to remove all old reports and HTML files:

``` bash title="Remove old reports"
# 1. Navigate to the main directory of SnakeSplice
# 2. Call the shell script to remove old reports
bash scripts/remove_all_reports.sh
```

Now all old reports and HTML files have been removed and we can proceed with the execution of the last module.
Let's continue by making a quick check to see whether everything is set up correctly.

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

## 5.4 Results & HTML report
By default the results of the last module are stored in the ` output/module0_report_generation/output/snakesplice_reports/` directory.
You will find there for each condition a separate directory with a comprehensive report in HTML format.
Please open it in your web browser to explore the results.


## 5.5 Troubleshooting
Here are some issues that might occur during the execution of the pipeline:

<span style="color:red;">TODO</span>


## 5.6 Celebrate
__**Woohoo!**__
We made it to the end of the SnakeSplice pipeline.
Amazing work!

You have now successfully executed the SnakeSplice pipeline and generated a comprehensive report that summarizes the findings of the SnakeSplice pipeline.
Please take your time to explore the report and to get familiar with the results.
