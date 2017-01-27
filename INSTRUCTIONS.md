# Instructions

## Credits
These instructions are more or less adapted from: [here](http://michal.karzynski.pl/blog/2013/06/09/django-nginx-gunicorn-virtualenv-supervisor/)


## Installing the required yum packages

Update the repository, and install the required development toos.  
We need git to retrieve git repo, and gcc is needed by python-pip 
to compile some of its packages.
```
yum update -y
yum groupinstall -y 'development tools'
```

OPTIONAL: e.g, if you want to get info about a package

```
yum install -y yum-utils
```

OPTIONAL: might be neded to compile some packages

```
yum install -y zlib-dev openssl-devel sqlite-devel bzip2-devel wget
```

Installing Python 2.7 from SCLO repository

```
yum install -y centos-release-SCL
yum install -y python27
```

Setup python libraries for ldconfig

```
echo "/opt/rh/python27/root/usr/lib64" > /etc/ld.so.conf.d/python27.conf ldconfig
```

Setup mysql support for installing mysql-python via pip, to allow django to access mysql servers

```
yum install mysql mysql-devel
```

Run the enable script for Python 2.7 every time we login

```
echo "source /opt/rh/python27/enable" >> .bashrc
```

Run the script now so we can use Pyton 2.7

```
source /opt/rh/python27/enable
```


## Install the packages from PyPI via PIP

Update the default pip to the lastest

```
pip install --upgrade pip
```

Update the virtualenv package so it will use the latest pip when creating new virtual environements

```
pip install --upgrade virtualenv
```
### Install python-virtualenvwrapper
Install virtualenvwrapper so it is easier to work with virtualenv

```
pip install virtualenvwrapper
```

Run the activation script for virtualenvwrapper

```
echo "source `which virtualenvwrapper.sh`" >> .bashrc
```

Run it now, so if you want to use it

```
source `which virtualenvrwapper.sh`
```

### Install python-supervisor
Install the supervisor package, that will run the gunicorn server/s

```
pip install supervisor
```

Create symlinks in /usr/bin to the supervisor programs

```
ln -s /opt/rh/python27/root/usr/bin/supervisord{ctl,d} /usr/bin/
```

Create the folders for supervisord daemon in /etc/

```
mkdir -p /etc/supervisor/conf.d
```

Download into /etc/init.d/supervisord the service script for supervisord

```
curl  https://raw.githubusercontent.com/azuer88/django-server-setup/master/files/etc/init.d/supervisord -o /etc/init.d/supervisord
```

And make it executable

```
chmod a+x /etc/init.d/supervisord
```

Download into /etc/supervisor/supervisord.conf the default configuration file for supervisord daemon

```
curl https://raw.githubusercontent.com/azuer88/django-server-setup/master/files/etc/supervisor/supervisord.conf> -o /etc/supervisor/supervisord.conf
```

Make sure that it starts automatically

```
chkconfig supervisord --levels 2345 on
```

Install nginx web servers

```
yum install -y nginx
```

Make sure that it start automatically

```
chkconfig nginx --levels 2345 on
```

## Setup system user and group

Create group and user that will be running the django projects

```
groupadd --system webapps
useradd --system --gid webapps --shell /bin/bash --home /webapps django
```

Create the user 'django' home, and populate it with the bash initialization hooks. Change the owner to 'django' and group to 'webapps'

```
mkdir /webapps
cp ~/.bash* /webapps
chown -R django:webapps $_
```

## Setup helper script

Switch to user 'django'

```
su - django
```

Get the initialization helper script for django projects from github

```
git clone https://github.com/azuer88/django-webapp-initscript
```

Create a bin folder where we will put all executables for user 'django', mostly just the above helper script, but you'll never know...

```
mkdir bin ln -s /webapps/django-webapp-initscript/src/* bin
```

Add above location to our PATH

```
echo "OLDPATH=$PATH" >> .bashrc
echo "export PATH=/webapps/bin:$OLDPATH" >>> .bashrc
```

Exit and switch back to django to enable the above changes, if needed 

```
exit 
su - django
```


## Finally

We are DONE! Great!


*NB:* 
* Make sure to install mysql-python (`pip install mysql-python`) if you need to need to access mysql servers.
* Make sure to install gunicorn, usually together with django, e.g., ```pip install django gunicorn```.
* You can run ```make_webapp.py <django_project_name> <FQDN>``` after you create the django project
* Make sure to name the virtualenv the same as your django project name.

 
