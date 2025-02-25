---
layout: default
title: Nginx
date:   2021-09-11 20:28:05 -0400
categories: Linux
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

# [build flask as backend, react as frontend app, nginx as proxy server](https://blog.miguelgrinberg.com/post/how-to-dockerize-a-react-flask-project)

1. Flask framework as the backend. When you launch the backend into production mode. Use the `gunicorn` to lanch the production mode.
2. In the backend folder, create `Dockerfile.api` to create a docker image: `docker build -f Dockerfile.api -t react-flask-app-api .`
3. React as the frontend. When you launch the frontend into production mode. If the React uses vite.js, then use `npm run build` to generate the `dist` folder which is the static folder and will be used in the nginx static folder `/usr/share/nginx/html`
4. In the frontend folder, create `Dockerfile.client` to create a docker image: `docker build -f Dockerfile.client -t react-flask-app-client .` In this `Dockerfile.client`, you need to specify the nginx configuration files.
5. Create `docker-compose.yml` file to connect these two docker image containers.

Here are the three important docker configure files

Dockerfile.api
```
FROM python:3.8
WORKDIR /app

COPY api/requirements.txt api/api.py api/.flaskenv ./
RUN pip install -r ./requirements.txt
ENV FLASK_ENV production

EXPOSE 5000
CMD ["gunicorn", "-b", ":5003", "api:app"] # use gunicorn to lauch production mode

```

Dockerfile.client
```
FROM node:16-alpine as build-step
WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH
COPY package.json yarn.lock ./
COPY ./src ./src
COPY ./public ./public
RUN yarn install
RUN yarn build

# Build step #2: build an nginx container
FROM nginx:stable-alpine
WORKDIR /home/log
COPY --from=build-step /app/build /usr/share/nginx/html
COPY deployment/nginx.default.conf /etc/nginx/conf.d/default.conf
RUN touch error.log # to save teh error iformation of nginx, so that you can debug easily
RUN touch access.log
```
nginx.default.conf

```

server {
    listen       80; # nginx listen 80 as default.
    server_name  localhost;

    root   /usr/share/nginx/html; # where to find the static files by nginx
    index index.html; # actually this line can be ignored. The nginx will use index.html as default.
    error_page   500 502 503 504  /50x.html;
    error_log /home/log/error.log; # redirect the error message to easily debug
    access_log /home/log/access.log;

    location / {
        try_files $uri $uri/ =404; # it will try the different uri
        add_header Cache-Control "no-cache";
    }

    location /static {
        expires 1y;
        add_header Cache-Control "public";
    }

    location /api {
        proxy_ssl_server_name on; # this line is important. Without this line, the server cannot connect the proxy_pass
        proxy_pass http://api:5003; # the reason why here use api, because api is the container name in the docker-comopose.yml file. The gunicore use port 5003 in the Dockerfile.api
    }
}

```

docker-compose.yml

```
services:
  api: # the reason why use http://api:5003
    build:
      context: .
      dockerfile: Dockerfile.api
    image: react-flask-app-api
  client:
    build:
      context: .
      dockerfile: Dockerfile.client
    image: react-flask-app-client
    ports:
      - "3000:80"
```

After complete the above docker configuration files, you need to build the frontend and backend images. And then test the app using the following commmand to connect the containers: `docker-compose -f docker-compose.yml up`.
If the docker-compose configuration file is `docker-compose.yml`, then you can ignore the `-f` parameter like `docker-compose up`.

Then you can use the browser to check the result. If something wrong, you can check the `error.log` file.

1. Access the client container : `docker exec -it react-flask-app_client_1 sh`
2. Check the `error.log` or `access.log` to find out the reason.
3. Most of the time, the error is that the api server cannot be found. Then you can try using `curl -i http://aip:5003/testUri` to test whether the proxy_pass is correct in the nginx configuration file..


# [How to solve the proxy pass issue](https://stackoverflow.com/questions/38375588/nginx-reverse-proxy-to-heroku-fails-ssl-handshake)

When you set the location with `proxy_pass`, sometimes you cannot redirect to the specific address. You can try to add the following directive.

```
location /proxy {
 proxy_pass "https://nginx.org/";
 proxy_ssl_server_name on; # change to on
 }

```

If you use the Docker container, you may also need to use the command to make the change works. like `sudo nginx -s reload`

# [Change the ip address to string](https://www.freecodecamp.org/news/the-nginx-handbook/#heading-introduction-to-nginxs-configuration-files)

In the nginx configuration file, if you wirte the `server_name` directive using the string. then you should also update the `/etc/hosts` file to enable the string work.

```
192.168.20.20   library.test
192.168.20.20   librarian.library.test
```

```
curl http://library.test

# your local library!

curl http://librarian.library.test

# welcome dear librarian!
```

The nginx use port 80 as default, so you can ignore the port if the port is 80. If this port has been used by other app, then you should specify the port number

```
curl http://library.test:8080

# your local library!

curl http://librarian.library.test:8080

# welcome dear librarian!
```

# Modify the address

When the address starts with `/images/`, it refers to `/home/projects/website/data/images/` actually. The `/images/` will be added at the end of the root directive.

```
location /images/ {
                root /home/projects/website/data;
        }
```

# Change the configure file and encorter error message

If you encouter the similar error message like : 'no some files'. You can try the following command.

```
sudo systemctl start nginx
sudo systemctl status nginx.service
```


# [How to uninstall Nginx](https://stackoverflow.com/questions/14801958/uninstalling-nginx)

Check the Status and version
```
sudo service nginx status
nginx -v
```
First Stop nginx service
```
sudo service nginx stop
```
Removes all but config files.
```
sudo apt-get remove nginx nginx-common
```
Removes everything.
```
sudo apt-get autoremove
```
Remove dependencies used by nginx which are no longer required.
```
sudo service nginx status
````

# [Install Nginx in Ubuntu](https://nginx.org/en/linux_packages.html)

After installing the Nginx following the guidline, you need to start the nginx server using the following [command line](https://stackoverflow.com/questions/60850421/is-there-a-way-to-resolve-an-issue-with-nginx-status-active-inactive-dead-pro).

```
sudo systemctl start nginx
```
