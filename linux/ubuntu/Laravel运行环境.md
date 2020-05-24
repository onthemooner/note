## Step
1. 更新Ubuntu版本库
2. 安装Nginx
3. 安装PHP-FPM 7.4
4. 安装MariaDB
5. 安装PHP Composer
6. 配置开发目录

## 1.更新Ubuntu版本库
```
sudo apt update
sudo apt upgrade
```

## 2.安装Nginx
```
sudo apt install nginx -y
```
安装完成后，启动nginx服务并将其添加到使用systemctl命令在系统引导时自动启动。
```
systemctl start nginx
systemctl enable nginx
```
Nginx运行在80端口上，使用netstat命令检查相关统计信息。
```
netstat -plntu
```

## 3.安装PHP 7.4和PHP-FPM
```
sudo apt install php7.4 php7.4-curl php7.4-common php7.4-cli php7.4-mysql php7.4-mbstring php7.4-fpm php7.4-xml php7.4-zip -y
```
接下来，转到PHP配置目录并编辑fpm目录中的php.ini文件。
```
vim /etc/php/7.4/fpm/php.ini
```
取消注释以下CGI行，并将该值更改为0。
```
cgi.fix_pathinfo=0
```

现在我们可以启动PHP-FPM并使其在系统启动时自动启动。
```
systemctl start php7.4-fpm
systemctl enable php7.4-fpm
```
默认在Ubuntu上，PHP-FPM在sock文件下运行。 使用netstat命令以下列方式检查PHP-FPM sock文件：
```
netstat -pl | grep php7.4-fpm
```


## 4.安装MariaDB
这是可选的，但是当您的Laravel项目基于MySQL时，您需要为您的项目执行此操作。 我们将在系统上安装MariaDB最新版本10.1。

使用以下命令从存储库安装MariaDB。
```
sudo apt install mariadb-server mariadb-client -y
```
安装完成后，运行MariaDB并使其在系统引导时启动。
```
systemcl start mysql
systemctl enable mysql
```
MariaDB在端口3306上启动，使用netstat命令检查它。
```
netstat -plntu
```
启动并检查MariaDB

接下来，使用下面的'mysql_secure_installation'命令配置MariaDB root密码。
```
mysql_secure_installation
```
输入您的root密码，删除匿名用户，远程禁止root登录等。
```
Set root password? [Y/n] Y
Remove anonymous users? [Y/n] Y
Disallow root login remotely? [Y/n] Y
Remove test database and access to it? [Y/n] Y
Reload privilege tables now? [Y/n] Y
```
MariaDB的安装和配置已完成。

## 5.安装PHP Composer
```
sudo apt install composer -y
```
阿里云镜像，全局配置（推荐），所有项目都会使用该镜像地址
```
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```
取消配置
```
composer config -g --unset repos.packagist
```

项目配置
仅修改当前工程配置，仅当前工程可使用该镜像地址：
```
composer config repo.packagist composer https://mirrors.aliyun.com/composer/
```
取消配置
```
composer config --unset repos.packagist
```

## 6.配置开发目录
```
vim /etc/nginx/sites-available/app.conf
```

写入配置
```
server {
    listen 80;
    server_name app.test;
    root /var/www/app;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;

        autoindex on;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```
重启nginx
```
systemctl restart nginx
nginx -s reload
```
