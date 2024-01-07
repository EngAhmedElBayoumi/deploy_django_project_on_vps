# Deploy django project on vps using apache2

## 1- update and upgrade the operating system by 

```
    sudo apt update
    sudo apt upgrade
```

## 2- Install essential software such as Python, pip, and Apache2: 

```
    sudo apt install python3 python3-pip apache2 libapache2-mod-wsgi-py3 python3-venv
```

## i will do the directories by this way you can create it by another way no problem 

## 3- go to /var/www and create virtualenv like this  and create src to put you project on it

```
    python3 -m venv venv
    cd venv
    mkdir src
```



## 4- create static and media dir like this :

```
    mkdir root-media
    mkdir root-static
```

## 5- give permission to media dir to be 0777 be  this command

```
    chmod 0777 root-media
```

## 6- go to src and upload your project you can use git or winscp or any way you want

## 7- back to venv parent dir and activate it

```
    cd /var/www
    source venv/bin/activate
```

## 8- install all package that you used it i will go to src and install it from reqirements.txt file like this

```
    cd /var/www/venv/src
    pip freeze -r requirements.txt
```

## 9- open settings.py file and update debug to False , media , static root and add your domain on ALLOWED_HOSTS

### the media and static like this :

```
STATIC_URL = 'static/'
STATICFILES_DIRS = [
    BASE_DIR, 'static'
]
STATIC_ROOT = '/var/www/static-root/'
MEDIA_URL = '/media/'
MEDIA_ROOT = '/var/www/media-root/'

```

## 10- collect static by this command:

```
    python manage.py collectstatic
```

## 11- Create an Apache2 virtual host configuration file

```
    sudo nano /etc/apache2/sites-available/000-default.conf
```

### in nano file we will write this (notic the code contain paths depend on the paths that i create it and the project name i always rename it project )

```
<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
    ServerName domain.com
</VirtualHost>

<VirtualHost *:443>
ServerName domain.com

ServerAdmin webmaster@localhost
Alias /static /var/www/static-root
<Directory /var/www/static-root>
   Require all granted
 </Directory>
Alias /media /var/www/media-root
<Directory /var/www/media-root>
   Require all granted
</Directory>
<Directory /var/www/venv/src/project>
    <Files wsgi.py>
        Require all granted
    </Files>
</Directory>
WSGIDaemonProcess project python-path=/var/www/venv/src/:/var/www/venv/lib/python3.9/site-packages
WSGIProcessGroup project
WSGIScriptAlias / /var/www/venv/src/project/wsgi.py
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

## 12- Enable the site:

```
sudo a2ensite 000-default.conf
```

## 13- restart apache:

```
    sudo systemctl restart apache2
```

# congratolation the website now is online 

### note i don't say any thing about database don't forget to change database from sqltite3 to another dbms 










