## Project Structure

```md
myproject/
│
├── myproject/
│   ├── settings.py
│   ├── urls.py
│   └── static/              <-- PROJECT LEVEL STATIC
│       └── project.css
│
├── main/
│   ├── views.py
│   ├── urls.py
│   ├── templates/main/
│   │   └── index.html
│   └── static/main/         <-- APP LEVEL STATIC
│       └── app.css
│
└── manage.py
```
## Apache conf file : testsite.conf
There is two way to serve the static files 
1) Apache server : serve the both static and media
2) whitenoise : only serve the static file

we are using whitenoise for now.

```bash
mahaveer@dlbhu-Veriton-S2690G:/etc/apache2/sites-enabled$ cat testsite.conf
<VirtualHost *:80>
    ServerName localhost

    ProxyPreserveHost On
    ProxyPass /static/ !
    
    ProxyPass / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/

    Alias /static/ /home/mahaveer/myproject/staticfiles/

    <Directory /home/mahaveer/myproject/staticfiles>
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/myproject_error.log
    CustomLog ${APACHE_LOG_DIR}/myproject_access.log combined
</VirtualHost>
mahaveer@dlbhu-Veriton-S2690G:/etc/apache2/sites-enabled$ 

```

## Systemd file : gunicorn.service
```bash
mahaveer@dlbhu-Veriton-S2690G:/etc/systemd/system$ cat gunicorn.service
[Unit]
Description=Gunicorn daemon for testsite
After=network.target

[Service]
User=mahaveer
Group=www-data
WorkingDirectory=/home/mahaveer/Desktop/test-django/testsite

ExecStart=/home/mahaveer/Desktop/test-django/testsite/env/bin/gunicorn \
          --workers 3 \
          --bind 127.0.0.1:8000 \
          testsite.wsgi:application

Restart=always
Environment=PYTHONUNBUFFERED=1

[Install]
WantedBy=multi-user.target

mahaveer@dlbhu-Veriton-S2690G:/etc/systemd/system$ 


```