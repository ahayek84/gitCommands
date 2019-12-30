**Steps to install Apache on Ubuntu**
* Install Apache2 via <br>
`sudo apt-get update` <br>
`sudo apt install apache2` <br>
`sudo apt install apache2-dev` <br>

* Enable Proxy via <br>
`sudo a2enmod ssl` <br>
`sudo a2enmod proxy` <br>
`sudo a2enmod proxy_balancer` <br>
`sudo a2enmod proxy_http` <br>

* Install mod_uwsgi, mod_proxy_uwsgi via <br>
`cd /tmp/` <br>
`wget https://projects.unbit.it/downloads/uwsgi-latest.tar.gz` <br>
`tar xvzf uwsgi-latest.tar.gz` <br>
`cd uwsgi-2.0.18/apache2` <br>
`sudo apxs2 -i -c mod_proxy_uwsgi.c` <br>
`chmod 644 /usr/lib/apache2/modules/mod_proxy_uwsgi.so` <br>
`sudo apxs2 -i -c mod_uwsgi.c` <br>
`chmod 644 /usr/lib/apache2/modules/mod_uwsgi.so` <br>

* **load modules in Apache** <br>
`cd /etc/apache2/mods-available` <br>

* create the following file :  proxy_uwsgi.load 
`sudo vi proxy_uwsgi.load` <br>
and paste this line in it 
`LoadModule proxy_uwsgi_module /usr/lib/apache2/modules/mod_proxy_uwsgi.so` <br>

* create the following file :  uwsgi.load
`sudo vi uwsgi.load` <br>
and paste this line in it 
`LoadModule uwsgi_module /usr/lib/apache2/modules/mod_uwsgi.so` <br>

* **activate modules in Apache** <br>
`sudo a2enmod proxy_uwsgi` <br>
`sudo a2enmod uwsgi` <br>


* Install uwsgi into the project env <br>
`conda activate ai_36` <br>
`sudo pip install uwsgi` <br>

* Enable uwsgi with apach2 <br>
`sudo a2enmod proxy_uwsgi` <br>
`sudo a2enmod uwsgi` <br>

* open default host for tunnling requests <br>
`vi /etc/apache2/sites-available/000-default.conf` <br>

* add the following virtual host tag <br>
<pre>
>VirtualHost *:80<
    ServerAdmin webmaster@localhost
    ServerName ai
    ServerAlias ai
    ProxyRequests Off
    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>
    ProxyPreserveHost on
    ProxyPass / uwsgi://127.0.0.1:8000/
    ProxyPassReverse / uwsgi://127.0.0.1:8000/
>/VirtualHost<
</pre>


* restart apach2 <br>
`sudo systemctl restart apache2` <br>


**Commands for uwsgi server**
* To start a uwsgi server <br>
`uwsgi --ini uwsgi.ini` <br>

* To check uwsgi server log <br>
`uwsgi --reload /tmp/ai-master.pid` <br>

* To stop uwsgi <br>
`pkill -f uwsgi -9` <br>


**Commands for Apach2 server**
* To start a Start server <br>
`sudo systemctl start apache2` <br>

* To check apache2 server log <br>
`tail -f /var/log/apache2/error.log` <br>

* To stop apach2 <br>
`sudo systemctl stop apache2` <br>



**Important Notes**
* if installation for uwsgi with pip fails for any reason use <br>
`conda install -c conda-forge/label/gcc7 uwsgi` <br>

* uwsgi is only available for Linux and Mac and there is <br>
  no support to windows yet 
  
* ** Never use mod_wsgi with machine learning applications Never **

* ** Never Install Anaconda in the root folder which means Never use --user with pip install **

**uwsgi.ini**
<pre>
[uwsgi]
project = ai
base = /var/www/AI

chdir=%(base)
wsgi-file=%(project)/wsgi.py
socket = 127.0.0.1:8000

static-map = /static=%(base)/static
static-expires = /* 7776000
offload-threads = %k

master=True
pidfile=/tmp/ai-master.pid
vacuum=True
max-requests=5000
daemonize=%(base)/log/ai.log
</pre>

**Refernecs**
* https://anaconda.org/conda-forge/uwsgi <br>
* https://uwsgi-docs.readthedocs.io/en/latest/ <br>
* https://uwsgi-docs.readthedocs.io/en/latest/Changelog-2.0.18.html <br>



  
















