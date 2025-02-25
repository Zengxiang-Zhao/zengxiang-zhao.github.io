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
