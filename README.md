very good video.
In case someone needs the commands that you used in aws:

sudo apt-get upgrade -y
sudo apt-get update -y

1. Deploy Python virtual env
sudo apt-get install python3-venv -y
python3 -m venv env

2. Activate env
source env/bin/activate

3. Install Django
pip3 install django

4. Clone the git project
git clone https://github.com/ShobiExplains/AwsDemo.git

5. Instal NGINX and GUNICORN
pip3 install gunicorn ## install without sudo..
sudo apt-get install nginx -y
pip install psycopg2-binary

6. Connect gunicorn
gunicorn --bind 0.0.0.0:8000 TestProject.wsgi:application ## similar to runserver

7. Install supervisor
sudo apt-get install -y supervisor ## This command holds the website after we logout

8. Config supervisor
cd /etc/supervisor/conf.d
sudo touch gunicorn.conf

##In the file file do following###
##
[program:gunicorn]
directory=/home/ubuntu/aws-django-test
command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind  unix:/home/ubuntu/aws-django-test/app.sock new_app.wsgi:application
autostart=true
autorestart=true
stderr_logfile=/var/log/gunicorn/gunicorn.err.log
stdout_logfile=/var/log/gunicorn/gunicorn.out.log

[group:guni]
Program:gunicorn
##
####endfile####


9. Connect file to supervisor
sudo supervisorctl reread

##if we receive the following error than we need to create the directory:
ERROR: CANT_REREAD: The directory named as part of the path /var/log/gunicorn/gunicorn.out.log does not exist. in section 'program:gunicorn' (file: '/etc/supervisor/conf.d/gunicorn.conf')

sudo mkdir -p /var/log/gunicorn
sudo supervisorctl reread
sudo supervisorctl update

10. Check if gunicorn is running in background
sudo supervisorctl status

11. Create nginx configuration for django site
sudo vim /etc/nginx/sites-available/django.conf

###inhalt##
server {
    listen 80;
    server_name 18.195.252.82;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/ubuntu/aws-django-test/app.sock;
    }
}
############

12. Test file configuration

sudo ln /etc/nginx/sites-available/django.conf /etc/nginx/sites-enabled/
sudo nginx -t


13. restart NGINX server
sudo service nginx restart



14: Open the browser:


http://aws_instance_ip:8000/hello/andrey
