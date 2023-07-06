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

One important thing for the flask is that you should use `python run.py` in the docker-compose.yml instead of 'flask run --port 5001'

Here's the `run.py`
```python
from flask import Flask
import pathlib
import sys,os

root_path = pathlib.Path(__file__).resolve().parent
home_path = root_path/'home'
sys.path.insert(0, str(home_path))
print(f'root_path is {root_path}')
from routes import blueprint

app = Flask(__name__)

app.register_blueprint(blueprint)

if __name__ == "__main__":
    app.run(host='0.0.0.0', debug=True, port=5002)
```

Here's the `Dockerfile` in the flask-app
```docker
FROM python:3.8

WORKDIR usr/src/app

ENV FLASK_ENV=development
ENV FLASK_APP=run.py

COPY requirements.txt .
RUN pip install --upgrade pip
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
```

Here's the `docker-compose.yml` file
```docker
version : '3'

services:
    flask_app:
        container_name: flask_app
        restart: always

        build: ./flask-app
        tty: true
        # volumes:
        ports:
            - "5002:5002"
        command:  python run.py # this one gives you the server's address and localhost addresss
        # command: flask run --port 5002 # this one is not working. It gives you the address of the localhost not the server's address

    flask_nginx:
        container_name: flask_nginx
        restart: always
        build: ./nginx
        ports:
            - "8085:8085"
        depends_on:
            - flask_app
```


