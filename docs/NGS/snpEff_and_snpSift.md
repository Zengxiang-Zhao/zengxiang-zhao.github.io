---
layout: default
title: snpEff and snpSift command line knowledge
parent: NGS
date:   2022-10-31 12:15: -0400
categories: NGS
---


---
## How to use `snpSift` to annotate vcf files with multiple databses

While actually you can use multiple times of `snpSift` command line to do the annotation. And each time use different annotation databse.

'''bash
//use dbsnp do the annotation
snpSift annotate -v ./databases/common_all_20180423_dbsnp.vcf ./inputs/format.vcf > outputs/format.dbsnp.vcf 
// use clinvar do the annotation
snpSift annotate -v ./databases/clinvar.vcf outputs/format.dbsnp.vcf > outputs/format.dbsnp.clinvar.vcf

'''

At last the `format.dbsnp.clinvar.vcf` will contain all the annotation of dbsnp and clinvar databses.


