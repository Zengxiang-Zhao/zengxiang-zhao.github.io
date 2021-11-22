---
layout: page
title: About
permalink: /about/
---

This is the base Jekyll theme. You can find out more info about customizing your Jekyll theme, as well as basic Jekyll usage documentation at [jekyllrb.com](https://jekyllrb.com/)

You can find the source code for Minima at GitHub:
[jekyll][jekyll-organization] /
[minima](https://github.com/jekyll/minima)

You can find the source code for Jekyll at GitHub:
[jekyll][jekyll-organization] /
[jekyll](https://github.com/jekyll/jekyll)


[jekyll-organization]: https://github.com/jekyll

``` python
   from sklearn import metrics
   def get_scores(y_preds,y):
       return {
            "Accuracy":metrics.accuracy_score(y_preds,y),
            'Precision':metrics.precision_score(y_preds,y),
            'Recall':metrics.recall_score(y_preds,y),
            'F1':metrics.f1_score(y_preds,y),
            'ROC_AUC':metrics.roc_auc_score(y_preds,y)
        }
```

<!-- {% highlight python linenos %}
from sklearn import metrics
   def get_scores(y_preds,y):
       return {
            "Accuracy":metrics.accuracy_score(y_preds,y),
            'Precision':metrics.precision_score(y_preds,y),
            'Recall':metrics.recall_score(y_preds,y),
            'F1':metrics.f1_score(y_preds,y),
            'ROC_AUC':metrics.roc_auc_score(y_preds,y)
        }
{% endhighlight %} -->


## try inline number codes

{% capture code%}
{% highlight python linenos %}

from sklearn import metrics
   def get_scores(y_preds,y):
       return {
            "Accuracy":metrics.accuracy_score(y_preds,y),
            'Precision':metrics.precision_score(y_preds,y),
            'Recall':metrics.recall_score(y_preds,y),
            'F1':metrics.f1_score(y_preds,y),
            'ROC_AUC':metrics.roc_auc_score(y_preds,y)
        }

{% endhighlight %}
{% endcapture %}
{% include fix_linenos.html code=code %}
