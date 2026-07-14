---
layout: default
title: nextflow knowledge points
parent: nextflow
date:   2021-11-10 20:28:05 -0400
categories: nextflow
---


---

# clean work directory

- Dry run: First check what the command will delete without actually removing anything: `nextflow clean -n`
- Clean everything except the last run: Remove all work directory files from prior pipeline executions, but keep the most recent run's files intact:`nextflow clean -f -before $(nextflow log -q | tail -n 1)`
- Clean a specific pipeline execution: Target and delete files for a specific run name (e.g., focused_ride): `nextflow clean focused_ride -f`
