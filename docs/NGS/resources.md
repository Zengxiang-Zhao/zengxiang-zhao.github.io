---
layout: default
title: Resources
parent: NGS
date:   2021-11-10 20:28:05 -0400
categories: NGS
---


---

## Learn Basic Info

- ### [Biomarkers can be classified into four types: diagnostic, prognostic, predictive, and therapeutic](https://pmc.ncbi.nlm.nih.gov/articles/PMC5637861/)
  
  > Biomarkers can be classified into four types: diagnostic, prognostic, predictive, and therapeutic. A diagnostic biomarker allows the early detection of the cancer in a noninvasive way and thus the secondary prevention of the cancer. A predictive biomarker allows predicting the response of the patient to a targeted therapy and so defining subpopulations of patients that are likely going to benefit from a specific therapy. A prognostic biomarker is a clinical or biological characteristic that provides information on the likely course of the disease; it gives information about the outcome of the patient [14, 17, 18]. A therapeutic biomarker is generally a protein that could be used as target for a therapy [18].

  The above description comes from the paper: _The following quto comes from the paper: Carlomagno N, Incollingo P, Tammaro V, et al. Diagnostic, Predictive, Prognostic, and Therapeutic Molecular Biomarkers in Third Millennium: A Breakthrough in Gastric Cancer. Biomed Res Int. 2017;2017:7869802. doi:10.1155/2017/7869802_
  

- ### [Sample GenBank Record](https://www.ncbi.nlm.nih.gov/genbank/samplerecord/)
  This page presents an annotated sample GenBank record (accession number U49845) in its GenBank Flat File format. You can see the corresponding live record for U49845, and see examples of other records that show a range of biological features.

  It describe a lot of basic concepts like: Locus, Sequence length, accession number and version and son on.

- ### [Standards - definitions, symbols, nucleotides, codons, amino acids (v2.0)](https://www.hgvs.org/mutnomen/standards.html)
  Describe the the symble meaning used to reprent DNA, Potein...
  
  Such as `NM_005465.7:c.*4943_*4946del`, here `NM_` means protein-coding RNA(mRNA)
  Here's a new version of standards that used in HGVS:https://hgvs-nomenclature.org/stable/background/standards/

  Here's also a good paper describe the standards : [A beginner’s guide to mutation nomenclature using the HGVS recommendations](https://www.sophiagenetics.com/science-hub/hgvs-nomenclature/)

  
  
-  ### [Ensembl Variation - Calculated variant consequences](https://useast.ensembl.org/info/genome/variation/prediction/predicted_data.html)
  Based on the Consequences of the variant to check the impact.

## Databases to Annotate the variants

  - ### [CIViC](https://civicdb.org/assertions/7/summary)
    You can use this website to check the Gene basic information and get the `AMP/ASCO/CAP Category` if this variant has this infor in the web. This web is aimed to create an open source that other people can use and share. And it alos has the API.
  - ### [CKB](https://ckb.genomenon.com/geneVariant/show?geneVariantId=49)
    You can use this website to check the variant evidence including the tumor type, therapy, guidlines, clinical trials. Illumina uses this database.
  - ### [OncoKB](https://www.oncokb.org/companion-diagnostic-devices)
    OncoKB™ is a precision oncology knowledge base developed at Memorial Sloan Kettering Cancer Center that contains biological and clinical information about genomic alterations in cancer.
  - ### [Genome Nexus](https://www.genomenexus.org/)
    Genome Nexus is free and available under an open source license. Genome Nexus is a resource that integrates annotations from multiple sources to help with the interpretation and annotation of genomic variants in cancer. It can be installed in a local environment or private cloud, and integrated with local resources
  - ### [Cancer HotSpots](https://www.cancerhotspots.org/#/home)
    This resource is maintained by the Kravis Center for Molecular Oncology at Memorial Sloan Kettering Cancer Center. It provides information about statistically significantly recurrent mutations identified in large scale cancer genomics data.

    Single residue and in-frame indel mutation hotspots identified in 24,592 tumor samples by the algorithm described in [Chang et al. 2017] and [Chang et al. 2016]
  - ### [OncoTree](https://oncotree.mskcc.org/#/home)
    Show the Tumor Types that is used by MSKCC. You can search the tumor types.
  - ### [My Cancer Genome](https://www.mycancergenome.org/)
    To check Biomarker and diesase associated clinical trials
  - 

## [ICGC Data Portal](https://dcc.icgc.org/)

It contains the gene profile

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

