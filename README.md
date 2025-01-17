### Description:

User interface for [Saltpeter](https://github.com/syscollective/saltpeter):

<img src="screenshots/table.png" width="800" >
<img src="screenshots/run.png" width="800" >
<img src="screenshots/results.png" width="800" >

### Installation:
```
cd /opt
```

Download the latest release and unzip it
```
unzip build.zip
rm build.zip
mv build saltpeter-ui
```

Add the nginx configuration(update with your websocket address)
```
vim /etc/nginx/sites-available/saltpeter
```
```
upstream websocket{
        # update with your websocket address
        server local_ip_address:8889;
}

server{
        listen 80;
        server_name localhost;
        location / {
            root /opt/saltpeter-ui;
            index index.html;
            # Other config you desire (TLS, logging, etc)...
            try_files $uri /index.html;
        }

        location /ws {
                proxy_pass http://websocket;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "Upgrade";
                proxy_set_header Host $host;

    		proxy_set_header X-Real-IP $remote_addr;
    		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    		proxy_set_header X-Forwarded-Proto https;

    		proxy_read_timeout  36000s;

    		proxy_redirect off;
        }
}
```
```
ln -s /etc/nginx/sites-available/saltpeter /etc/nginx/sites-enabled/saltpeter
```
Restart nginx
```
service nginx restart
```

Now the UI will run on port 80, on localhost
