# Cấu hình IP tĩnh
        sudo nano /etc/netplan/00-installer-config.yaml
> Copy đoạn code này vô

        # This is the network config written by 'subiquity'
        network:
          ethernets:
            ens160:
              dhcp4: no
              addresses: [172.20.232.100/16]
              gateway4: 172.20.0.1
              nameservers:
                addresses: [8.8.8.8,8.8.4.4]
          version: 2
> Save lại, chạy lệnh này

        sudo netplan try --state /etc/netplan
> Kiểm tra IP đã thiết lập thành công hay chưa

        ip a

![alt](https://i.imgur.com/nSbAw2u.png)

# Cài đặt Wordpress
**1. Cài đặt dịch vụ Nginx**

        sudo -i

        apt update

        apt install nginx

> Lên trình duyệt gõ IP máy để kiểm tra

![alt](https://i.imgur.com/4uae3bC.png)

**2.  Cài đặt dịch vụ MySQL**

        apt install mysql-server


**3. Tạo database mẫu**

        mysql -u root -p

        ALTER USER 'root'@'localhost' IDENTIFIED WITH MYSQL_NATIVE_PASSWORD BY 'Knn2022@';

        CREATE DATABASE my_db;

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

**8. Copy file wp-config-sample.php ra file wp-cpnfig.php**

        cp wp-config-sample.php wp-config.php

**9. Chỉnh sửa file wp-config.php**

        nano wp-config.php

![alt](https://i.imgur.com/QtTotzj.png)

**10. Cấp quyền 777 cho thư mục Wordpress**

        chmod -R 777 ./*

**11. Cài các gói PHP cần thiết**

        add-apt-repository ppa:ondrej/php

        apt install php7.4-fpm php7.4-mysql

**12. Cấu hình Nginx**

        nano /etc/nginx/sites-available/default

![alt](https://i.imgur.com/jp2oz7C.png)

**13. Khởi động lại Nginx**

        service nginx restart

**14. Gõ IP máy lên kiểm tra**

![alt](https://i.imgur.com/mxv2CQY.png)

# Cấu hình Chứng chỉ SSL

**1. Tạo thư mục ssl**

        mkdir /etc/nginx/ssl

**2. Tạo file .crt và file .key**

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/key.key -out /etc/nginx/ssl/cert.crt

**3. Cấp quyền 777**

        chmod 700 /etc/nginx/ssl/*

**4. Cấu hình lại Nginx**

        nano /etc/nginx/sites-available/default

![alt](https://i.imgur.com/YzVUFRf.png)


**5. Khởi động lại Nginx**

        service nginx restart

**6. Gõ https://<IP máy> lên kiểm tra**

![alt](https://i.imgur.com/J2HIvwr.png)


