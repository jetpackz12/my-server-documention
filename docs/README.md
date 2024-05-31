# Linux Server Installation ( Ubuntu 22.04 )

## Nginx 
Below commands will install nginx. [LINK](https://phoenixnap.com/kb/install-nginx-ubuntu-22-04) - Just disabled the ufw no need to follow all proccess on this link.
```bash
sudo apt update
sudo apt install nginx
sudo systemctl status nginx
```

## PHP
Below commands will install PHP. [LINK](https://shahriar-ratul.medium.com/install-php-8-2-on-ubuntu-22with-nginx-5902fcc1142b)
```bash
sudo apt update
sudo apt install software-properties-common

# If this is not working use below alternative.
sudo add-apt-repository ppa:ondrej/php
sudo apt update

# If the above is not working use this as an alternative.
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E5267A6C
sudo sh -c 'echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu $(lsb_release -cs) main" > /etc/apt/sources.list.d/ondrej-php.list'
sudo apt-get update
# End

sudo apt install php8.2-fpm
php-fpm8.2 -v
sudo apt install php8.2-common php8.2-mysql php8.2-xml php8.2-xmlrpc php8.2-curl php8.2-gd php8.2-imagick php8.2-cli php8.2-dev php8.2-imap php8.2-mbstring php8.2-opcache php8.2-soap php8.2-zip php8.2-redis php8.2-intl -y
```

Might as well update the php.ini for better performance.
```bash
sudo nano /etc/php/8.2/fpm/php.ini
```

Find below and update the values press F6 for searching.
```bash
upload_max_filesize = 32M 
post_max_size = 48M 
memory_limit = 256M 
max_execution_time = 600 
max_input_vars = 3000 
max_input_time = 1000
```

Then below will restart php8.2-fpm
```bash
systemctl restart php8.2-fpm
```

## Composer
Below commands will install Composer. [LINK](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-composer-on-ubuntu-22-04#step-2-downloading-and-installing-composer)
```bash
cd ~
curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php
HASH=`curl -sS https://composer.github.io/installer.sig`
echo $HASH
php -r "if (hash_file('SHA384', '/tmp/composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

## MariaDB
Below commands will install MariaDB. [LINK](https://www.digitalocean.com/community/tutorials/how-to-install-mariadb-on-ubuntu-22-04#introduction)
```bash
sudo apt update
sudo apt install mariadb-server
sudo mysql_secure_installation
```
Below are for the configuration of the MariaDB.
```bash
cd /etc/mysql/mariadb.conf.d
mv 50-server.cnf 50-server.cnf.save
nano 50-server.cnf
```
Paste below configuration ( This is for 8gb ram ).
```bash
## 50-server.cnf
#
# These groups are read by MariaDB server.
# Use it for options that only the server (but not clients) should see

# this is read by the standalone daemon and embedded servers
[server]

# this is only for the mysqld standalone daemon
[mysqld]

#
# * Basic Settings
#

user                    = mysql
pid-file                = /run/mysqld/mysqld.pid
socket                  = /run/mysqld/mysqld.sock
port                    = 3306
basedir                 = /usr
datadir                 = /var/lib/mysql
tmpdir                  = /tmp
lc-messages-dir         = /usr/share/mysql
skip-external-locking
skip-name-resolve

# Broken reverse DNS slows down connections considerably and name resolve is
# safe to skip if there are no "host by domain name" access grants
#skip-name-resolve

# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0 # to use SSH tunneling change the value to this 127.0.0.1

#
# * Fine Tuning
#

#key_buffer_size         = 256M
key_buffer_size         = 64M
max_allowed_packet      = 64M
connect_timeout         = 10
wait_timeout            = 28880
#thread_stack           = 192K
thread_cache_size       = 128
sort_buffer_size        = 2M
bulk_insert_buffer_size = 64M
tmp_table_size          = 64M
max_heap_table_size     = 64M
# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam_recover_options = BACKUP
myisam_sort_buffer_size = 64M
#max_connections         = 2000
max_connections         = 500
table_open_cache        = 400
concurrent_insert       = 2
#table_cache            = 64
#thread_concurrency     = 10
read_buffer_size        = 1M
read_rnd_buffer_size    = 1M
#
# * Query Cache Configuration
#
query_cache_limit       = 1M
query_cache_size        = 64M

#
# * Logging and Replication
#
log_warnings           = 2
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
# As of 5.1 you can enable the log at runtime!
general_log_file       = /var/log/mysql/mysql.log
general_log            = 0
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Enable the slow query log to see queries with especially long duration
slow_query_log = 1
slow_query_log_file     = /var/log/mysql/mariadb-slow.log
long_query_time = 1
#log_slow_rate_limit    = 1000
log_slow_verbosity     = query_plan
#log-queries-not-using-indexes
log_bin_trust_function_creators = 1
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
#server-id              = 1
#log_bin                = /var/log/mysql/mysql-bin.log
expire_logs_days        = 2
max_binlog_size        = 100M
#binlog_do_db           = include_database_name
#binlog_ignore_db       = exclude_database_name
#
# * SSL/TLS
#

# For documentation, please read
# https://mariadb.com/kb/en/securing-connections-for-client-and-server/
#ssl-ca = /etc/mysql/cacert.pem
#ssl-cert = /etc/mysql/server-cert.pem
#ssl-key = /etc/mysql/server-key.pem
#require-secure-transport = on

#
# * Character sets
#

# MySQL/MariaDB default is Latin1, but in Debian we rather default to the full
# utf8 4-byte character set. See also client.cnf


#
# * InnoDB
#
default_storage_engine  = InnoDB
#innodb_buffer_pool_size = 32G
innodb_buffer_pool_size = 5G
innodb_log_buffer_size  = 16M
innodb_file_per_table   = 1
innodb_open_files       = 2000
innodb_io_capacity      = 400
innodb_flush_method     = O_DIRECT
innodb_lock_wait_timeout = 50
innodb_sort_buffer_size = 128M
innodb_log_files_in_group = 2
innodb_log_file_size = 256M

# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!
# Most important is to give InnoDB 80 % of the system RAM for buffer use:
# https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size
#innodb_buffer_pool_size = 8G


init_connect=SET collation_connection = utf8_general_ci
init_connect=SET NAMES utf8
character-set-server = utf8
collation-server = utf8_general_ci
join_buffer_size = 1M
# this is only for embedded server
[embedded]

# This group is only read by MariaDB servers, not by MySQL.
# If you use the same .cnf file for MySQL and MariaDB,
# you can put MariaDB-only options here
[mariadb]

# This group is only read by MariaDB-10.6 servers.
# If you use the same .cnf file for MariaDB of different versions,
# use this group for options that older servers don't understand
[mariadb-10.6]
```

After pasting the configuration restart mysql.
```bash
systemctl restart mysql
```

Below are for granting user privileges.
```bash
mysql -u root -p
grant all privileges on *.* to 'username'@'%' identified by 'password';
flush privileges;
exit;
```

## NODE
Below commands will install Node. [LINK](https://netshop-isp.com.cy/blog/how-to-install-nodejs-any-version-on-ubuntu-22-04-server/)
```bash
sudo apt update
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install nodejs -y
sudo node -v
```
#
# How to clone a project into the server.
## Public repository
### Cloning the project
This will clone the project.
```bash
git clone https://github.com/sample/sample.git # Please change the url to your project use HTTPS.
```

## Private repository
### SSH-Keygen
This command will genarate ssh-keygen. [LINK](https://www.ssh.com/academy/ssh/keygen)
```bash
ssh-keygen -t rsa -b 4096 # Just enter to all.
cd ~
cat .ssh/id_rsa.pub # Copy the public key into your github.
```

This will clone the project.
```bash
git clone git@github.com:sample/sample.git # Please change the url to your project use SSH.
```

# How to configure vue js project( vite ) in the server.
## Project installation
Steps for project installation and serving the project.
```bash
cd /vue_project/ # Go to the project folder
npm install # Project setup
npm run dev # For development
npm run build # For production
```

## Configure package.json
Update scripts configuration.
```bash
"scripts": {
    "dev": "vite --host",
    "build": "vite build",
    "preview": "vite preview"
  },
```

## Proxy
You might add a proxy for the ipa end point, create .env file in the root folder then paste this in the .env.
```bash
VITE_API_URL=http://back_end_url
```

Update the vite.config.js and paste below.
```bash
import { defineConfig, loadEnv } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig(({ mode }) => {
  const env = loadEnv(mode, process.cwd(), '')

  return {
    plugins: [vue()],
    server: {
      proxy: {
        '/api': {
          target: env.VITE_API_URL,
          changeOrigin: true,
          secure: false,
        }
      }
    }
  }
})
```

After than update all endpoint.
```bash
# Original endpoint
axios
  .get("http://back_end_url/api/index") # Endpoint
  .then((response) => {
    this.json_data = response.data;
  })
  .catch((error) => {
    console.log(error);
  });

# Updated endpoint
axios
  .get("/api/index") # Endpoint
  .then((response) => {
    this.json_data = response.data;
  })
  .catch((error) => {
    console.log(error);
  });
```

## Nginx configuration
Below will create a front_end file.
```bash
cd /etc/nginx/sites-enabled
touch front_end
nano front_end
```

Paste below configuration [LINK](https://medium.com/homullus/vuejs-dev-serve-with-reverse-proxy-cdc3c9756aeb)
```bash
server {
    listen 80;
    listen [::]:80;

    root    /opt/news_app_front/dist;
    index   index.html index.htm;
    
    server_name _; # if there are domain just change '_' to domain
    charset utf-8;

    # Serve index.html for any request except API calls
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Proxy pass API requests to the backend server
    location /api/ {
        proxy_pass http://back_end_url;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    error_log  /var/log/nginx/vue-app-error.log;
    access_log /var/log/nginx/vue-app-access.log;

}
```

After that restart nginx.
```bash
systemctl restart nginx
```


# How to configure laravel project in the server.
## File permissions
Change the below file permission.
```bash
cd /laravel_project/ # Go to the project folder
chmod -R 777 bootstrap/
chmod -R 777 storage/
chmod -R 777 public/
```

## Project installation
Steps for project installation and serving the project.
```bash
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate # If this result error add the db name into the database.
php artisan storage:link # This is optional
php artisan serve --host=10.0.0.224 --port=8080 # change the host value to your current server host.
```

## Nginx configuration
Below will create a back_end file.
```bash
cd /etc/nginx/sites-enabled
touch back_end
nano back_end
```

Paste below configuration [LINK](https://laravel.com/docs/11.x/deployment#nginx)
```bash
server {
    listen 8080;
    listen [::]:8080;
    server_name _; # if there are domain just change '_' to domain
    root /opt/news_app_back/public;
 
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";
 
    index index.php;
 
    charset utf-8;
 
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
 
    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }
 
    error_page 404 /index.php;
 
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_hide_header X-Powered-By;
    }
 
    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

After that restart nginx.
```bash
systemctl restart nginx
```