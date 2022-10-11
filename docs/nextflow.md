---
layout: default
title : NextFlow
date:   2021-10-5 20:28:05 -0400
categories: NextFlow
nav_order: 102
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

## The arrangement of process

Recently I always got the error that said there's no such file that the command need to use. And I checked the output publishDir and found that the file existed in the folder. The process complains the error again and again.
![nextflow error](/assets/images/nextflow/nextflow-error.png)
![error reason](/assets/images/nextflow/error-reason.png)
From the above picture, You can see that there's noly one process failed. And it complains that some file didn't exist. We should figure out where this setence means the file not exists. And at last I know that the place means the process folder, not the `publishDir` folder. See the first column of the executor. The first column is the position of this process like [2a/0cbbf7]. `2a` is folder name in the work folder like below. `0cbbf7` is the subfolder. All the files this process needed are stored here.
![arrange work](/assets/images/nextflow/arrange-work.png)

```nextflow
process create_index {
    publishDir 'output'
    container "staphb/samtools:latest"

    input:
        path ref from ref_ch3

    output:
        file 'chr1_GL383518v1_alt.fa.fai' into fai_ch


    """
    samtools faidx $ref
    """

}
```

Now let's see the failed process `collectHsMetrics`. It needs "chr1_GL383518v1_alt.fa.fai". But it donesn't exist in the process folder (align.bam  chr1_GL383518v1_alt.fa  list.interval_list). So we need to add it through the `input` like below

```nextflow
process collectHsMetrics {
    publishDir 'output'
    container 'broadinstitute/picard:latest'

    input:
        // file bed from bed_ch
        file align_bam from align_ch2
        path ref from ref_ch4
        file interval from interval_ch
        file index from fai_ch
    output:
        file "output_hs_metrics.txt"

    """
    java -jar /usr/picard/picard.jar CollectHsMetrics -I $align_bam -O output_hs_metrics.txt -R $ref -BAIT_INTERVALS list.interval_list -TARGET_INTERVALS list.interval_list
    """

}
```

we added the index through the input `file index from fai_ch`. And you can generate the index in another process and just place it into a channel. So this process can get it throuh the channel.

Note: you should specify all the input files that you need to use in the command line. Because each process has a process folder, all the files that this process needed are saved in the process folder. 

## How to use the same file twice in two following process

Assign this file to different channels. And then you can use the file twice in the following channels. 

```nextflow
process p1 {
  output:
    file 'hello_world.txt' into ch_a
    file 'hello_world.txt' into ch_b
}

process p2 {
  input:
    file welcome from ch_a
}

process p3 {
  input:
    file welcome from ch_b
}
```

## How to know the command that you should use in the docker container

