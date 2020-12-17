# 运行环境

* Ubuntu 16.04, Ubuntu 18.04 or Ubuntu 20.04
* NGINX
* Node.js
* MySQL 5.7 or 8.0（MariaDB也可以）
* A server with at least 1GB memory
* A registered domain name

## 1.创建一个新用户
```
# Create a new user and follow prompts
adduser <user>

# Add user to superuser group to unlock admin privileges
usermod -aG sudo <user>

# Then log in as the new user
su - <user>
```

## 2.更新apt
```
# Update package lists
sudo apt-get update

# Update installed packages
sudo apt-get upgrade
```

## 3.安装nginx
```
sudo apt install nginx -y

systemctl start nginx

systemctl enable nginx

sudo ufw allow 'Nginx Full'
```

## 4.安装MariaDB
```
sudo apt install mariadb-server mariadb-client -y

systemctl start mysql

systemctl enable mysql
```
创建ghost用户并给予权限
```
sudo mysql

create user 'xxx'@'localhost' identified by 'pwd';

grant all privileges on ghost_prod.* to 'xxx'@'localhost';

quit
```

## 5.安装Node.js
```
# Add the NodeSource APT repository for Node 12
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash

# Install Node.js
sudo apt-get install -y nodejs
```

## 6.安装Ghost-CLI
```
sudo npm install ghost-cli@latest -g
```

## 7.安装Ghost
```
# Create directory: Change `sitename` to whatever you like
sudo mkdir -p /var/www/ghost

# Set directory owner: Replace <user> with the name of your user
sudo chown <user>:<user> /var/www/ghost

# Set the correct permissions
sudo chmod 775 /var/www/ghost

# Then navigate into it
cd /var/www/ghost

# Run the install process
ghost install
```

## 8.配置SSL
```
cd /etc/nginx/site-availables

vim www.xxx.com.conf

ln -s /etc/nginx/site-availables/www.xxx.com.conf /etc/nginx/site-enables/www.xxx.com.conf
```

配置内容如下：
```
server {
    listen 443 ssl;

    ssl_certificate "/etc/nginx/sslcert/1_www.xxx.com_bundle.crt";
    ssl_certificate_key "/etc/nginx/sslcert/2_www.xxx.com.key";
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    server_name www.xxx.com;
    root /var/www/ghost/system/nginx-root; # Used for acme.sh SSL verification (https://acme.sh)

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:2368;
    }

    location ~ /.well-known {
        allow all;
    }

    client_max_body_size 50m;
}

# http jump to https config
server {
    listen 80;

    server_name www.xxx.com;

    return 301 https://$host$request_uri;
}
```