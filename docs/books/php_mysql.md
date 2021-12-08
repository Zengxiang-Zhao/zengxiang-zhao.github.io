---
layout: default
title: Learning PHP, MySQL & JavaScript
parent: books
date:   2021-11-10 20:28:05 -0400
categories: PHP
nav_order : 1
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

# Chapter 1

![client and server relationship](/assets/images/php_mysql/client_server.png)


The steps in the request and response sequence are as follows:
1. You enter http://server.com into your browser’s address bar.
2. Your browser looks up the Internet Protocol (IP) address for server.com.
3. Your browser issues a request for the home page at server.com.
4. The request crosses the internet and arrives at the server.com web server.
5. The web server, having received the request, looks for the web page on its disk.
6. The web server retrieves the page and returns it to the browser.
7. Your browser displays the web page.

![the dynamic relationship of client and server](assets/images/php_mysql/client_server_dynamic.png)

The steps are as follows:
1. You enter http://server.com into your browser’s address bar.
2. Your browser looks up the IP address for server.com.
3. Your browser issues a request to that address for the web server’s home page.
4. The request crosses the internet and arrives at the server.com web server.
5. The web server, having received the request, fetches the home page from its hard disk.
6. With the home page now in memory, the web server notices that it is a file incor‐ porating PHP scripting and passes the page to the PHP interpreter.
7. The PHP interpreter executes the PHP code.
8. Some of the PHP contains SQL statements, which the PHP interpreter now passes to the MySQL database engine.
9. The MySQL database returns the results of the statements to the PHP interpreter.
10. The PHP interpreter returns the results of the executed PHP code, along with the results from the MySQL database, to the web server.
11. The web server returns the page to the requesting client, which displays it.


### asynchronous communication

异步串行通信

Using asynchronous communication, web pages perform
data handling and send requests to web servers in the background—without the web user being aware that this is going on.

### The difference between PHP and Javascript

PHP runs on the server, whereas JavaScript runs on the client.

# Chapter 2 :Setting Up a Development Server

在本地建立development server 的好处：
1. 每次脚本的修改没有必要直接上传到云端的server，这样会费时。当你的script有bug时，这也是不安全的。
2. 同时出错时也会让人尴尬。


