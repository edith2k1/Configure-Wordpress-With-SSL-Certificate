# Content

- [How To Create Wordpress Site On Ubuntu Server ?](#wp)

- [How To Install An SSL Certificate On Your WordPress Site ?](#ssl)

***

## How To Create Wordpress Site On Ubuntu Server ? <a id="wp"></a>

**1. Install Nginx Service**

    sudo apt-get install nginx -y

**2. Install MySQL Service**

    sudo apt-get install mysql-server -y

**3. Create Base Database**

    mysql -u root -p

    CREATE DATABASE my_db;
        
    CREATE USER 'db_user'@'localhost' IDENTIFIED BY '123456';

    GRANT ALL PRIVILEGES ON * . * TO 'db_user'@'localhost';

    FLUSH PRIVILEGES;

    EXIT

**4. Download Wordpress Source**

    curl -O https://wordpress.org/latest.tar.gz

**5. Extract**

    tar xzvf latest.tar.gz

**6. Copy Wordpress Source to /var/www**

    cp -r wordpress/ /var/www/

**7. Go to Wordpress Folder**

    cd /var/www/wordpress

**8. Create wp-config.php File From wp-config-sample.php File**

    cp wp-config-sample.php wp-config.php

**9. Configure wp-config.php File**

    vi wp-config.php

> Copy this to the file

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

**10. Grant Permission 777 To Wordpress Folder**

    chmod -R 777 ./*

**11. Install Some PHP Packages To Run Wordpress Source**

    add-apt-repository ppa:ondrej/php

    apt install php7.4-fpm php7.4-mysql

**12. Configure Nginx**

    vi /etc/nginx/sites-available/default

> Copy this to the file

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

**13. Restart Nginx Service**

    service nginx restart

***

## How To Install An SSL Certificate On Your WordPress Site ? <a id="ssl"></a>

**1. Create ssl Folder**

    mkdir /etc/nginx/ssl

**2. Create .crt and .key File**

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/key.key -out /etc/nginx/ssl/cert.crt

**3. Grant Permission 777 To ssl Folder**

    chmod 777 /etc/nginx/ssl/*

**4. Reconfigure Nginx**

    nano /etc/nginx/sites-available/default

> Copy this to the file

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

**5. Restart Nginx Service**

    service nginx restart
