---
layout: default
title: nf-core knowledge points
parent: nextflow
date:   2021-11-10 20:28:05 -0400
categories: nextflow
---


---
# Install modules from nf-core locally
`nf-core modules install find/concatenate`

It will download the modules from the [nf-core modules](https://nf-co.re/modules/)

# List nf-core modules locally
`nf-core modules list local`

It will show the modules you downloaded from the [nf-core modules](https://nf-co.re/modules/)

# create a module in nf-core
`nf-core modules create --empty-template COWPY2`

Then you can follow the steps in the terminal to create a module that following the nf-core schema

# UI to build nf-core schema

`nf-core pipelines schema build`

You will open a UI to let you add parameters.
