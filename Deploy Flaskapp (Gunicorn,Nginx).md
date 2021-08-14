วิธีใช้งาน Flask ด้วย Gunicorn และ Nginx บน Ubuntu

**1. Installation necessity**

```
$ sudo apt-get update
$ sudo apt-get install python3-pip python3-dev nginx
$ sudo apt-get install python3-venv
```

**2. Setup workspace**

Work space folder
```
$ mkdir flaskapp
$ cd flaskapp
```
Flaskpp environment 
```
$ python3 -m venv flaskvenv
$ source flaskvenv/bin/activate
```
Install required libraries in here. To check installed packages type
```
$ pip freeze
```

To check python version type
```
$ python --version
```
Copy your flaskapp to this folder or create a new app
```
$ sudo nano flaskapp.py
```
```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def index():
    return "<h1>Hello, Flask application.</h1>"

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```
Run and test the app. It will be acccesed via http://server_ip:5000
```
$ python flaskapp.py
```
Create wsgi.py to call flaskapp
```
$ sudo nano wsgi.py
```
```
from project1 import app

if __name__ == "__main__":
    app.run()
```
To test gunicorn type:
```
$ gunicorn --bind 0.0.0.0:5000 wsgi:app
```
Website will be available on http://server_ip:5000. If nothing goes worng, ctrl+c and deactivate venv

**3. Service Setup**
Create unit file in /etc/systemd/system
```
$ sudo nano /etc/systemd/system/project1.service
```
```
[Unit]
Description=Gunicorn instance to serve **name**
After=network.target

[Service]
User=saitarn15d
Group=www-data
WorkingDirectory=/home/ubuntu/flaskapp ** directory from step2 **
Environment="PATH=/home/ubuntu/flaskapp/flaskvenv/bin" ** directory from step2 **
ExecStart=/home/ubuntu/flaskapp/flaskvenv/bin/gunicorn --workers 3 --bind unix:flaskapp.sock -m 007 wsgi:app

[Install]
WantedBy=multi-user.target
```
Start gunicorn service
```
$ sudo systemctl start flaskapp
$ sudo systemctl enable flaskapp
```

**4. Nginx**
```
$ sudo nano /etc/nginx/sites-available/flaskapp
```
```
server {
    listen 80;
    server_name serverip;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/ubuntu/flaskapp/flaskapp.sock;
    }
}
```
flaskapp.sock is in workspace folder 

```
$ sudo ln -s /etc/nginx/sites-available/flaskapp /etc/nginx/sites-enabled
```
To test syntax
```
$ sudo nginx -t
```
If no error
```
$ sudo systemctl restart nginx
```
##TO UPDATE FOR CODE CHANGE
```
$ sudo systemctl restart **servicename**
$ sudo systemctl restart flaskapp
```
The service is in /etc/systemd/system//servicename/.service


DONE!!!
credit : https://medium.com/@saitarn14/%E0%B8%A7%E0%B8%B4%E0%B8%98%E0%B8%B5%E0%B9%83%E0%B8%8A%E0%B9%89%E0%B8%87%E0%B8%B2%E0%B8%99-flask-%E0%B8%94%E0%B9%89%E0%B8%A7%E0%B8%A2-gunicorn-%E0%B9%81%E0%B8%A5%E0%B8%B0-nginx-%E0%B8%9A%E0%B8%99-ubuntu18-d25c56582fd7
