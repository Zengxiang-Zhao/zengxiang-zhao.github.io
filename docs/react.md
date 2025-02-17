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

## Resources

1. [Robin's Blog](https://www.robinwieruch.de/blog/)

## [axios sent two times request in backend in Effects](https://react.dev/reference/react/StrictMode)

If you use <StrictMode> in your app, then the axios will trig two times request at the backend. When you need to update the database, it will cause error. Cause it will render two times. If you need to check the function about updating the database, then disable the strictMode in your module.

##  Implement automatic login after a period of inactivity in a React app

To implement automatic login after a period of inactivity in a React app, you can use a combination of local storage or cookies, a timer, and an authentication mechanism. Here's a step-by-step guide:

1. Authentication Setup:
Authentication Mechanism: Choose an authentication method like JWT (JSON Web Tokens), OAuth, or a custom backend solution.
Login/Logout: Implement login and logout functionality. Store the authentication token (e.g., JWT) in local storage or a secure cookie upon successful login.
3. Implementing the Inactivity Timer

```javascript
import React, { useEffect, useState } from 'react';

function App() {
  const [lastActivity, setLastActivity] = useState(Date.now());

  useEffect(() => {
    const handleActivity = () => {
      setLastActivity(Date.now());
    };

    window.addEventListener('mousemove', handleActivity);
    window.addEventListener('keydown', handleActivity);

    return () => {
      window.removeEventListener('mousemove', handleActivity);
      window.removeEventListener('keydown', handleActivity);
    };
  }, []);

  useEffect(() => {
    const inactivityTimeout = setTimeout(() => {
      // Perform logout or redirect to login
    }, 300000); // 5 minutes in milliseconds

    return () => clearTimeout(inactivityTimeout);
  }, [lastActivity]);

  // ... rest of your component logic
}

```

3. Handling Auto-Login
Check Token: On app load, check if a valid authentication token exists in local storage or cookie.
Automatic Login: If a valid token is found, automatically log the user in. You may want to display a notification or a "Welcome back" message.
4. Security Considerations
Secure Storage: Ensure that you store the authentication token securely. Use HttpOnly and Secure flags for cookies.
Token Expiration: Set an appropriate expiration time for the authentication token.
Session Timeout: Implement a session timeout on the backend to invalidate tokens after a certain period of inactivity.

```javascript

import React, { useEffect } from 'react';
import jwtDecode from 'jwt-decode';

function App() {
  useEffect(() => {
    const token = localStorage.getItem('authToken');

    if (token) {
      try {
        const decodedToken = jwtDecode(token);
        if (decodedToken.exp * 1000 > Date.now()) {
          // User is logged in, perform necessary actions
        } else {
          // Token has expired, redirect to login
        }
      } catch (error) {
        // Invalid token, redirect to login
      }
    }
  }, []);

  // ... rest of your component logic
}
```

Important Considerations
User Experience: Clearly communicate to the user that they will be automatically logged in after a period of inactivity.
Security: Prioritize security when implementing automatic login functionality. Consider using HTTPS and implementing appropriate security measures.

## How to download file by hit download button

When you'd like to hit a button and then show up a screen to download a file. Here's a example

Backend
```python
import tmpfile
from flask import send_file,make_response

@bp.route('/download/<reportID>', methods=['POST'])
def download(reportID):
    pathFolder= tempfile.mkdtemp(prefix='generateReport_' ,dir=pathTemp)
    fd, pathFile = tempfile.mkstemp(prefix=f'{caseNo}_',suffix='.docx',dir=pathFolder)

    


    pathFolder = pathlib.Path(pathFolder) # TODO: fix
    # list_files = list(pathFolder.glob('*.xlsx'))

    # stream = BytesIO()
    # with ZipFile(stream, 'w') as zf:
    #     for file in list_files:
    #         zf.write(file, file.name)
    # stream.seek(0)

    # TODO: save the data to MongoDB database

    response =  make_response( send_file(
        # stream,
        pathFile,
        as_attachment=True,
        # download_name=f'archive_{utils.get_timeStamp()}.zip'
        )
    )
    response.headers['pathFolder'] =  str(pathFolder.name)
    response.headers['filename'] = f'{caseNo}_{utils.get_timeStampForName()}.docx'

    return response
```


Frontend
```react
const handleClickGenerateReport = () =>{
        axios(
            {
                url: `/api/report/download/${reportID}`, //your url
                method: 'POST',
                data: formData, // this can be deleted

                responseType: 'blob', // important
            })
        .then((response) => {
            console.log('response data is :', response)
            const pathFolder = response.headers.pathFolder;
            const filename = response.headers.filename;
            const href = URL.createObjectURL(response.data);
        
            // create "a" HTML element with href to file & click
            const link = document.createElement('a');
            link.href = href;
            link.setAttribute('download',filename); //or any other extension
            // link.setAttribute('download', 'archieve.zip'); //or any other extension
            document.body.appendChild(link);
            link.click();
        
            // clean up "a" element & remove ObjectURL
            document.body.removeChild(link);
            URL.revokeObjectURL(href);

            //POST /report/generateReport/667450b95d141978d7bcc016

            axios.delete('/api/report/deleteTemp', {
                params: { folderName:pathFolder },
                })
                .then(response => {
                console.log(response.data);
                })
                .catch(error => {
                if (error.response) {
                    console.log(error.response.data);
                    console.log(error.response.status);
                    console.log(error.response.headers);
                } else if (error.request) {
                    console.log(error.request);
                } else {
                    console.log('Error', error.message);
                }
                console.log(error.config);
                });


        })
        .catch(err => console.warn('Error: ',err));


    }

```


## [How to create a custome context](https://react.dev/reference/react/createContext)

```js
import * as React from "react";
import { useLocation, Navigate } from "react-router-dom";
import axios from "axios";

const authContext = React.createContext(); 

function useProviderAuth() {
  //Do something and get some information here and return the information in a dictionary style.

  return {
    authed,
    user,
    login,
    logout,
  };
}


// make the context be available to the children, thus in the main.js we could just embrace the component with this Authprovider <AuthProvider> </ComponentName> </AuthProvider>

export function AuthProvider({ children }) {
  const auth = useProviderAuth();

  return <authContext.Provider value={auth}>{children}</authContext.Provider>;
}

// in the children components, you can import the useAuth to get the context. Here it's actually finishe the work React.useContext(authContext) here, and you don't need to do it in the componet.
export default function useAuth() {
  return React.useContext(authContext);
}


```

Here's an example about authenication of account

```js
import * as React from "react";
import { useLocation, Navigate } from "react-router-dom";
import axios from "axios";

const authContext = React.createContext();


function useProviderAuth() {
  const [authed, setAuthed] = React.useState(false);
  const [user,setUser] = React.useState(undefined);

  // confirm at backend side and and change the state in the login function
  const login = async (email, password) => {
    console.log('email is: ',email, 'password is: ',password)
    const formData = new FormData();
  
    formData.append('email', email);
    formData.append('password', password);
  
    try {
      const res = await axios.post('/api/auth/login',formData);

      console.log(res)
      localStorage.setItem('token',res.data['access_token']);
      setAuthed(true);
      setUser(res.data['user']); // user is a dictionary that contains : username, email, role
      console.log('res.data is : ',res.data, 'authed is : ',authed, 'user is: ',user);
    } catch (error){
      console.log('Error: ',error);
      alert(error.response.data['msg'])
    }
  }


  // change the state in the logout function
  const logout = async() => {
    try{
      const res = await axios.post("/api/auth/logout");
      localStorage.removeItem("token");
      setAuthed(false);
      setUser(null)

    } catch (error){
      console.log('Error: ',error);
    }
  }


// the context that you need about authenication
  return {
    authed,
    user,
    login,
    logout,
  };
}

export function AuthProvider({ children }) {
  const auth = useProviderAuth();

  return <authContext.Provider value={auth}>{children}</authContext.Provider>;
}

export default function useAuth() {
  return React.useContext(authContext);
}

export function RequireAuth({ children }) {
    const { authed } = useAuth();
    const location = useLocation();
  
    return authed === true ? (
      children
    ) : (
      <Navigate to="/auth/login" replace state={{ path: location.pathname }} />
    );
  }

export function RequireAdmin({ children }) {
  const { user,authed } = useAuth();
  const location = useLocation();

  if (authed){
    if (user.role === 'admin'){
      return children;
    }
    else{
      return (
        <>
          <p>You do not have permission for this function</p>
        </>
      )
    }
  }
  else {
    <Navigate to="/auth/login" replace state={{ path: location.pathname }} />
  }

  // return authed === true ? (
  //   children
  // ) : (
  //   <Navigate to="/auth/login" replace state={{ path: location.pathname }} />
  // );
}
```


## [format HTML text](https://www.tutorialspoint.com/html/html_phrase_elements.htm)

How to write HTML text using the original mark.

## How to write logEdit in your project

We need to write the following fileds in the logEdit to cover the essential information:

- Who
- When
- What

e.g.

```python
from datetime import datetime

logEdit = [
{
'editor':editor,
'date':datetime.now(),
'act':'init',
'category':'',
'before':'',
'after':'',
}
]


```

## [JS array delete duplicate element](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

Using reducer to sort js array:

```javascript
onst listTag = listJob.reduce((accumulator,item) => {
        const listTag = item['listTag'];
        listTag.forEach( item => {
            if(!accumulator.includes(item)){
                accumulator.push(item);
            }
        })
        return accumulator; 
    },[])
```

please note that: `[]` is the initial value of the `accumulator`. Thus in this case, the accumulator is a empty list. So inside the function. You handle the accumulaotr.


## [JavaScript async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
Sometimes we'd like to fetch data from database using an API. We need to use `async functions` or `Promise functions`

async function and Promise function
```js
function resolveAfter2Seconds() {
  return new Promise((resolve) => {
    setTimeout(() => {
      const res = 3+4;
      resolve(['resolvedddd',res]);
    }, 2000);
  });
}

async function asyncCall() {
  console.log('calling');
  const result = await resolveAfter2Seconds();
  console.log(result);
  // Expected output: "resolved"
  return 'Get the result'
}
```

In the above code, if you use Promise inside the function, then you shouldn't add `async` keyword before the function

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











