# Cấu hình Wordpress
**1. Cài đặt dịch vụ Nginx**

        sudo -i

        apt update

        apt install nginx

> Lên trình duyệt gõ IP máy để kiểm tra

![](https://i.imgur.com/e0iSXbw.png)

**2.  Cài đặt dịch vụ MySQL**

        apt install mysql-server


**3. Tạo database mẫu**

        mysql -u root -p

        CREATE DATABASE my_db;
        
        CREATE USER 'db_user'@'localhost' IDENTIFIED BY '123456';

        GRANT ALL PRIVILEGES ON * . * TO 'db_user'@'localhost';

        FLUSH PRIVILEGES;

        EXIT

**4. Tải mã nguồn Wordpress**

        curl -O https://wordpress.org/latest.tar.gz

**5. Giải nén**

        tar xzvf latest.tar.gz

**6. Copy thư mục Wordpress vào /var/www**

        cp -r wordpress/ /var/www/

**7. Đi vào thư mục Wordpress**

        cd /var/www/wordpress

**8. Copy file wp-config-sample.php ra file wp-config.php**

        cp wp-config-sample.php wp-config.php

**9. Chỉnh sửa file wp-config.php**

        nano wp-config.php

> 

        <?php
        define( 'DB_NAME', 'my_db' );

        /** Database username */
        define( 'DB_USER', 'db_user' );

        /** Database password */
        define( 'DB_PASSWORD', '123456' );

        /** Database hostname */
        define( 'DB_HOST', 'localhost' );

        /** Database charset to use in creating database tables. */
        define( 'DB_CHARSET', 'utf8' );

        /** The database collate type. Don't change this if in doubt. */
        define( 'DB_COLLATE', '' );

        $table_prefix = 'wp_';

        define ('FS_METHOD', 'direct');

        define( 'WP_DEBUG', false );

        require_once ABSPATH . 'wp-settings.php';



![](https://i.imgur.com/deMSrfW.png)

**10. Cấp quyền 777 cho thư mục Wordpress**

        chmod -R 777 ./*

**11. Cài các gói PHP cần thiết**

        add-apt-repository ppa:ondrej/php

        apt install php7.4-fpm php7.4-mysql

**12. Cấu hình Nginx**

        nano /etc/nginx/sites-available/default

> 

        server {
            listen 80 default_server;
            listen [::]:80 default_server;

            # SSL configuration
            #
            # listen 443 ssl default_server;
            # listen [::]:443 ssl default_server;

            root /var/www/wordpress;

            # Add index.php to the list if you are using PHP
            index index.html index.htm index.php;

            server_name 0.0.0.0;

            location / {
                    try_files $uri $uri/ =404;
            }
            location ~ \.php$ {
                    include snippets/fastcgi-php.conf;
                    fastcgi_pass unix:/run/php/php7.4-fpm.sock;
            }

        }

![](https://i.imgur.com/3u4fyVU.png)

**13. Khởi động lại Nginx**

        service nginx restart

**14. Gõ IP máy lên kiểm tra**

![](https://i.imgur.com/WRddb6I.png)

# Cấu hình Chứng chỉ SSL

**1. Tạo thư mục ssl**

        mkdir /etc/nginx/ssl

**2. Tạo file .crt và file .key**

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/key.key -out /etc/nginx/ssl/cert.crt

**3. Cấp quyền 777**

        chmod 777 /etc/nginx/ssl/*

**4. Cấu hình lại Nginx**

        nano /etc/nginx/sites-available/default

> 

        server {
            listen 80 default_server;
            listen [::]:80 default_server;

            # SSL configuration
            #
            listen 443 ssl default_server;
            listen [::]:443 ssl default_server;


            ssl_certificate /etc/nginx/ssl/cert.crt;
            ssl_certificate_key /etc/nginx/ssl/key.key;

            root /var/www/wordpress;

            # Add index.php to the list if you are using PHP
            index index.html index.htm index.php;

            server_name 0.0.0.0;

            location / {
                    try_files $uri $uri/ =404;
            }
            location ~ \.php$ {
                    include snippets/fastcgi-php.conf;
                    fastcgi_pass unix:/run/php/php7.4-fpm.sock;
            }

        }



![](https://i.imgur.com/9YKGaCQ.png)


**5. Khởi động lại Nginx**

        service nginx restart

**6. Gõ https://<IP máy> lên kiểm tra**

![](https://i.imgur.com/AxdBFFh.png)


