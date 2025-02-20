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
