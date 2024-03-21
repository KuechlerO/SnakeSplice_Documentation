# Knatterton - Let's SnakeSplice 

Hey Leute, diese Seite ist die maßgeschneiderte Dokumentation für euch!
Hier werde ich euch erklären, wie ihr am besten SnakeSplice auf unserem "Knatterton"-Server benutzt.


## 1. Installation
Alle Installationsschritte sind bereits im Tutorial beschrieben.

Bitte geht dafür auf die [Tutorial-Seite "Installation"](../getting_started/installation.md) und folgt den Anweisungen.
Danach kehrt danach einfach hierher zurück.

## 2. Test-Datensätze
Es gibt leider ein Problem mit dem STAR-Aligner auf unserem Server, sodass invalide BAM-Dateien erzeugt werden.
Deswegen habe ich euch die BAM-files schon vorgeneriert und auf dem Server abgelegt.
Ihr könnt die fertig-generierten BAM files also direkt verwenden, ohne die Alignierung selbst durchzuführen.

Insgesamt sind diese Dateien für euch unter `/home/sharedFolder/` verfügbar:

- `star` - Ordner mit den fertigen BAM-Dateien
- `simulated_reads_chr21_controls_selected` - Ordner mit den simulierten Reads der Kontrollgruppe
- `simulated_reads_chr21_cases_selected` - Ordner mit den simulierten Reads der Fallgruppe


Damit die BAM-files aus dem `star`-Ordner verwendet werden, ist es am einfachsten den Ordner in euren SnakeSplice-Ordner zu kopieren.
Dafür könnt ihr folgenden Befehl verwenden:

```bash title="Copy BAM files"
cp -r /home/sharedFolder/star /home/username/snakesplice/output/module1_qc_preproc_and_alignment/output/
```

!!! tipp FASTQ Files Hack
    Anstatt neben den BAM-Files auch die FASTQ-Daten in euren SnakeSplice Ordner zu kopieren, könnt ihr auch einfach 
    die Pfade in eurer `pep/pep_config.yaml` Datei anpassen (siehe nächster Tipp).

!!! tip "input_metadata.csv & pep_config.yaml"
    Bitte denkt daran, die `input_metadata.csv` und `pep_config.yaml` Dateien entsprechend anzupassen.
    Ihr könnt die Dateien auch hier runterladen: [input_metadata.csv](input_metadata.csv) und [pep_config.yaml](pep_config.yaml).



## 3. Snakemake austricksen
Damit Snakemake die fertigen BAM-Dateien nicht nochmal generiert, müssen wir ein wenig tricksen.
Dabei führen wir die Tools in zwei Schritten aus:

1. Zuerst führen wir nur `trimmomatic` und `fastqc` aus.
2. Danach führen wir einen `--touch` aus, um die BAM-Dateien als fertig zu markieren.
3. Abschließend führen wir die restlichen Tools aus.


### 3.1 Trimmomatic & FastQC
Zuerst wollen wir lediglich `trimmomatic` & `fastqc` ausführen, um die Qualität der Reads zu überprüfen.


``` yaml title="Configuration - Only Trimmomatic & FastQC" hl_lines="7 9 10"
[...]
# ------- 1.1 Module: Quality-Control, Preprocessing, and Alignment --------
module1_qc_preprocessing_and_alignment_settings:
  switch_variables:
    # Switch variables to decide which features are to be included
    run_check_of_strandedness: False      # Run python script to check strandedness of input read files
    run_trimmomatic: True                # Run trimmomatic: Quality trimming of input reads
    run_kraken2: False                    # Run Kraken2: Check for potential contamination via Kraken2
    run_fastqc_before_trimming: True      # Run fastqc: Quality control of input reads before trimming
    run_fastqc_after_trimming: True       # Run fastqc: Quality control of input reads after trimming
    run_alignment:
      use_star: False                     # Run STAR alignment: Align reads to reference genome
      use_olego: False                    # Run Olego alignment: Align reads to reference genome
    run_bamstats: False                  # Run bamstats: Quality control of aligned reads
    run_qualimap: False                   # Run Qualimap: Quality control of alignment results
    run_deeptools: False                  # Run deeptools: Quality control of alignment results
    run_multiqc: False                     # Run multiqc: Summarize all quality control results into one output-file
[...]
```

Führe dazu nun den Snakemake Befehl aus:

```bash title="Run SnakeSplice Mod1 - Only Trimmomatic & FastQC"
snakemake --profile profiles/profile_local
```

### 3.2 Einen Touch ausführen
Nachdem dieser Schritt abgeschlossen ist, könnt wir die restlichen Tools aktivieren und führen einen `--touch` aus, um die fertigen BAM-Dateien zu markieren.

``` yaml title="Configuration - All Tools" hl_lines="7 9 10 12 14 15 17"
[...]
# ------- 1.1 Module: Quality-Control, Preprocessing, and Alignment --------
module1_qc_preprocessing_and_alignment_settings:
  switch_variables:
    # Switch variables to decide which features are to be included
    run_check_of_strandedness: False      # Run python script to check strandedness of input read files
    run_trimmomatic: True                # Run trimmomatic: Quality trimming of input reads
    run_kraken2: False                    # Run Kraken2: Check for potential contamination via Kraken2
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
```

Die Ausführung des `--touch` Befehls markiert die BAM-Dateien als fertig und verhindert, dass sie erneut generiert werden.

```bash title="Run SnakeSplice Mod1 - Touch BAM files"
snakemake --profile profiles/profile_local --touch
```

### 3.3 Restliche Tools ausführen
Dananach können wir die restlichen Tools ausführen, ohne dass die BAM-Dateien erneut generiert werden.
Dafür reicht es, den normalen Snakemake Befehl auszuführen.
    
```bash title="Run SnakeSplice Mod1 - All selected Tools"
snakemake --profile profiles/profile_local
```

## 4. Fertig!
Das war's auch schon! Ihr habt nun erfolgreich SnakeSplice auf unserem
"Knatterton"-Server ausgeführt.
Doch das war erst der Anfang.
Drei weitere Module warten auf euch, um eure Daten zu analysieren.
Also nichts wie los!

[Hier geht's weiter zum nächsten Modul](../getting_started/tutorial_gene_fusions.md).
