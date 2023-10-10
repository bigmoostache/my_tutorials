sudo apt-get install nginx
sudo apt install python3-certbot-nginx
sudo apt-get install certbot
sudo apt update

sudo nano /etc/nginx/sites-available/dashboard.blends.fr

server {
    listen 80;
    server_name dashboard.blends.fr;

    location / {
        proxy_pass http://localhost:8443;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}


sudo ln -s /etc/nginx/sites-available/dashboard.blends.fr /etc/nginx/sites-enabled/

sudo nginx -t

sudo service nginx restart

sudo certbot certonly --nginx -d dashboard.blends.fr