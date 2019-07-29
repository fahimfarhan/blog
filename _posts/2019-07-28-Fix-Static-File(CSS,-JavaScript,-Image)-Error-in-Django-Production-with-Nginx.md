---
layout: post
title:  "Fix Static File(CSS, JavaScript, Image) Error in Django Production with Nginx"
description: Fix Static File(CSS, JavaScript, Image) Error in Django Production with Nginx.
date:   2019-07-28 21:03:36 +0600
categories: django-production ubuntu-server-18.04 linux python django-deployment django-troubleshooting
---
### How to Deploy Django in Production 
### Worst Nightmare of All Time!
Hello internet!

![alt text](https://i.imgur.com/hBHkhGN.jpg "Logo Title Text 1")

If you are a python Django developer, you know that everything goes wrong when you do this:
```
DEBUG = False
```
Your javascript, css, images, these static contents stop working. Today, we'll put an end to this using nginx. 
For demonstrating purposes, I'll be using Ubuntu Server 18.04.2 in my virtual box ( SAY NO XAMPP!!! One should NEVER use XAMPP!!! ). Follow my instructions precisely, and you should be just fine.

[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/4Ouc1VPNpMU/0.jpg)](http://www.youtube.com/watch?v=4Ouc1VPNpMU)
## Prerequisites:
1. Django basics
2. Linux basics 
3. working with terminals
4. working with a terminal based editor (eg, vim, nano etc. ) I'll be using vim as I'm used to it. But feel free to use whatever you like.

 <iframe width="420" height="315"
src="https://www.youtube.com/embed/tgbNymZ7vqY">
</iframe> 

## Steps
1. To start with, 1st make a simple django project.You may make an html template, and connect your css stuffs (not needed though). In your settings, add this: 
```
STATIC_ROOT = os.path.join(BASE_DIR, 'static/') 
```
, and run the command 
``` 
$ manage.py collectstatic
```
. Upload the project into github/bitbucket.

2. install ubuntu server (or anything else of your choice) in a virtual box. Make sure you keep a clone of your newly installed os as you are probably going to break things. The backup will probably save you some time.
3. install nginx:

``` 
sudo apt-get install nginx 
```

4. Bring your project inside the virtualbox. In my case, 

``` 
git clone https://fahimfarhan@bitbucket.org/fahimfarhan/webapp.git 
```
I cloned it somewhere, say, 
```
/home/(user-name)/
```
, so the project root is  
```
/home/(user-name)/(project-name)
```
, and in my case it is 
```
/home/fahimfarhan/webapp
```

5. goto 
```
$ cd /etc/nginx/sites-available 
```
We need to create some files here. Remenber, this location has permission related issues. So, either change permissions, or use sudo command. I'll stick to sudo.
```
$ sudo touch (project-name)
```
, in my case, that is 
```
$ sudo touch webapp
```
. Then 
``` 
$ sudo vim webapp
```
 
 6. Now type in this:
 ```
 server {
    listen 8000;
    server_name 0.0.0.0;

    location = /favicon.ico { access_log off; log_not_found off; }

    location /static/ {
            root /home/(user-name)/(project);
    }

    location / {
            include proxy_params;
            proxy_pass http://unix:/home/(user-name)/(project)/(project).sock;
    }
}
 ```
 Adjust (username) , (project) as needed.
7. If you have multiple projects, you will have to change the port number.
8. save the file, and create a link:
``` 
sudo ln -s /etc/nginx/sites-available/webapp /etc/nginx/sites-enabled 
```
After that, restart nginx:
``` 
$ sudo service nginx restart 
```
9. Goto your project root directory, 
```
 $ cd ~/webapp/
 ``` 
 and run this command:
```
 gunicorn --daemon --workers 1 --bind unix:/home/(username)/(project)/(project).sock (project).wsgi 
 ```
So in my case it is: 
```
 gunicorn3 --daemon --workers 1 --bind unix:/home/fahimfarhan/webapp/webapp.sock webapp.wsgi 
 ```

This should do the trick.


10. (Optional) To automate things, you can simply write a script file, add the commands in the file, and use crontab.
## References
1. Install ubuntu server: [https://hibbard.eu/install-ubuntu-virtual-box/](https://hibbard.eu/install-ubuntu-virtual-box/)
2. django with nginx: [http://rahmonov.me/posts/run-a-django-app-with-nginx-and-gunicorn/](http://rahmonov.me/posts/run-a-django-app-with-nginx-and-gunicorn/)