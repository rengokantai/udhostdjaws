#### udhostdjaws
setup django on vanilla ec2
```
sudo apt-get update
sudo apt-get install -y vim build-essential apache2 apache2-mpm-prefork apache2-utils libexpat1 libapache2-mod-wsgi mysql-client mysql-server libmysqlclient-dev python-dev
sudo apt-get install postgresql postgresql-contrib python-psycopg2 
sudo pip install mysql-dev mysqlclient django
```
change pwemission:
```
cd /var/www
sudo chown -R ubuntu .
sudo chmod -R g+w .
django-admin startproject ke
cd ke
python manage.py migrate
```
change apache config
```
cd /etc/apathe2
sudo chown -R ubuntu .
sudo chmod -R g+w .
vim apache2.conf
```
edit,add
```
WSGIScriptAlias / /var/www/ke/ke/wsgi.py
WSGIPythonPath /var/www/ke
<Directory /var/www/ke/ke>
<Files wsgi.py>
Require all granted
</Files>
</Directory>
######Setting up aws server
mysql:
```
DATABASES = {
 'ENGINE' : 'django.db.backends.mysql',
```

export/import database.  
export from local machine,select all tables to sql file.  
import this file.

######7 serving static files
STATIC_ROOT STATIC_URL MEDIA_ROOT MEDIA_URL
```
STATIC_URL ='/static/'
MEDIA_URL='/media/'
STATIC_ROOT='..project/static/'
MEDIA_ROOT='..project/media/'
```

django deploy check.
```
python manage.py check --deploy
```
change settings.py
```
SECURE_CONTENT_TYPE_NOSNIFF = True
SECURE_BROWSER_XSS_FILTER = True
```

