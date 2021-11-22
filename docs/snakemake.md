---
layout: default
title : snakemake
date:   2021-10-5 20:28:05 -0400
categories: snakemake
nav_order: 101
---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 安装

`conda install -c bioconda -c conda-forge snakemake`

## 整体范式

```python
rule rulename:
    input:
        string
    output: 
        string
    shell:
        string
```

例子：

```python
rule bwa_map:
    input:
        "data/genome.fa"
        "data/samples/A.fastq"
    output:
        "mapped_reads/A.bam"
    shell:
        "bwa mem {input} | samtools view -Sb - > {output}"
```

> **Note:**`rulename`规则的名字，`input 和 output`:一般是输入文件的名称，用string来表示，包括路径。例如‘data/genome.fasta’，`shell`部分就是执行部分，如果是在terminal端执行，则是使用`shell`字符，如果是使用python或r，则改为`script`字符。`input or output file lists can contain arbitrary Python statements, as long as it returns a string, or a list of strings.`

> input 和 output 都是文件名称，即在程序运行过程中需要用到的文件，分别是输入文件和输出文件。bwa 为在terminal端运行的python命令， 在shell 指令下使用`{}`来引用本rule中的其他指令。若`{input}`有多个文件，则多个文件以空格隔开。例如shell 中的input在运行中实际转化为`data/genome.fa data/samples/A.fastq`. 

> 使用shell命令，如果是多行的话，则可以分开写，前一行最后留下一个空格即可，snake make会把两者连接在一起执行。

<!-- > `expand`：用于input 扩充多个文件。例如`bam=expand("sorted_reads/{sample}.bam", sample=['A','B'])`就是扩展到`sorted_reads/A.bam sorted_reads/B.bam` 两个文件。其中的'sample'可以理解为python语言中的变量。 -->

## snakemake -np （干执行）

并不真正的执行命令，只是显示运行过程。

```md
-n : dry-run
-p : 显示执行过程中的命令

```

若都是具体的文件没有使用{sample}(wildcards)来指代文件，则可以直接使用. 若有wildcards则需要指明target文件。若有多个target 文件则需要都写明。例如`snakemake -np mapped_reads/A.bam mapped_reads/B.bam` ,在shell 中可以简化为`snakemake -np mapped_reads/{A,B}.bam`。

snakemake 首先是生成DAG，因此需要用到所有的rule，若snakemake需要指明target文件，则应指明最终的target文件，而不能指明中间的某个target文件。

## snakemake --cores num (真执行)

```python
snakemake --cores 1 mapped_reads/A.bam

```

若有wildcard则需要指明target文件。 Snakemake only re-runs jobs if one of the input files is newer than one of the output files or one of the input files will be updated by another job.

> **Note:** `--forceall` 强制执行，例如虽然已经有了最终文件，若是想再执行一次可以使用 `snakemake --forceall --cores 1 mapped_reads/A.bam`


## wildcards

```python
output:
        "mapped_reads/{sample}.bam"
shell:
        "samtools sort -T sorted_reads/{wildcards.sample} "
        "-O bam {input} > {output}"

```

{sample},即括号中间的变量为wildcard。在input或者output中可以直接使用括号包裹变量，output与input中的变量应相同。若在shell指令中想引用input或者output中的变量，则应该使用`{wildcards.sample}`来引用。可以理解为`wildcards`是snakemake中的全局dicationary，保存snakemake中的所有变量。

## 画出DAG图

```python
snakemake --dag sorted_reads/{A,B}.bam.bai | dot -Tsvg > dag.svg
```

> **Note:**可修改变量`sorted_reads/{A,B}.bam.bai`(target文件)和`dag.svg`(文件名称)

## expand (扩展多个文件)

```python
expand("sorted_reads/{sample}.bam", sample=SAMPLES)
```

若SAMPLES=['A','B'], 那么我们实际上获得的是a list of files. `["sorted_reads/A.bam", "sorted_reads/B.bam"]`,

当pattern中含有多个wildcard时则更有用，例如：

```python
expand("sorted_reads/{sample}.{replicate}.bam", sample=SAMPLES, replicate=[0, 1])
```

则会产生更多的文件`["sorted_reads/A.0.bam", "sorted_reads/A.1.bam", "sorted_reads/B.0.bam", "sorted_reads/B.1.bam"]`

