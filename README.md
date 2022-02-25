**1. Cài đặt dịch vụ Nginx**

        sudo -i

        apt update

        apt install nginx

**2. Cài đặt các gói PHP cần thiết**

        add-apt-repository ppa:ondrej/php

        apt install php7.4-fpm php7.4-curl php7.4-mysql php7.4-xml

**3. Cài đặt dịch vụ MySQL**

        apt install mysql-server

**4. Tạo database mẫu**

        mysql -u root -p

        alter user 'root'@'localhost' identified with mysql_native_password by 'Knn2022@';

        create database my_db;

        flush privigeles;

        quit

**5. Tải mã nguồn Wordpress**

        curl -O https://wordpress.org/latest.tar.gz

**6. Giải nén**

        tar xzvf latest.tar.gz

**7. Copy thư mục Wordpress vào /var/www**

        cp -r wordpress/ /var/www/

**8. Đi vào thư mục Wordpress**

        cd /var/www/wordpress

**9. Copy từ file wp-config-sample.php sang file wp-cpnfig.php**

        cp wp-config-sample.php wp-cpnfig.php

**10. Chỉnh sửa file wp-config.php**

- Thêm database_name, user, password

- Thêm define('FS_METHOD', 'direct'); ở cuối file

**11. Cấp quyền 777 cho thư mục Wordpress**

        chmod -R 777 ./*

**12. Cấu hình Nginx**

        nano /etc/nginx/sites-available/default

-   Sửa đổi đường dẫn thư mục root trỏ tới source nguồn wordpress.
-   Thêm 0.0.0.0 vào dòng server_name;
-   Bỏ comment dòng

    location ~ \ .php$ {

       include snippets/fastcgi-php.conf;
       fastcgi_pass unix:/run/php/php7.4-fpm.sock;

    }

-   Save file lại

**13. Khởi động lại Nginx**

        service nginx restart

**14. Gõ IP máy lên kiểm tra**

**15. Tạo thư mục ssl**

        mkdir /etc/nginx/ssl

**16. Cấp quyền execute**

        chmod 700 /etc/nginc/ssl

**17. Tạo file .crt và file .key**

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/example.key -out /etc/nginx/ssl/example.crt

**18. Cấu hình lại Nginx**

-   Bỏ comment dòng

    listen 443 ssl default_server;
    
    listen [::]:443 ssl default_server;

- Thêm 2 dòng này vô

        ssl_certificate /etc/nginx/ssl/example.crt

        ssl_certificate_key /etc/nginx/ssl/example.key


**19. Khởi động lại Nginx**

        service nginx restart
        nginx -t

**20. Gõ IP máy lên kiểm tra**


