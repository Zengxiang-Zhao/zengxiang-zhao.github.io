---
layout: default
title: HGNC-api
parent: NGS
date:   2021-11-10 20:28:05 -0400
categories: NGS
---


---

## [使用HGNC-api来提取基因的信息](https://www.genenames.org/help/rest/)

Return the json format result 

```python
import json,requests
content = requests.get('http://rest.genenames.org/fetch/symbol/TP53',headers ={"Accept" :"application/json"})

res_json = json.loads(content.text)

```

