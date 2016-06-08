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
```
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
######9 switch to production
```
ALLOWED_HOSTS = ['y.ke.me']
DEBUG = False
```
#####5
######4
add a hosted zone: domain from registrar.  
copy NS to registrar.
######5
in route53, add A record,name=, value=52.1.2.3  
add CNAME record, name=www,value=yd.me
#####6
######2 Integrating s3 with django
create a bucket, add bucket policy, generate:  
Actions:GetObject,peincipal=* arn=bucketname/*  
Edit core policy->  
```
sudo pip install boto django-storages
```
edit settings.py
```
INSTALLED_APPS=(
'storages',
)
```
Add:
```
DEFAULT_FILE_STORAGE = 'storages.backends.s3boto.S3BotoStorage'

AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```
create a new user only give permission to s3.  
add a policy: set principal to username we just created.  
actions=all actions, arn= bucketname/*, bucketname
######3
continue seetting. default: media files, like user upload. static: css,js
```
AWS_STORAGE_BUCKET_NAME=''
STATICFILES_STORAGE = 'storages.backends.s3boto.S3BotoStorage'
```
create a new py file to contain S3BOtoStorage subclass: (location: same lavel of manage.py)
```
cat aws_storage_classes.py (ctrl+d)
```
content:
```
from storages.backends.s3boto import S3BotoStorage

class StaticStorage(S3BotoStorage):
    location = 'static'
class MediaStorage(S3BotoStorage):
    location ='media'
```
then,re-edit
```
DEFAULT_FILE_STORAGE = 'aws_storage_classes.MediaStorage'
STATICFILES_STORAGE = 'aws_storage_classes.StaticStorage'
```
create two folders in bucket, media,static.  
then,re-edit
```
AWS_S3_DOMAIN="%s.s3.amazonaws.com"%AWS_STRAGE_BUCKET_NAME
```
######4 Integrating cloudfront
replace AWS_S3_DOMAIN with cloudfront link

#####7
######4 removing security key
```
sudo cat > keys.json
```
edit
```
{"django_key":"1213","aws_secret":""}
```
then ctrl+D  
in settings.py
```
import json
SECRET_KEY=''
AWS_SECRET_ACCESS_KEY=''
with open('keys.json') as data:
    data = json.load(data)
    SECRET_KEY=data['django_key']
    AWS_SECRET_ACCESS_KEY=data['aws_secret']
```
#####8
######backup and create AMI
for vanilla ubuntu
```
sudo update-rc.d mysql defaults
sudo update-rc.d apache2 defaults
```
#####9
create load balancer. set
```
ALLOWED_HOSTS = ['*']
```
#####10
######2 creating autoscaling
create launch configuration., using ami
######3 Triggering scaling via cloudwatch
autoscaling group->select->scaling policies  
open cloudwatch, 
select metric:elb,search latency->next  
whenever latency>=3  
also create a low traffic alarm.  
select metric:elb,search request->next.
whenever requestcount<=100 period=5min,statictic=sum  

back to settings,take the action add 1 instances,...
