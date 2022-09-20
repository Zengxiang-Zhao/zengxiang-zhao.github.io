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
3. `projectDir` can be used to as the UnixPath variable. It point to the folder where the nextflow script located in. And you can use to specify other files in the same folder like `'$folder_path/hello_world.txt'`
4. `println folder_path` is to print the varilabe. If you'd like to print some value for confirmation, then you can use `println` directly
5. If you want to see the value of `Channel`, then you'd have to use `subscribe` and `view`. 
6. If you want to use view to show the content of the Channel, then you use `()`. If you'd like to use some pattern, then use `{'some pattern :'+it}`
7. 