> **Note:** Snakemake works _backwards_ from requested output, and not from available input.

## 引用不同输入文件

```python
rule bcftools_call:
    input:
        fa="data/genome.fa",
        bam=expand("sorted_reads/{sample}.bam", sample=SAMPLES),
        bai=expand("sorted_reads/{sample}.bam.bai", sample=SAMPLES)
    output:
        "calls/all.vcf"
    shell:
        "samtools mpileup -g -f {input.fa} {input.bam} | "
        "bcftools call -mv - > {output}"
```

当有多个输入文件或输出文件时，为了操作方面可以给其命名，例如 `fa=...`，然后在shell指令中引用时可以使用名称指代，例如`{input.fa}`

对于较长的shell指令，应将其分割成短的指令，python会将其整合成一个。在输入和输出文件中可以包含任何的python命令，只要这个命令返回的是 *a string* or *a list of string*

## 使用脚本

```python
rule plot_quals:
    input:
        "calls/all.vcf"
    output:
        "plots/quals.svg"
    script:
        "scripts/plot-quals.py"
```

在这里，把python的code放在plot-quals.py脚本中。plot-quals.py可以放在`scripts/plot-quals.py`中，内容可以是对数据进行统计或者是画图。plot-quals.py 的内容如下：

```python
import matplotlib
matplotlib.use("Agg") # 设定matplotlib后台运行的环境，在Jupyter中则是 matplotlib%linline
import matplotlib.pyplot as plt
from pysam import VariantFile # 使用VariantFile来引用snakemake中的文件

quals = [record.qual for record in VariantFile(snakemake.input[0])]
plt.hist(quals)

plt.savefig(snakemake.output[0])
```

> **Note:** python 脚本的路径是与snakefile的路径相对的，所有snakefile中的变量，都可以通过全局变量`snakemake`获得，例如`snakemake.output[0]`. 这里需要指出的是snakemake.output and snakemake.input 代表的是 a list of file names. 因此若指定某一个文件要使用index，即便只有一个文件，`snakemake.output[0]`。

当在snakefile 中有多行python code 时，最好使用script 指令，把code放到脚本文件中。python脚本可以是如下形式：

```python
def do_something(data_path, out_path, threads, myparam):
    # python code

do_something(snakemake.input[0], snakemake.output[0], snakemake.threads, snakemake.config["myparam"])

```

## target rule

为了运行snakemake file，我们需要在命令行中`snakemake --cores 1` 后添加target file。为了方便我们可以在snakefile 中的最前端添加一个rule 作为targets。在默认情况下，snakemake会默认第一个rule为target，且第一个target不能含有wildcard，若有则会报错。

因此，最好在snakefile 中的第一个rule添加target rule。并把想要执行的target file 放到input 指令中例如：

```python
rule all:
    input:
        "plots/quals.svg" ,
        "plots/second.svg"
```

这样我们在run-dry 时可以直接用`snakemake -n`。在input 或者output 中的string若没有`,`进行分割，则python会整合成为一个string。因此若是多个文件，则应使用`,`进行分割。

> **Note:**除了target rule 必须在最前端为，其他rule 并没有顺序要求，也就是可以随便放。


## flags

- `--forcerun`: 强制执行某一个rule，而在DAG这个rule之后的rule 也要重新运行。例如：`snakemake --cores 1 --forcerun bcftools_call`。

- `--reason`: 显示执行这个rule的原因。

- `--cores`: snakemake运行所需要的CPU 数目。这是snakemake 运行所必需要有的flag，可以直接指定cores 的数目，如果没有写则默认使用全部的cores。

- `--forceall`: 强制重新运行整个workflow，既是之前已经有结果的rule也会再运行一次。

- '--summary': prints a table associating each output file with the rule used to generate it, the creation date and optionally the version of the tool used for creation is provided. Further, the table informs about updated input files and changes to the source code of the rule after creation of the output file. 

## 增加threads

可以在rule 中增加threads 来提高运算速度。在rule中增加threads 指令，如下：

```python
rule bwa_map:
    input:
        "data/genome.fa",
        "data/samples/{sample}.fastq"
    output:
        "mapped_reads/{sample}.bam"
    threads: 8 # threads 为8
    shell:
        "bwa mem -t {threads} {input} | samtools view -Sb - > {output}"
```

