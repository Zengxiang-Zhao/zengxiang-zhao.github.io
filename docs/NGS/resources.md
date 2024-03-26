---
layout: default
title: Resources
parent: NGS
date:   2021-11-10 20:28:05 -0400
categories: NGS
---


---

## [NIH resources](https://docs.gdc.cancer.gov/Data/Bioinformatics_Pipelines/DNA_Seq_Variant_Calling_Pipeline/)

In NIH, some good resources that about: 

1. [DNA-Seq analysis pipeline](https://docs.gdc.cancer.gov/Data/Bioinformatics_Pipelines/DNA_Seq_Variant_Calling_Pipeline/#dna-seq-analysis-pipeline)
2. [copy number variation (CNV) pipeline ](https://docs.gdc.cancer.gov/Data/Bioinformatics_Pipelines/CNV_Pipeline/#copy-number-variation-analysis-pipeline)

If you have time, then it's a good place to go through some basic concepts and workflow. The good point it that, it has the command line to tell you how to use the software.

## [How to install illumina bcl-convert software in Ubuntu](https://kb.10xgenomics.com/hc/en-us/articles/360001618231-How-to-troubleshoot-installing-bcl2fastq-or-bcl-convert)

illumina has [bcl-convert packages](https://emea.support.illumina.com/sequencing/sequencing_software/bcl-convert.html) for centos. We have ubuntu OS. thus there is the solution to use the centos package in ubuntu.

```bash
rpm2cpio ./bcl-convert-3.9.3-2.el7.x86_64.rpm | cpio -idmv
```


##  - [resource description for dbsnp and clinvar](https://www.ncbi.nlm.nih.gov/variation/docs/human_variation_vcf/)

## - [dbsnp resourec donwload](https://ftp.ncbi.nih.gov/snp/organisms/human_9606/VCF/)

## - [clinvar resource donwload](https://ftp.ncbi.nlm.nih.gov/pub/clinvar/)

## [对VCF的详细解释说明](https://www.jieandze1314.com/post/cnposts/60/)

## [生物星球](https://www.jieandze1314.com/post/enposts/cancer-biology/)

这里面的文章很不错，介绍了生物信息的相关知识，可以作为基因测序的知识入门。

## [生物星球-solid tumor 介绍](https://www.jieandze1314.com/post/cnposts/102/)

## [FDA drugs](https://www.fda.gov/drugs/science-and-research-drugs/table-pharmacogenomic-biomarkers-drug-labeling)

This website contain the FDA drugs and biomarkers

## [PharmGKB website](https://www.pharmgkb.org/downloads)

This website contain detailed information about drug labeling. Need to read the infor in detail

## [How to define the Tier Level](https://www.pharmgkb.org/page/clinAnnLevels)

Clinical Annotation Levels of Evidence