1. In the docker hub, search the image name and hit the tags. For example [this address use picard](https://hub.docker.com/layers/broadinstitute/picard/latest/images/sha256-1a7c4c708896d685057079995096c2dd4938f51892db9c6bcdc7dc6d5824ff4d?context=explore).
2. From the `IMAGE LAYERS`, you may find a varialbe named `WORKDIR`. In the picard, the WORKDIR is `WORKDIR /usr/picard`
3. In the terminal, try `sudo docker run broadinstitute/picard:latest java -jar /usr/picard/picard.jar FastqToSam` to test the command
4. If the above method can not determine the command. Then you need to use the docker interface to try and find the command that you can use
5. `sudo docker run -it container`. Check folders like `/usr/` and `/usr/bin` and `/bin`

## How to use docker container in nextflow

1. install `docker` on Ubuntu. Please refer [this Website](https://www.simplilearn.com/tutorials/docker-tutorial/how-to-install-docker-on-ubuntu)
2. create a new environment through `conda` : `conda create -n nextflow python=3.8`
3. activate the nextflow environment: `conda activate nextflow`
4. install nextflow through conda : `conda install -c bioconda nextflow `
5. create a `nextflow.config` file in the current project. About the `nextflow.config` file please refer  [Official Website](https://www.nextflow.io/docs/latest/config.html)
6. write docker setting in nextflow.config file. refer [this link](https://www.nextflow.io/docs/latest/config.html#scope-docker).If you'd like to use more different containers then you should write the nextflow.config like below. `fastq2bam` and `bam2vcf` are the process names that you defined in the nextflow file. Within the `{}`, define the container that you want to use. And you also need to make `docker {enabled=true}`. Please refer [this website](https://www.annasyme.com/docs/docker-nextflow.html)
```nextflow
process {
    withName:fastq2bam {
        container = 'python:3.8'
    }

    withName:bam2vcf {
        container = 'zlskidmore/fgbio:latest' 
    }
}

docker {
    enabled = true
}
```
8. If you run the nextflow file now. You'll get the error. Because you can't run the docker directly. If you'd like to run docker in the terminal. Then you should use `sudo`, like `sudo docker run container`. In this case, we don't want to use `sudo` to run the docker. So we need to do the following stuff. Please refer [this website](https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket)
9. Create the docker group: `sudo groupadd docker`
10. Add your user to the docker group : `sudo usermod -aG docker ${USER}`. Here `${USER}` refer to the current user. Try `echo ${USER}` to show the current user name
11. You would need to loog out and log back in so that your group membership is re-evaluated or type the following command: `su -s ${USER}`
12. Verify that you can run docker commands without sudo. `docker run hello-world` . If this command run successfully, then you can use the docker container in the nextflow now.

## How to use python script in nextflow process

{% capture code%}
{% highlight nextflow linenos %}

Channel.fromPath("/home/user/projects/nextflow/nf-training-public/nf-training/chr1_GL383518v1_alt.fa").into{file_ch ; bar; fool}
file_path = Channel.fromPath("/home/user/projects/nextflow/nf-training-public/nf-training/chr1_GL383518v1_alt.fa")
folder_path = projectDir

println folder_path
bar.subscribe {println it}
fool.view{"fool emit: " +it}
// fool.view()

process test_python {
    publishDir 'out'
    input:
        path x from file_ch
        val path_file from file_path

    output:
        stdout result
        file 'output.txt'

    """
    #!/home/user/miniconda3/envs/recon/bin/python
    from pathlib import Path

    print('$path_file')
    print('$folder_path')
    path_x = Path('$path_file')
    print(path_x.parent)

    with open('$x', 'r') as f:
        list_line = f.readlines()

    print(list_line[0])

    with open('output.txt', 'w') as f:
        f.write(list_line[0])
    """

}

result.subscribe {println it}

{% endhighlight %}
{% endcapture %}
{% include fix_linenos.html code=code %}

1. if you want to assign the channel to multiple variables in nextflow, you can use [`into` operator](https://www.nextflow.io/docs/latest/operator.html#operator-into)
2. If you just want to assign one variable just use equal sign `=`
3. `projectDir` can be used to as the UnixPath variable. It point to the folder where the nextflow script located in. And you can use it to specify other files in the same folder like `'$folder_path/hello_world.txt'`
4. `println folder_path` is to print the varilabe. If you'd like to print some value for confirmation, then you can use `println` directly
5. If you want to see the value of `Channel`, then you'd have to use `subscribe` and `view`. 
6. If you want to use view to show the content of the Channel, then you use `()`. If you'd like to use some pattern, then use `{'some pattern :'+it}`
7. define the nextflow process using keyword `process` like `process process_name { content }`
8. `publishDir 'out'` : here nextflow will create a folder named "out" if there's no such folder int the `projectDir`. and all the output files that you define in the `output` will be saved in the "out" folder. 
9. define [input variables](https://www.nextflow.io/docs/latest/process.html#inputs) through keyword `input`. There are 7 qualifiers in input: `val, env, file, path, stdin, tuple, each`. More detailed information please check [website](https://www.nextflow.io/docs/latest/process.html#inputs). 
![input qualifier](/assets/images/nextflow/input_qualifier.png)
10. `path x from file_ch`: here `file_ch` is a channel. And the qualifier of the x is `path`, thus it will contain all the UnixPath format information. Here if we replace the qualifier with `file`, then it will only contain the file name. Acually it will only contain the stem of the filename like 'Hell_world' from 'Hello_world.txt'.
11. define output variables through keyword `output`. the qualifiers of output as shown below. Here `file 'output.txt'` must be used in the python script. And the name must be kept same. Otherwise, nextflow will confuse and show errors.
![output qualifier](/assets/images/nextflow/output_qualifier.png)
11. If you'd like to show the results on the terminal, then you need to use `stdout`. Actually in `stdout result`, the `result` acts as a channel that all the print stuff in the python script or other stuff show up in the terminal will be sent to the result as a queue. Thus we can use `view` or `subscribe` to show the content of the channel.
12. In the script, we need to specify the python environment through `#!`
13. Actually the variables in the current process or the variables in the nextflow head part can be accessed through `'$variable'`. Please remember that we use `''` or `""` to enclose the variable.

