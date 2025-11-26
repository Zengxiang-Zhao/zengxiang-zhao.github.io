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

# Nginx act as loadidng balancer to distribute the request to multiple backend servers

If your app has tons of queries, you can use Nginx acting as loading balancer to multiple backend servers.
v
You can create the image using the following command line. After you get the images, you can use docker compose to arrange the containers.

```yml
# version : '3.8'

services:
  mongodb:
    image: mongo:latest
    container_name: container_wrs_mongodb_production
    ports:
      - "27018:27017"
    volumes:
      - pathSaveDB:/data/db
    networks:
        - report_network_production

  api:
    image: image_wrs_backend_production:v1.1
    # restart: always
    container_name: container_wrs_api_production_1
    env_file:
      - ./backend/.env
    environment:
      MONGODB_URI: "mongodb://mongodb:27017"
    volumes:
      - pathToLocal:pathToContainer

    networks:
      - report_network_production
    depends_on:
      - mongodb



  api2:
    image: image_wrs_backend_production:v1.1
    # restart: always
    container_name: container_wrs_api_production_2
    env_file:
      - ./backend/.env
    environment:
      MONGODB_URI: "mongodb://mongodb:27017" # Here connect to same network mongodb server, use the port 27017 
    volumes:
       - pathToLocal:pathToContainer

    networks:
      - report_network_production

    depends_on:
      - mongodb

  client:
    image: image_wrs_frontend_production:v1.0
    # restart: always
    container_name: container_wrs_client_production
    volumes:
      - ./nginx/default.conf.production:/etc/nginx/conf.d/default.conf
    networks:
      - report_network_production
    ports:
      - "80:80"
    depends_on:
      - api
      - api2
     

networks:
  report_network_production: # Define your custom network
    driver: bridge # You can specify other drivers like 'overlay' for swarm mode

```
Becuase I use nginx in the client container, I can change the nginx.conf using the volume property. Here's the `default.conf.production`

```configure
## nginx/default.conf
upstream backends {
   # no load balancing method is specified for Round Robin
   server api;
   server api2;
}

server {
  # Nginx listens on port 80 by default. You can change this if needed.
  listen 80;

  # Specifies your domain. Use "localhost" for local development or your domain name for production.
  # server_name localhost;

  # The root directory that contains the `dist` folder generated after building your app.
  root /usr/share/nginx/html;
  index index.html;

  # Serve all routes and pages
  # Use the base name to serve all pages. In this case, the base name is "/".
  location / {
    try_files $uri /index.html =404;
  }

  location /static {
        expires 1y;
        add_header Cache-Control "public";
    }

    location /api {
        proxy_pass http://backends; # Production
    }

}


```

How to test the loading balancer uses different backend servers?

In your python routes add a route '/test'

```python
import socket
@bp.route('/test', methods=['POST','GET'])
def test():
    hostname = socket.gethostname()
    IPAddr = socket.gethostbyname(hostname)

    print("Your Computer Name is:", hostname)
    print("Your Computer IP Address is:", IPAddr)
    return f"Hello World: hostname is: {hostname}; Ip: {IPAddr}"
```

When you hit the button, you will see the different hostname and Ip address.

# docker build a new image with a tag using dockerfile

`docker build -t imageName:tag -f pathToDockerfile .`

e.g.: `docker build -t image_wrs_frontend_development:v1.0 -f ./Dockerfile.client.development .`

Please note there's `.` at the end of command line.

# docker compose file that use mongodb container and connection with other containers

You'd like to create an website that use mongodb as the backend database. While you also would like to have two different kinds of `production` and `development` mongodb databases.

When you under development, you can test it use the development database. While you under production to use the production database.

**How to create a mondodb container within docker-compose.yml file**

`docker-compose.yml` file content
```yml
version: '3.8'

services:
  mongodb:
    image: mongo:latest
    container_name: mongodb_container
    ports:
      - "27019:27017"
    volumes:
      - /home/zhaozx/projects/MongoDB_data/WRS-development/db:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: user
      MONGO_INITDB_ROOT_PASSWORD: password

  python_app:
    build: ./app
    container_name: python_app_container
    ports:
      - "5000:5000" # Example port for a web app
    depends_on:
      - mongodb
    environment:
      MONGO_URI: mongodb://user:password@mongodb:27017/mydatabase?authSource=admin
```

