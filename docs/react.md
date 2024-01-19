---
layout: default
title: React
date:   2024-01-11 20:28:05 -0400
categories: React
nav_order: 12

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
## Subcomponent cannot show up content correctly

That's because there's no key in the subcomponent, you should provide the key in the subcomponent to make sure the content is correct. React use key 
to check whether this component should be rendered. If you use MongoDB, then the _id is a good choice to be the key.

## [Connect to database directly without hitting a button](https://dev.to/darkmavis1980/fetching-data-with-react-hooks-and-axios-114h)

Sometimes we'd like show some database content directly when we go into the web page not after hitting the button. we can use `useEffect` hook to do that. The logic is to use async function. Here you should also use `setLoading` state to let the front know when your data is ready.

```
useEffect(() => {
        const fetchData = async () =>{
        setLoading(true);
        try {
            const res = await axios.get('/path/get');
            // console.log('resonpose is :',res.data)
            setList(res.data);
        } catch (error) {
            console.error(error.message);
        }
        setLoading(false);
        }
        fetchData();
    }, []);
```

Besides that, you can customize this function into a state.

## development and test port share the same ones

Here's a situation I ecountered:

I used Vite to generate a react app,which will use Tailwind css and Bootstrap to decorate the elements. After I created the frontend and backend, I used docker to contain the app. And use docker-copose.yml file to launch the app. I found that if use the follwoing docker-compose.yml file

```yml
version : '3'

services:
    report_backend:
        container_name : report_backend
        restart: always

        build: ./backend
        tty: true
        ports:
            - "1111:1111"
        command: python run.py
        volumes:
            - /path/from:/path/to

    report_frontend:
        container_name: report_frontend
        restart: always
        build: ./frontend
        ports:
            - "2222:2222"
        command: npm run dev
        depends_on:
            - report_backend

    # report_mongodb:
    #     container_name: report_mongodb
    #     restart: always
    #     build: ./mongodb_data
    #     ports:
    #       - "3333:3333"
    #     volumes:
    #       - docker-data:/data/db

```

If I use `docker-compose up --build -d` to launch this docker compose file, then when I test the web locally, I need to change the ports to avoid the ports that have been used by the docker.

Here's a important note: because I have used the `MongoDB` data database in another app, I can use it directly instead of create a new one container for MongoDB database.

In order to test and launch docker without changing ports, I changed the docker-compose.yml file and vite configure file as below.

```yml
# docker-compose.yml file
version : '3'

services:
    report_backend:
        container_name : report_backend
        restart: always

        build: ./backend
        tty: true
        ports:
            - "1011:1111"
        command: python run.py
        volumes:
            - /path/from:/path/to

    report_frontend:
        container_name: report_frontend
        restart: always
        build: ./frontend
        ports:
            - "2022:2222"
        command: npm run dev
        depends_on:
            - report_backend

    # report_mongodb:
    #     container_name: report_mongodb
    #     restart: always
    #     build: ./mongodb_data
    #     ports:
    #       - "3333:3333"
    #     volumes:
    #       - docker-data:/data/db
```

``` javascript
# vite.config.js file
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
// const path = require('path');


// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  server: {
    host: true,
    port : 2222,
    proxy: {
        '/api': {
            target: 'ipAddress:1111',
            changeOrigin: true,
            secure: false,
            ws: true,
            rewrite: (path) => path.replace(/^\/api/, ""), # all the path from backend will add api,
        }
    }
  },
  server2: {
    host: true,
    port : 2022,
    proxy: {
        '/api': {
            target: 'ipAddress:1011',
            changeOrigin: true,
            secure: false,
            ws: true,
            rewrite: (path) => path.replace(/^\/api/, ""),
        }
    }
  },
})

```

Actually, the main idea is that, I created two servers in the vite config file. Server1 for the local test that use the ports 2222 for fronted, 1111 for backend.
Server2 that uses ports 2022 for frontend, 1011 for backend.

And in the docker-compose.yml file, I also changed the maping of machine_port:container_port from 2222:2222 to 2022:2222, Thus the container use its namespace 2222, but the machine use 2022. thus you can use 1pAddress:2022 to show the frontend.

the backend ports mapping is changed from 1111:1111 to 1011:1111. the container use 1111 in its namespace, while the machine use 1011. That's the reason why in the vite config file we set the server2

```
server2: {
    host: true,
    port : 2022,
    proxy: {
        '/api': {
            target: 'ipAddress:1011', # after mapping, this is the machine ports use
            changeOrigin: true,
            secure: false,
            ws: true,
            rewrite: (path) => path.replace(/^\/api/, ""),
        }
    }
  },
```











