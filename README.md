# deploy-flask
A demo of deployment of flask, uwsgi and nginx stack.

## STEP 1: write a simple flask app

* Establish a virtualenv `virtualenv venv -p python3`
* Activate environment: `. venv/bin/activate`
* `pip install -r requirements.txt`
* Write a simple flask app `hello.py`
* Run `python hello.py`
* Test http://127.0.0.1:5000/ on browser

## STEP 2: host the flask app with uwsgi

* Configure with `uwsgi.ini`
* Run `uwsgi --ini uwsgi.ini`
* Test http://127.0.0.1:8080/ on browser

You will see some log in `demo.log`:

```
*** Starting uWSGI 2.0.18 (64bit) on [Sat May 25 11:56:26 2019] ***
compiled with version: 8.2.1 20181127 on 10 February 2019 15:11:33
os: Linux-5.1.3-arch2-1-ARCH #1 SMP PREEMPT Tue May 21 23:16:12 UTC 2019
nodename: antergos-K55VJ
machine: x86_64
clock source: unix
pcre jit disabled
detected number of CPU cores: 4
current working directory: /media/pika/Workbench/workspace/deploy-flask
detected binary path: /usr/bin/uwsgi
chdir() to .
your processes number limit is 63396
your memory page size is 4096 bytes
detected max file descriptor number: 1024
lock engine: pthread robust mutexes
thunder lock: enabled
uWSGI http bound on 127.0.0.1:8080 fd 3
uwsgi socket 0 bound to UNIX address /tmp/demo.sock fd 6
Python version: 3.7.3 (default, Mar 26 2019, 21:43:19)  [GCC 8.2.1 20181127]
Set PythonHome to venv
Python main interpreter initialized at 0x55761c31c2d0
python threads support enabled
your server socket listen backlog is limited to 100 connections
your mercy for graceful operations on workers is 60 seconds
mapped 291680 bytes (284 KB) for 3 cores
*** Operational MODE: preforking ***
WSGI app 0 (mountpoint='') ready in 1 seconds on interpreter 0x55761c31c2d0 pid: 21349 (default app)
*** uWSGI is running in multiple interpreter mode ***
spawned uWSGI master process (pid: 21349)
spawned uWSGI worker 1 (pid: 21351, cores: 1)
spawned uWSGI worker 2 (pid: 21352, cores: 1)
spawned uWSGI worker 3 (pid: 21353, cores: 1)
spawned uWSGI http 1 (pid: 21354)
[pid: 21351|app: 0|req: 1/1] 127.0.0.1 () {36 vars in 669 bytes} [Sat May 25 11:56:36 2019] GET / => generated 12 bytes in 2 msecs (HTTP/1.1 200) 2 headers in 79 bytes (1 switches on core 0)
SIGINT/SIGQUIT received...killing workers...
gateway "uWSGI http 1" has been buried (pid: 21354)
worker 1 buried after 1 seconds
worker 2 buried after 1 seconds
worker 3 buried after 1 seconds
goodbye to uWSGI.
VACUUM: unix socket /tmp/demo.sock removed.
```

## STEP 3: setup service with systemd for your project

* Place `uwsgi.ini` to `/etc/uwsgi/vassals/uwsgi.ini`
* Create and configure service `/etc/systemd/system/demo.service`
* Start service with `sudo systemctl start demo.service`, and it would run `uwsgi --ini uwsgi.ini` as a service for you

* Check service status with `sudo systemctl status demo.service`
* Stop service with `sudo systemctl stop demo.service`

## STEP 4: set web server with nginx

* Update repo with `sudo apt update`
* Install nginx in your OS: `sudo apt install nginx`
* Configure with `demo.conf`
    * Change `server_name` to YOUR-IP or 127.0.0.1
* Create configure file link to nginx: `sudo ln -s /your/path/to/demo/demo.conf /etc/nginx/sites-available/demo.conf`
    * In Ubuntu, place conf in `/etc/nginx/sites-available/` and link to `/etc/nginx/sites-enabled/`
    * In CentOS, place conf in `/etc/nginx/conf.d/`
* Start nginx service with `sudo systemctl start nginx.service`
* Test http://YOUR-IP/ on browser

You will see some log in `demo.log`:

```
*** Starting uWSGI 2.0.18 (64bit) on [Sat May 25 11:33:07 2019] ***
compiled with version: 8.2.1 20181127 on 10 February 2019 15:11:33
os: Linux-5.1.3-arch2-1-ARCH #1 SMP PREEMPT Tue May 21 23:16:12 UTC 2019
nodename: antergos-K55VJ
machine: x86_64
clock source: unix
pcre jit disabled
detected number of CPU cores: 4
current working directory: /home/pika/demo
detected binary path: /usr/bin/uwsgi
chdir() to /home/pika/demo
your processes number limit is 63396
your memory page size is 4096 bytes
detected max file descriptor number: 1024
lock engine: pthread robust mutexes
thunder lock: enabled
uWSGI http bound on 127.0.0.1:8080 fd 3
uwsgi socket 0 bound to UNIX address /tmp/demo.sock fd 6
Python version: 3.7.3 (default, Mar 26 2019, 21:43:19)  [GCC 8.2.1 20181127]
Set PythonHome to /home/pika/demo/venv
Python main interpreter initialized at 0x56511f40ba00
python threads support enabled
your server socket listen backlog is limited to 100 connections
your mercy for graceful operations on workers is 60 seconds
mapped 291680 bytes (284 KB) for 3 cores
*** Operational MODE: preforking ***
WSGI app 0 (mountpoint='') ready in 0 seconds on interpreter 0x56511f40ba00 pid: 17701 (default app)
*** uWSGI is running in multiple interpreter mode ***
spawned uWSGI master process (pid: 17701)
spawned uWSGI worker 1 (pid: 17702, cores: 1)
spawned uWSGI worker 2 (pid: 17703, cores: 1)
spawned uWSGI worker 3 (pid: 17704, cores: 1)
spawned uWSGI http 1 (pid: 17705)
[pid: 17702|app: 0|req: 1/1] 192.168.100.102 () {46 vars in 846 bytes} [Sat May 25 11:33:15 2019] GET / => generated 12 bytes in 3 msecs (HTTP/1.1 200) 2 headers in 79 bytes (1 switches on core 0)
SIGINT/SIGQUIT received...killing workers...
gateway "uWSGI http 1" has been buried (pid: 17705)
worker 1 buried after 1 seconds
worker 2 buried after 1 seconds
worker 3 buried after 1 seconds
goodbye to uWSGI.
VACUUM: unix socket /tmp/demo.sock removed.
```

* Check service status with `sudo systemctl status nginx.service`
* Stop service with `sudo systemctl stop nginx.service`
* Reload config without stoping a service with `sudo systemctl reload nginx.service`

## Further reading

[使用 uWSGI、nginx、systemd 部署 Django](https://blog.liang2.tw/posts/2016/05/django-deploy-uwsgi-nginx-systemd/)