若想在shell 中使用threads 变量，则使用`{threads}`表示。在默认状态下threads=1。在实际的运行过程中，threads 的数量不会超过snakemake所需要的CPU 数目。例如 `snakemake --cores 10`，而`bwa_map`需要8个cores，那么同一时间只能有一个`bwa_map`可以运行，剩下的2个cores可以运行其他的rules。

## config files

使用config file 的目的是为了让snakemake workflow 更具有可扩展性。可以使workflow 适应新的数据，只需要改动config file就可以重复利用workflow。config file可以使用JSON 或者 YAML 格式。在snakefile 中需要使用`configfile` 指令来说明。需要把`configfile: "config.yaml"`放在snakefile 的最前端。例如：

```yaml
samples:
    A: data/samples/A.fastq
    B: data/samples/B.fastq
```

snakemake 会自动把config.yaml 中的变量保存到全局变量`config`中。例如：

```python
rule bcftools_call:
    input:
        fa="data/genome.fa",
        bam=expand("sorted_reads/{sample}.bam", sample=config["samples"]), # 通过config指明其变量
        bai=expand("sorted_reads/{sample}.bam.bai", sample=config["samples"])
    output:
        "calls/all.vcf"
    shell:
        "samtools mpileup -g -f {input.fa} {input.bam} | "
        "bcftools call -mv - > {output}"
```

在dry-run的过程中，可以看出即使之前的rule中没有指明变量，但是仍然可以执行，这是因为snakemake生成的DAG是从后往前递归生成。例如在bcftools_call 中我们需要用到`config["samples"]`中的A，B。那么之前的需要先运行的rule中的变量则需要替换为A，或者B。而在`bcftools_call`中需要指定变量，则也是因为有`expand`的缘故。

