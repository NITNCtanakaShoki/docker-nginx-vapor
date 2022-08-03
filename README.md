# 操作

```shell
apt update 

apt install -y git

# docker install
apt  install -y docker.io
curl -L "https://github.com/docker/compose/releases/download/1.26.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# 仮想メモリ拡張
fallocate -l 6G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile # swap有効化
cp /etc/fstab /etc/fstab.bak # backup
echo '/swapfile none swap sw 0 0' | tee -a /etc/fstab # swap永続化

# Nginx
apt install -y nginx
systemctl start nginx
unlink /etc/nginx/sites-available/default
unlink /etc/nginx/sites-enabled/default
echo "
server {
    server_name hello.com;
    listen 80;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_pass_header Server;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass_header Server;
        proxy_connect_timeout 3s;
        proxy_read_timeout 10s;
    }
}
" > /etc/nginx/sites-enabled/conf
systemctl restart nginx

# Firewall
ufw enable # yが必要
ufw allow 'Nginx HTTP'

# git clone & build & up
git clone https://github.com/NITNCtanakaShoki/docker-nginx-vapor.git
docker-compose build
docker-compose up -d
docker-compose run migrate
```