Under the service `mongodb`
- `ports`: you can change the ports number as  "27019:27017"
- `volumes`: you can specify different location for the mongodb data path. Here's the place actually to differ the production and development database
- `enviroment`: you can specify the mongodb username and passowrd to login the database
- **You need to add `depends_on` to make sure the mongodb is active before the backend connect to the database. If you have more containers, you need to use depends one to arrange the relationship between them.**
  
Please note that: you need to service name instead of container name to connect to the mongodb database as the following paradigm:
`mongodb://user:password@serviceName:27017/mydatabase?authSource=admin`

Here you need to pay attation to the port number. In the mongodb container, you change the output port that the mongodb container use is "27019". While you need to use "27017" in the `MONGO_URI` environment variable. When other container connects to the mongodb container, it needs to use "27017" port to connect to it.

The whole project is as shwon below to do the test
1. `mkdir pythonApp && cd pythonApp` : create a project folder named `pythonApp` and enter the folder
2. `touch docker-compose.yml` : create a `docker-compose.yml` file
3. `mkdir app && touch Dockerfile  main.py requirements.txt`: create a folder named `app` and create two files: `Dockerfile`, `main.py` and 'requirements.txt'

The content `docker-compose.yml` file is shown as above.

content of `Dockerfile` in the app folder:
```dockerfile
FROM python:3.8-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "main.py"]
```

content of `main.py` in the app folder:
```python
from pymongo import MongoClient
import os
import time

# Wait for MongoDB to be ready
time.sleep(10) # Adjust as needed

mongo_uri = os.getenv('MONGO_URI', 'mongodb://localhost:27017/')

try:
    client = MongoClient(mongo_uri)
    db = client.mydatabase
    collection = db.mycollection
    collection.insert_one({"message": "Hello from Python!"})
    print("Successfully connected to MongoDB and inserted a document.")
    for doc in collection.find():
        print(doc)
    client.close()
except Exception as e:
    print(f"Error connecting to MongoDB: {e}")
```

Content of `requirements.txt` file:
```txt
pymongo
```

1. create container using docker-comopose.yml: `docker-compose -f docker-compose.yml up --build  -d`. Here `--build` is to build image before create containers. If you are just to do the test, you can remove this option.
2. check the containers: `docker ps`
3. Connect to the mongodb container to do the test: `docker exec -it mongodb_container bash`
4. Connect to the app container to do the test: `docker exec -it python_app_container /bin/sh  `

# Remove dangling images in the local server

```bash
docker images -f "dangling=true" -q | xargs -d '\n' -I{} docker rmi {}
```

# [Transfer docker images to another server](https://stackoverflow.com/questions/23935141/how-to-copy-docker-images-from-one-host-to-another-without-using-a-repository)

1. Save image locally: 'docker save -o image_test.tar image_test:latest'
2. transfer to another machine: `scp image_test.tar username@server_ip:path_to_save`
3. restore the image: `docker load -i <path to image tar file>`

# Command lines used in docker: Based on Docker Deep Dive by Nigel Poulton

You can think of the docker image as a class or machine template

- list docker images: `docker images` or `docker image ls`
- run container using image: `docker container run -it ubuntu:1atest /bin/bash`.
  - The `-it` flags tell the daemon to make the container interactive and to attach our current terminal to the shell of the container
  - Next, the command tells Docker that we want the container to be based on the ubuntu:latest image
  - Finally, we tell Docker which process we want to run inside of the container
- list all running containers: `docker container ls`
- attach your shell to running containers: `docker container exec -it container_name/container_id bash` based on the command : `docker container  exec -options <container-name or container-id> <command>`
- stop container: `docker container stop <container-name or container-id>`
- remove container: `docker container rm <container-name or container-id>`
- 

# Full stack and dockernize it: Frontend(react) + Backend(Flask) + Database(MongoDB)