对于yaml 格式的要求可以参考[runoob](https://www.runoob.com/w3cnote/yaml-intro.html)。以下为简单介绍：

```yaml
# dictionary
key: 
    child-key: value
    child-key2: value2

# list
names
 - A
 - B
 - C

# list another type
key: [value1, value2, ...]

```

## Input functions

Snakemake workflow 的执行过程需要经过以下三步：
1. In the **initialization** phase, the files defining the workflow are parsed and all rules are instantiated. ： 初始化，即rule中的input，output指令中的文件可以确定的确定。如果有wildcard则会根据情况进行确定。可以通过编写input function 传递config 中的文件路径。
1. In the **DAG** phase, the directed acyclic dependency graph of all jobs is built by filling wildcards and matching input files to output files. ： 建立非循环图，根据输入文件和输出文件的相互依赖关系建立各Jobs直接的关系，从而形成图。
1. In the **scheduling** phase, the DAG of jobs is executed, with jobs started according to the available resources.： 执行阶段， 根据已有的资源开始执行，例如需要的threads数量和cores 的数量。

想 expand functions 可以在**initialization** 阶段生成文件名称。在这一阶段，我们并不知道wildcard values 和 rule dependencies。因此向bwa_map rule 中的input file 我们并不能确定，此文件的确定只能在DAG步中获得。如果想在第一步就知道其具体的名称，则需要input function来提取config 文件中的路径。

```python
def get_bwa_map_input_fastqs(wildcards):
    return config["samples"][wildcards.sample]

rule bwa_map:
    input:
        "data/genome.fa",
        get_bwa_map_input_fastqs
    output:
        "mapped_reads/{sample}.bam"
    threads: 8
    shell:
        "bwa mem -t {threads} {input} | samtools view -Sb - > {output}"
```

`wildcards` 为snakefile 中的全局变量，可以通过属性获得其中的值，例如`wildcards.sample`。因此，当一个job 中的wildcard values 确定了，那么input function 就可以执行，返回config 中的文件路径。

## Rule parameters

有时需要在shell command 中添加其他的参数，可以在rule 中添加`params`指令。而引用时直接通过属性引用。如下：

```python
rule bwa_map:
    input:
        "data/genome.fa",
        lambda wildcards: config["samples"][wildcards.sample]
    output:
        "mapped_reads/{sample}.bam"
    params: # 其他的参数
        rg=r"@RG\tID:{sample}\tSM:{sample}"
    threads: 8
    shell:
        "bwa mem -R '{params.rg}' -t {threads} {input} | samtools view -Sb - > {output}"
```

> **Note:**引用参数`{params.rg}`

## Logging

在执行workflow时，需要把每个job的输出放到log file中，以方便查看。例如出错信息。可以在rule 中添加log 指令，与output指令的格式相似，但是log 指令下的string 并不适用于`rule matching`，并且在出错后也不会被清除。如下：

```python
rule bwa_map:
    input:
        "data/genome.fa",
        get_bwa_map_input_fastqs
    output:
        "mapped_reads/{sample}.bam"
    params:
        rg=r"@RG\tID:{sample}\tSM:{sample}"
    log:
        "logs/bwa_mem/{sample}.log" # log 文件
    threads: 8
    shell:
        "(bwa mem -R '{params.rg}' -t {threads} {input} | "
        "samtools view -Sb - > {output}) 2> {log}" 
```

> **Note:** `2>filename`:Redirect stderr to file "filename." 把错误信息保存到filename中。

具体解释参考
[STDERR OUTPUT](https://tldp.org/LDP/abs/html/io-redirection.html)
。如果再运行一次，则会覆盖掉原来的错误信息。最好把log file 都存放到`logs/` 文件夹下。 prefixed by the rule or tool name.
>log file中的wildcard 则必须与output file 中的wildcard相同。若output file 中没有wildcard 则log file中也不应该有wildcard。

## Temporary and protected files

- Temporary

在workflow 中有的文件是为了其他rule的执行而生成的，有时这种中间文件又特别大，占内存。因此，在全部执行完以后，没有必要保留这种中间的大文件，可以直接删除来保留内存空间。**you can mark output files as temporary.**  Snakemake will delete the marked files for you, once all the consuming jobs (that need it as input) have been executed.使用`temp()`使文件在无用时删除，例如：

```python
rule bwa_map:
    input:
        "data/genome.fa",
        get_bwa_map_input_fastqs
    output:
        temp("mapped_reads/{sample}.bam") # 当不需要时会删除文件，若前面的文件夹也不需要，则也会删除。
    params:
        rg=r"@RG\tID:{sample}\tSM:{sample}"
    log:
        "logs/bwa_mem/{sample}.log"
    threads: 8
    shell:
        "(bwa mem -R '{params.rg}' -t {threads} {input} | "
        "samtools view -Sb - > {output}) 2> {log}"
```
> **Note:**如果是直接生成的target file 则不会被删除。例如在terminal 端直接生成目标文件：`snakemake --cores 1 mapped_reads/A.bam`,那么`mapped_reads/A.bam`不会被删除，即使有`temp()`标记。

- Protected

同样的有些文件则非常的重要，我们不希望它多次进行重写。例如计算量非常大的文件，it is reasonable to protect the final file from **accidental deletion or modification.** 使用`protected`保护文件不被删除和修改。例如：

```python
rule samtools_sort:
    input:
        "mapped_reads/{sample}.bam"
    output:
        protected("sorted_reads/{sample}.bam") # 保护文件
    shell:
        "samtools sort -T sorted_reads/{wildcards.sample} "
        "-O bam {input} > {output}"
```
> **Note:**当文件需要修改或者删除时会报错。

## Benchmarking

使用benchmark指令可以获得job的运行时间(measure the wall clock time of a job). 例如：

```python
rule bwa_map:
    input:
        "data/genome.fa",
        lambda wildcards: config["samples"][wildcards.sample]
    output:
        temp("mapped_reads/{sample}.bam")
    params:
        rg="@RG\tID:{sample}\tSM:{sample}"
    log:
        "logs/bwa_mem/{sample}.log"
    benchmark: # 指令
        "benchmarks/{sample}.bwa.benchmark.txt"
    threads: 8
    shell:
        "(bwa mem -R '{params.rg}' -t {threads} {input} | "
        "samtools view -Sb - > {output}) 2> {log}"
```

> **Node:**benchmark 指令下的string如同output 下的string。是保存benchmark的结果。默认是与Snakefile统一文件夹下，所以使用`"benchmarks/{sample}.bwa.benchmark.txt"`而不是`"/benchmarks/{sample}.bwa.benchmark.txt"`，且其中wildcard 应与output中的wildcard相同。
> 如果想重复地运行某一job可以使用`repeat("benchmarks/{sample}.bwa.benchmark.txt", 3)`，Snakemake can be told to run the job three times. The repeated measurements occur as subsequent lines in the tab-delimited benchmark file.

## Modularization

为了创建更大的，更复杂的workflow，我们有必要将大的workflow分割成多个模块。使用`include`指令来引出另一个Snakefile。例如：
`include: "path/to/other.snakefile"`

另外，Snakemake 允许定义`sub-workflow`。一个`sub-workflow`指另一个完整的Snakemake workflow. 这一subworkflow 的输出可以作为当前workflow的输入。这种机制在你想扩展之前的workflow时是非常有帮助的，因为你无需修改之前的workflow。例如：

```python
subworkflow otherworkflow:
    workdir:
        "../path/to/otherworkflow"
    snakefile:
        "../path/to/otherworkflow/Snakefile"
    configfile:
        "path/to/custom_configfile.yaml"

rule a:
    input:
        otherworkflow("test.txt") # 一个subworkflow 的输出是现在的workflow的输入
    output: ...
    shell:  ...
```

在上例中subworkflow 命名为“otherworkflow”，也列出了工作路径和参数文件'custom_configfile.yaml'的路径。具体解释参考[module](https://snakemake.readthedocs.io/en/stable/snakefiles/modularization.html#snakefiles-sub-workflows)

当执行时，首先运行subworkflow尝试创建或者更新test.txt文件。然后再运行当前workflow。

## 自动执行和软件依赖

为了让workflow顺利的运行，有时有必要在不同的jobs 中使用不同的环境。可以在rule 中添加`conda`指令。例如：

```python
rule samtools_index:
  input:
      "sorted_reads/{sample}.bam"
  output:
      "sorted_reads/{sample}.bam.bai"
  conda: # conda 指令
      "envs/samtools.yaml" # conda 创建环境的包
  shell:
      "samtools index {input}"
```

with `envs/samtools.yaml` defined as

```python
channels:
  - bioconda
  - conda-forge
dependencies:
  - samtools =1.9
```

`snakemake --use-conda --cores 1`

当使用上面命令在terminal执行时，会自动创建所需要的环境，然后在执行这个job时就自动激活这个环境。最好设定package的最大或者最小的版本。这种情况下，可以使用不同的python版本也是可以的。

>**Note:** 所创建的环境是存放在Snakefile中的conda文件中。`/snakemake-tutorial/.snakemake/conda/bfc7b07df124da6806b0c7722e1e8520`

在这里涉及一个conda 如何创建env的知识点。具体的细节解释，可以参考[官方文件](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html?highlight=environment#creating-an-environment-with-commands) 。 现总结如下：

1. conda 可以通过文件创建虚拟环境，如下：

```yaml
name: flower_classifier
channels:
  - bioconda
  - conda-forge
dependencies:
  - bwa =0.7.*
  - samtools =1.9
  - pip:
    - tensorflow-gpu==1.10.0
    - mlflow
    - click==6.7
    - scikit-learn
    - pillow
```

其中有几个关键的参数：
- name: env的名称，如果是在snakemake中使用conda创建env时，则没有必要添加`name`，因为也不会创建此name的env。
- channels：channels一般指conda下载packages时的base，即conda是从哪个站点或者base下载所需要的packages，默认的是conda-forge，这个是免费的。
- dependencies：环境所需要的packages。可以指定具体的package的版本，同时也可以通过添加wildcard解释一定范围内的版本，例如`bwa =0.7.*` 则可以接受`0.7.0 ~ 0.7.9`。那么为什么后面会出现`pip`呢？这是因为有些package在conda中不存在，而在pip中存在，则需要使用pip下载。一般情况下，我们使用conda创建env，最好使用conda安装package。

有以下几个常用的命令：
- `conda create --name myenv` : 创建虚拟环境
- `conda create -n myenv python=3.6` : 创建有特定版本package的虚拟环境
- `conda create --no-default-packages -n myenv python`: 不安装预定版本的packages
- `conda env export > environment.yml` : 把当前的env下的安装所需要的packages放到yml文件下。
- `conda env create -f environment.yml`: 安装文件中的虚拟环境
- `conda env update --file environment.yml  --prune`: 跟新文件中的packages，例如版本更新。`The --prune option causes conda to remove any dependencies that are no longer required from the environment.`
- `conda env list`: 列出虚拟环境
- `conda remove --name myenv --all`: 删除掉env。

## Automatic reports

生成HTML report 方便查看，使用如下命令：
`snakemake --report report.html` 生成report.html，可以直接查看。



