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

# Manage Docker images

- list unused docker images : `docker images -f "dangling=true" -q`
- Remove unused docker images using one of the following methods:
  - `docker images -f "dangling=true" -q | xargs -d '\n' -I{} docker rmi {}`
  - `docker rmi  $(docker images --filter dangling=true -q )`
  - `docker image prune`
  

# Docker compose file use networks

Sometimes the subnet of docker network will conflict with the current server or AWS server, thus you need to change the subnet. There are two solutions:

1. You create one network, and add it in the docker-compose file
2. create a new network directly in the docker-compose file

### method 1:

create a new network using docker
`docker network create -d bridge --subnet=192.168.0.0/16 data-management`

then write the docker-compose.yml file as following:

```yaml
version : '3'

services:
    data_management_backend:
        container_name : data_management_backend
        restart: always

        build: ./backend
        tty: true
        ports:
            - "3103:3003" # thus you can split the production and test environment
        command: python run.py
        volumes:
            - /absolut-path/data-management/backend/temp:/absolut-path/data-management/backend/temp
        networks:
            - data-management

    data_management_frontend:
        container_name: data_management_frontend
        restart: always
        build: ./frontend
        ports:
            - "3104:3004"
        command: npm run dev
        depends_on:
            - data_management_backend
        networks:
            - data-management

networks:
    data-management:
        external:true

```

### method 2:

```yaml
version : '3'

services:
    data_management_backend:
        container_name : data_management_backend
        restart: always

        build: ./backend
        tty: true
        ports:
            - "3103:3003" # thus you can split the production and test environment
        command: python run.py
        volumes:
            - /absolut-path/data-management/backend/temp:/absolut-path/data-management/backend/temp
        networks:
            - data-management

    data_management_frontend:
        container_name: data_management_frontend
        restart: always
        build: ./frontend
        ports:
            - "3104:3004"
        command: npm run dev
        depends_on:
            - data_management_backend
        networks:
            - data-management

networks:
    data-management:
        ipam:
            config:
                - subnet: 172.177.0.0/16
```



# [docker error: http: invalid Host header ](https://stackoverflow.com/questions/77225539/docker-compose-error-internal-booting-buildkit-http-invalid-host-header)

This error come from the docker version `20.10.24`. We need to upgrade the docker version to the newest.

`sudo snap refresh docker --channel=latest/edge`

please remeber to change the `/var/run/docker.sock` file permission.

# use docker command without sudo

after you install docker successfully, then do the following steps:

1. sudo groupadd docker
2. sudo usermod -aG docker $USER
3. if you write `docker ps` and give you error like below:

   docker ps
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json": dial unix /var/run/docker.sock: connect: permission denied

5. then you need to change the right of `/var/run/docker.sock`
6. `ls -l /var/run/docker.sock`
`srw-rw---- 1 root root 0 Jan 29 19:00 /var/run/docker.sock`
7. `sudo chmod 666 /var/run/docker.sock`
8. `ls -l /var/run/docker.sock`         
`srw-rw-rw- 1 root root 0 Jan 29 19:00 /var/run/docker.sock`

You see that the `srw-rw----` changed to `srw-rw-rw-`

9. now the docker can run correctly.


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

# [How to use MongoDB docker container](https://earthly.dev/blog/mongodb-docker/)

# How to combine Flask, MongoDB and Docker

1. Create Flask app and using pymongo to access the mongodb data. Here you need to pay attention to the mongodb host address: `docker-data:/data/db`
2. Create a Dockerfile in the Flask app folder
3. Create a Dockerfile for Mongodb
4. Create a docker-compose.yml file to combine Flask app and Mongodb : persist the mongodb data by docker volume. mount the MongoDB data to the /data/db
5. Check the mongodb host ip: `docker inspect mongodb-docker-container-name` and get the `IPAddress` from the `Networks`. And use this ip as the MongoDB host address. If the `IPAdress` cannot work, then use the `GateWay` address. Or just don't use the host parameter in `MongoClient`.
6. `docker-compose up --build -d`