1. Create a docker volume to mount the local path: Refer to this [post](/docs/mongoDB.md#how-to-create-a-volume-and-launch-a-mongodb-container-using-that-volume)
2. Create the frontend and backend container: Refer to the below [post](/docs/docker.md#dockernize-the-frontendreact--backendflask)
3. Make sure the step 1 and step 2 use the same network.
4. At the backend part, use the step 1 container name as the hostname as shown below
    ```python
    client = MongoClient(host='container-name-mongodb',port=27017) 
    ```
    Here, the mongodb use 27017 as the port, and you should not modify it. And the host uses the mongodb container name instead of the hostname you specified in the step1.
6. 

# [Dockernize the frontend(react) + backend(Flask)](https://blog.miguelgrinberg.com/post/how-to-dockerize-a-react-flask-project)

## Backend
For the backend Flask api part, you need to create a `requirements.txt` file that should conatin `gunicorn` package as shown below

```requirement
Flask==3.0.3
Werkzeug==3.0.6
gunicorn==20.1.0
```
And create a `Dockerfile` for the backend part, and following the content below:

```Dockerfile
FROM python:3.8-slim # the slim version has small space
WORKDIR /app

COPY api/requirements.txt api/api.py api/.env ./ # copy the essential files to the work dir
RUN pip install -r ./requirements.txt # install necessary packages
ENV FLASK_ENV production # no use here, as we are going to use gunicore to launch the Flask app

EXPOSE 5000 # the port number 
CMD ["gunicorn", "-b", ":5000", "api:app"] # here the `api:app` : api is the python file name that contains the function name app (app = Flask(__name__)) 
```

## Frontend

After you create your frontend part, create a `Dockerfile` for frontend as an example below. The frontend `Dockerfile` and `default.conf` comes from [Medium post](https://dev-mus.medium.com/how-to-deploy-a-vite-react-app-using-nginx-server-d7190a29d8cd)

```Dockerfile

## Dockerfile
################################
## BUILD ENVIRONMENT ###########
################################

# Use the official Node.js Alpine image (adjust based on your project's requirements)
# You can find the appropriate image on Docker Hub: https://hub.docker.com/_/node
# In this example, we're using node:20-alpine3.20
# run in termilnal commande line "node --version to get the version of your app"
FROM node:20-alpine3.20 As build

# Set the working directory inside the container
WORKDIR /app

# Copy package.json and package-lock.json into the container
COPY package*.json package-lock.json ./

# Copy the project files into the working directory
COPY ./ ./

# Install dependencies using npm
RUN npm ci

# Build the React app for production
RUN npm run build

################################
#### PRODUCTION ENVIRONMENT ####
################################

# Use the official NGINX image for production
FROM nginx:stable-alpine as production

# copy nginx configuration in side conf.d folder
COPY --from=build /app/nginx /etc/nginx/conf.d

# Copy the build output from the dist folder into the Nginx html directory
COPY --from=build /app/dist /usr/share/nginx/html

# Expose port 80 to allow access to the app
EXPOSE 80

# Run Nginx in the foreground
ENTRYPOINT ["nginx", "-g", "daemon off;"] 
```
This Dockerfile uses a feature of Docker called [multi-stage builds](https://docs.docker.com/build/building/multi-stage/) to decrease the production image size.
In the above `Dockerfile`, there's a folder named `/app/nginx` which contains the `nginx` configuration file `default.conf` wich will be used to implement the static website pages and connect the backend api. You can follow the following as an example.

```nginx
## nginx/default.conf
server {
  # Nginx listens on port 80 by default. You can change this if needed.
  listen 80;

  # Specifies your domain. Use "localhost" for local development or your domain name for production.
  # server_name localhost;

  # The root directory that contains the `dist` folder generated after building your app.
  root /usr/share/nginx/html;
  index index.html;

  # Serve all routes and pages
  # Use the base name to serve all pages. In this case, the base name is "/".
  location / {
    try_files $uri /index.html =404;
  }

  location /static {
        expires 1y;
        add_header Cache-Control "public";
    }

    # the reason why we use http://api:5000, beacause we are going to create a docker container for the backend and name it as `api`. And this container must share the frontend container with the same network.
    location /api {
        # rewrite ^/api/(.*)$ /$1 break;  # Removes the /api/ prefix
        proxy_pass http://api:5000;
    }

  # Example: If your base name is "/example", the location block will look like this:
  # location /example {
  #   rewrite ^/example(/.*) $1 break;
  #   try_files $uri /index.html =404;
  # }
}

```

## Create docker images and containers

Create docker image
```bash
docker build -f Dockerfile.client -t image_name_client .
docker build -f Dockerfile.api -t image_name_api .
```

Create docker container
```bash
docker network create network_name 
docker container run --name client --rm -p 80:80 --network network_name image_name_client
docker container run --name api --rm --network network_name image_name_api # here the container name is `api` that is used in default.conf
```

Because the container client and api share the same network, they can communicate by using the other container name.





# Optimizing Image Sizes

The following word comes from the book `"Kubernetes: Up and Running, 2nd
edition, by Brendan Burns, Joe Beda, and Kelsey Hightower (O’Reilly). Copyright
2019 Brendan Burns, Joe Beda, and Kelsey Hightower, 978-1-492-04653-0.”`

There are two pitfalls about the images

- Create large image size that contains large files
  ```bash
  .
  └── layer A: contains a large file named 'BigFile'
   └── layer B: removes 'BigFile'
   └── layer C: builds on B by adding a static binary
  ```
  You might think that BigFile is no longer present in this image. After all, when you
run the image, it is no longer accessible. But in fact it is still present in layer A, which
means that whenever you push or pull the image, BigFile is still transmitted through
the network, even if you can no longer access it.

- Image caching and building
  ```bash
  .
  └── layer A: contains a base OS
   └── layer B: adds source code server.js
   └── layer C: installs the 'node' package

  versus:
  
  .
  └── layer A: contains a base OS
   └── layer B: installs the 'node' package
   └── layer C: adds source code server.js
  
  ```
  It seems obvious that both of these images will behave identically, and indeed the first
time they are pulled they do. However, consider what happens when server.js changes.
In one case, it is only the change that needs to be pulled or pushed, but in the other
case, both server.js and the layer providing the node package need to be pulled and
pushed, since the node layer is dependent on the server.js layer. In general, you want
to order your layers from least likely to change to most likely to change in order to
optimize the image size for pushing and pulling. This is why, in Example 2-4, we copy
the package*.json files and install dependencies before copying the rest of the pro‐
gram files. A developer is going to update and change the program files much more
often than the dependencies.

# Create a container from image

```bash
docker run -d --name <container-name> --publish 3000:3000 <image-name or image-id>
```

This command starts the kuard container and maps ports 3000 on your local
machine to 3000 in the container. The --publish option can be shortened to -p. This
forwarding is necessary because each container gets its own IP address, so listening
on localhost inside the container doesn’t cause you to listen on your machine.
Without the port forwarding, connections will be inaccessible to your machine. The
-d option specifies that this should run in the background (daemon), while --name
kuard gives the container a friendly name.

# Interact with docker container

- Option 1: interact from image directly
  ```bash
  docker run --rm -it --entrypoint bash <container-name or container-id>
  ```
  Here `--rm` is to delete the container and associated resource after the usage of container. `-it` is to interface with the container. '--entrypoint bash' it to use bash as the entry command line instead of the command line that you set in the Dockerfile.
- Option 2: interact from container
  ```bash
  docker exec -it <container-name or container-id> bash
  ```
  Here you have create a container based on one image. And then use `exec` and `-it` to interact with the container

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


Sometimes you have create a mongodb docker already and you need to use it in other projects. Now you need to use the networks to connect your project with the mongodb docker.

1. check the networks that the mongodb docker is using : `docker inspect mongodb_docker_name` get the `Networks` keyword in the output and extract the networks name, e.g. web-net
2. in your project docker-compose.yml file, you need to add networks in the each container as shown below.
```yaml
version : '3'

services:
    myWeb_backend:
        container_name : myWeb_backend
        restart: always
        build: ./backend
        networks:
            - web-net
        tty: true
        ports:
            - "3103:3003"
        command: python run.py 

    myWeb_frontend:
        container_name: myWeb_frontend
        restart: always
        build: ./frontend
        networks:
            - web-net
        ports:
            - "3104:3104"
        command: npm run docker
        depends_on:
            - myWeb_backend

networks:
    web-net:
        external: true
```
3. Here we use flask as the backend and react as the fronted and add the external networks. you must add the following line to enable the external networks usable.
   ```yaml
      networks:
        web-net:
            external: true 
   ```
5.  At the backend, we expose our flask port `3103:3003`. The docker flask will use 3003 as the port, but when it expose to the public, it use 3103 as the port. At the fronted, we use 3104:3104 as the ports. So in the docker, it use 3104, and it also use 3104 to the public. But when you config vite, you need to pay attentiion to the server the backend is using right now, actually it's 3103 right now instead of 3003.
6. vite.config.js file as shown below
```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// for bootstrap
import * as path from 'path';

// https://vitejs.dev/config/
export default defineConfig({

  // add these you can use material packages
  optimizeDeps: {
    include: ['@mui/material/Tooltip', '@emotion/styled'],
  },

  plugins: [react()],
  server: {
    host: true,
    // port : 3004,// local
    port: 3104, //docker
    proxy: {
        '/api': {
            // target: 'http://server_ip:3003',//loacl
            target: 'http://server_ip:3103', //docker
            changeOrigin: true,
            secure: false,
            ws: true,
            rewrite: (path) => path.replace(/^\/api/, ""),
        }
    }
  },

})

```
5. uncomment the docker line to use docker-compose command or uncomment the local line to use it locally.
6. 

