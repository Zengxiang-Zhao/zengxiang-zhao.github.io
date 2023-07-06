---
layout: default
title: Docker
date:   2021-09-11 20:28:05 -0400
categories: git
nav_order: 100

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

# Use Docker to contain Flask and Ngnix

Here's the tree structure of the project

```bash
.
├── docker-compose.yml
├── flask-app
│   ├── Dockerfile
│   ├── home
│   ├── __init__.py
│   ├── __pycache__
│   ├── requirements.txt
│   ├── run.py
│   ├── static
│   └── templates
└── nginx
    ├── default.conf
    └── Dockerfile
```


