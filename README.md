> This is a modified version of the [nf-core/mag](https://github.com/nf-core/mag) pipeline in 2025/06/30, originally developed and licensed under the MIT License by [nf-core/mag team](https://github.com/nf-core/mag/graphs/contributors).

> 📌 本 README 為基於 [nf-core/mag](https://github.com/nf-core/mag) 的修改版本，僅用於學習與紀錄用途，2025/06/30。
> 原始專案由 [nf-core/mag team](https://github.com/nf-core/mag/graphs/contributors) 開發，原始授權為 MIT License。


# Quick run test
```bash
nextflow run main.nf -profile test,docker --outdir results

nextflow run nf-core/mag \
   -profile test,docker \
   --gtdb_db https://data.ace.uq.edu.au/public/gtdb/data/releases/release220/220.0/auxillary_files/gtdbtk_package/full_package/gtdbtk_r220_data.tar.gz \
   --outdir ./results
```

<h1>
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="docs/images/mag_logo_mascot_dark.png">
    <img alt="nf-core/mag" src="docs/images/mag_logo_mascot_light.png">
  </picture>
</h1>

[![GitHub Actions CI Status](https://github.com/nf-core/mag/actions/workflows/ci.yml/badge.svg)](https://github.com/nf-core/mag/actions/workflows/ci.yml)
[![GitHub Actions Linting Status](https://github.com/nf-core/mag/actions/workflows/linting.yml/badge.svg)](https://github.com/nf-core/mag/actions/workflows/linting.yml)[![AWS CI](https://img.shields.io/badge/CI%20tests-full%20size-FF9900?labelColor=000000&logo=Amazon%20AWS)](https://nf-co.re/mag/results)[![Cite with Zenodo](http://img.shields.io/badge/DOI-10.5281/zenodo.3589527-1073c8?labelColor=000000)](https://doi.org/10.5281/zenodo.3589527)
[![nf-test](https://img.shields.io/badge/unit_tests-nf--test-337ab7.svg)](https://www.nf-test.com)
[![Cite Publication](https://img.shields.io/badge/Cite%20Us!-Cite%20Publication-orange)](https://doi.org/10.1093/nargab/lqac007)

[![Nextflow](https://img.shields.io/badge/nextflow%20DSL2-%E2%89%A525.04.2-23aa62.svg)](https://www.nextflow.io/)
[![run with conda](http://img.shields.io/badge/run%20with-conda-3EB049?labelColor=000000&logo=anaconda)](https://docs.conda.io/en/latest/)
[![run with docker](https://img.shields.io/badge/run%20with-docker-0db7ed?labelColor=000000&logo=docker)](https://www.docker.com/)
[![run with singularity](https://img.shields.io/badge/run%20with-singularity-1d355c.svg?labelColor=000000)](https://sylabs.io/docs/)
[![Launch on Seqera Platform](https://img.shields.io/badge/Launch%20%F0%9F%9A%80-Seqera%20Platform-%234256e7)](https://cloud.seqera.io/launch?pipeline=https://github.com/nf-core/mag)

[![Get help on Slack](http://img.shields.io/badge/slack-nf--core%20%23mag-4A154B?labelColor=000000&logo=slack)](https://nfcore.slack.com/channels/mag)[![Follow on Twitter](http://img.shields.io/badge/twitter-%40nf__core-1DA1F2?labelColor=000000&logo=twitter)](https://twitter.com/nf_core)[![Follow on Mastodon](https://img.shields.io/badge/mastodon-nf__core-6364ff?labelColor=FFFFFF&logo=mastodon)](https://mstdn.science/@nf_core)[![Watch on YouTube](http://img.shields.io/badge/youtube-nf--core-FF0000?labelColor=000000&logo=youtube)](https://www.youtube.com/c/nf-core)

## Introduction

**nf-core/mag** is a bioinformatics best-practise analysis pipeline for assembly, binning and annotation of metagenomes.

<p align="center">
    <img src="docs/images/mag_workflow.png" alt="nf-core/mag workflow overview" width="90%">
</p>

## Pipeline summary

## Usage

> [!NOTE]
> If you are new to Nextflow and nf-core, please refer to [this page](https://nf-co.re/docs/usage/installation) on how to set-up Nextflow. Make sure to [test your setup](https://nf-co.re/docs/usage/introduction#how-to-run-a-pipeline) with `-profile test` before running the workflow on actual data.

By default, the pipeline currently performs the following: it supports both short and long reads, quality trims the reads and adapters with [fastp](https://github.com/OpenGene/fastp), [AdapterRemoval](https://github.com/MikkelSchubert/adapterremoval), or [trimmomatic](https://github.com/usadellab/Trimmomatic) and [Porechop](https://github.com/rrwick/Porechop), and performs basic QC with [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/), and merges multiple sequencing runs.

The pipeline then:

- assigns taxonomy to reads using [Centrifuge](https://ccb.jhu.edu/software/centrifuge/) and/or [Kraken2](https://github.com/DerrickWood/kraken2/wiki)
- performs assembly using [MEGAHIT](https://github.com/voutcn/megahit) and [SPAdes](http://cab.spbu.ru/software/spades/), and checks their quality using [Quast](http://quast.sourceforge.net/quast)
- (optionally) performs ancient DNA assembly validation using [PyDamage](https://github.com/maxibor/pydamage) and contig consensus sequence recalling with [Freebayes](https://github.com/freebayes/freebayes) and [BCFtools](http://samtools.github.io/bcftools/bcftools.html)
- predicts protein-coding genes for the assemblies using [Prodigal](https://github.com/hyattpd/Prodigal), and bins with [Prokka](https://github.com/tseemann/prokka) and optionally [MetaEuk](https://www.google.com/search?channel=fs&client=ubuntu-sn&q=MetaEuk)
- performs metagenome binning using [MetaBAT2](https://bitbucket.org/berkeleylab/metabat/src/master/), [MaxBin2](https://sourceforge.net/projects/maxbin2/), and/or with [CONCOCT](https://github.com/BinPro/CONCOCT), and checks the quality of the genome bins using [Busco](https://busco.ezlab.org/), [CheckM](https://ecogenomics.github.io/CheckM/), or [CheckM2](https://github.com/chklovski/CheckM2) and optionally [GUNC](https://grp-bork.embl-community.io/gunc/).
- Performs ancient DNA validation and repair with [pyDamage](https://github.com/maxibor/pydamage) and [freebayes](https://github.com/freebayes/freebayes)
- optionally refines bins with [DAS Tool](https://github.com/cmks/DAS_Tool)
- assigns taxonomy to bins using [GTDB-Tk](https://github.com/Ecogenomics/GTDBTk) and/or [CAT](https://github.com/dutilh/CAT) and optionally identifies viruses in assemblies using [geNomad](https://github.com/apcamargo/genomad), or Eukaryotes with [Tiara](https://github.com/ibe-uw/tiara)

Furthermore, the pipeline creates various reports in the results directory specified, including a [MultiQC](https://multiqc.info/) report summarizing some of the findings and software versions.

## Usage

> [!NOTE]
> If you are new to Nextflow and nf-core, please refer to [this page](https://nf-co.re/docs/usage/installation) on how to set-up Nextflow. Make sure to [test your setup](https://nf-co.re/docs/usage/introduction#how-to-run-a-pipeline) with `-profile test` before running the workflow on actual data.

```bash
nextflow run nf-core/mag -profile <docker/singularity/podman/shifter/charliecloud/conda/institute> --input '*_R{1,2}.fastq.gz' --outdir <OUTDIR>
```

or

```bash
nextflow run nf-core/mag -profile <docker/singularity/podman/shifter/charliecloud/conda/institute> --input samplesheet.csv --outdir <OUTDIR>
```

> [!WARNING]
> Please provide pipeline parameters via the CLI or Nextflow `-params-file` option. Custom config files including those provided by the `-c` Nextflow option can be used to provide any configuration _**except for parameters**_; see [docs](https://nf-co.re/docs/usage/getting_started/configuration#custom-configuration-files).

For more details and further functionality, please refer to the [usage documentation](https://nf-co.re/mag/usage) and the [parameter documentation](https://nf-co.re/mag/parameters).

## Pipeline output

To see the results of an example test run with a full size dataset refer to the [results](https://nf-co.re/mag/results) tab on the nf-core website pipeline page.
For more details about the output files and reports, please refer to the
[output documentation](https://nf-co.re/mag/output).

### Group-wise co-assembly and co-abundance computation

Each sample has an associated group ID (see [input specifications](https://nf-co.re/mag/usage#input_specifications)). This group information can be used for group-wise co-assembly with `MEGAHIT` or `SPAdes` and/or to compute co-abundances for the binning step with `MetaBAT2`. By default, group-wise co-assembly is disabled, while the computation of group-wise co-abundances is enabled. For more information about how this group information can be used see the documentation for the parameters [`--coassemble_group`](https://nf-co.re/mag/parameters#coassemble_group) and [`--binning_map_mode`](https://nf-co.re/mag/parameters#binning_map_mode).

When group-wise co-assembly is enabled, `SPAdes` is run on accordingly pooled read files, since `metaSPAdes` does not yet allow the input of multiple samples or libraries. In contrast, `MEGAHIT` is run for each group while supplying lists of the individual readfiles.

## Credits

nf-core/mag was written by [Hadrien Gourlé](https://hadriengourle.com) at [SLU](https://slu.se), [Daniel Straub](https://github.com/d4straub) and [Sabrina Krakau](https://github.com/skrakau) at the [Quantitative Biology Center (QBiC)](http://qbic.life). [James A. Fellows Yates](https://github.com/jfy133) and [Maxime Borry](https://github.com/maxibor) at the [Max Planck Institute for Evolutionary Anthropology](https://www.eva.mpg.de) joined in version 2.2.0.

Other code contributors include:

- [Antonia Schuster](https://github.com/AntoniaSchuster)
- [Alexander Ramos](https://github.com/alxndrdiaz)
- [Carson Miller](https://github.com/CarsonJM)
- [Daniel Lundin](https://github.com/erikrikarddaniel)
- [Danielle Callan](https://github.com/d-callan)
- [Gregory Sprenger](https://github.com/gregorysprenger)
- [Jim Downie](https://github.com/prototaxites)
- [Phil Palmer](https://github.com/PhilPalmer)
- [@willros](https://github.com/willros)
- [Adam Rosenbaum](https://github.com/muabnezor)
- [Diego Alvarez](https://github.com/dialvarezs)
- [Nikolaos Vergoulidis](https://github.com/IceGreb)

Long read processing was inspired by [caspargross/HybridAssembly](https://github.com/caspargross/HybridAssembly) written by Caspar Gross [@caspargross](https://github.com/caspargross)

We thank the following people for their extensive assistance in the development of this pipeline:

- [Alexander Peltzer](https://github.com/apeltzer)
- [Phil Ewels](https://github.com/ewels)
- [Gisela Gabernet](https://github.com/ggabernet)
- [Harshil Patel](https://github.com/drpatelh)
- [Johannes Alneberg](https://github.com/alneberg)
- [Maxime Garcia](https://github.com/MaxUlysse)
- [Michael L Heuer](https://github.com/heuermh)
- [Alex Hübner](https://github.com/alexhbnr)

## Contributions and Support

If you would like to contribute to this pipeline, please see the [contributing guidelines](.github/CONTRIBUTING.md).

For further information or help, don't hesitate to get in touch on the [Slack `#mag` channel](https://nfcore.slack.com/channels/mag) (you can join with [this invite](https://nf-co.re/join/slack)).

## Citations

If you use nf-core/mag for your analysis, please cite the preprint as follows:

> **nf-core/mag: a best-practice pipeline for metagenome hybrid assembly and binning**
>
> Sabrina Krakau, Daniel Straub, Hadrien Gourlé, Gisela Gabernet, Sven Nahnsen.
>
> NAR Genom Bioinform. 2022 Feb 2;4(1):lqac007. doi: [10.1093/nargab/lqac007](https://doi.org/10.1093/nargab/lqac007).

Additionally you can cite the pipeline directly with the following doi: [10.5281/zenodo.3589527](https://doi.org/10.5281/zenodo.3589527)

An extensive list of references for the tools used by the pipeline can be found in the [`CITATIONS.md`](CITATIONS.md) file.

You can cite the `nf-core` publication as follows:

> **The nf-core framework for community-curated bioinformatics pipelines.**
>
> Philip Ewels, Alexander Peltzer, Sven Fillinger, Harshil Patel, Johannes Alneberg, Andreas Wilm, Maxime Ulysse Garcia, Paolo Di Tommaso & Sven Nahnsen.
>
> _Nat Biotechnol._ 2020 Feb 13. doi: [10.1038/s41587-020-0439-x](https://dx.doi.org/10.1038/s41587-020-0439-x).
