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

file_ch = Channel.fromPath("/home/zhaozx/projects/nextflow/nf-training-public/nf-training/chr1_GL383518v1_alt.fa")

process test_python {

    input:
        path x from file_ch

    output:
        stdout result

    """
    #!//home/zhaozx/miniconda3/envs/nextflow/bin/python

    with open('$x', 'r') as f:
        list_line = f.readlines()

    print(list_line[0])
    """

}

result.subscribe {println it}

{% endhighlight %}
{% endcapture %}
{% include fix_linenos.html code=code %}
