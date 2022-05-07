Click [here](https://github.com/edith2k1){:target="_blank" rel="noopener"} to go to my Quora profile. 


# Content

- [How To Create Wordpress Site On Ubuntu Server ?](#wp)

- [How To Install An SSL Certificate On Your WordPress Site ?](#ssl)

***

## How To Create Wordpress Site On Ubuntu Server ? <a id="wp"></a>

**1. Install Nginx Service**

    sudo apt-get update ; apt-get install nginx -y

**2. Install MySQL Service**

    sudo apt-get install mysql-server -y

**3. Create Base Database**

    sudo mysql -u root -p

    CREATE DATABASE my_db;
        
    CREATE USER 'db_user'@'localhost' IDENTIFIED BY '123456';

    GRANT ALL PRIVILEGES ON * . * TO 'db_user'@'localhost';

    FLUSH PRIVILEGES;

    EXIT

**4. Download Wordpress Source**

    curl -O https://wordpress.org/latest.tar.gz

**5. Extract**

    tar xzvf latest.tar.gz

**6. Configure wp-config.php File**

    cat > ./wordpress/wp-config.php

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
    
**6. Copy Wordpress Source to /var/www**

    sudo cp -r wordpress/ /var/www/

**8. Grant Permission 777 To Wordpress Folder**

    sudo chmod -R 777 /var/www/wordpress/*

**9. Install Some PHP Packages To Launch Wordpress Source**

    sudo add-apt-repository ppa:ondrej/php

    sudo apt-get install php7.4-fpm php7.4-mysql -y

**10. Configure Nginx**

    sudo vi /etc/nginx/sites-available/default

> Delete all and Copy this to the file

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

    sudo service nginx restart

***

## How To Install An SSL Certificate On Your WordPress Site ? <a id="ssl"></a>

**1. Create ssl Folder**

    sudo mkdir /etc/nginx/ssl

**2. Create .crt and .key File**

    sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/key.key -out /etc/nginx/ssl/cert.crt

**3. Grant Permission 777 To ssl Folder**

    sudo chmod 777 /etc/nginx/ssl/*

**4. Reconfigure Nginx**

    sudo vi /etc/nginx/sites-available/default

> Delete all and Copy this to the file

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

    sudo service nginx restart
