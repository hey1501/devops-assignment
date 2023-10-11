Dockerize a WordPress application with Dockefile and Docker-compose file and Database Optimization 
Wordpress :
WordPress is a free and open-source content management system written in PHP and paired with a MySQL or MariaDB database. Features include a plugin architecture and a template system, referred to within WordPress as Theme.
Dockerizing a WordPress application might be helpful for testing or developing new themes, and plugins. In this article we will make the complete ready to use setup.

1 .Prerequisite : 
   1. Docker
   2. Docker Compose

   Setup development environment :

1. Download wordpress zip file from official website .
2. Unzip the wordpress file
3. Now let’s see what containers we need to build the environment :
    1.Ubuntu with Apache server installed
    2.Mysqlserver
4. Here is our project structure :
    1. ---wordpress/
    2. ---- Dockerfile
    3. --- config/wdpress.conf
    4. ----docker-compose.yml
    5. --- .env


5 .Lets Make the Dockerfile :
FROM ubuntu:18.04
RUN \
  sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && \
  apt-get update && \
  apt-get -y upgrade && \
  apt-get install -y build-essential && \
  apt-get install -y software-properties-common && \
  apt-get install -y byobu curl git htop man unzip vim wget && \
  rm -rf /var/lib/apt/lists/*
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update
RUN apt-get upgrade
RUN apt-get install -y apache2 libapache2-mod-php
RUN apt install -y php unzip
RUN apt-get install -y php-cli php-common php-mbstring php-gd php-intl php-xml php-mysql php-zip php-curl php-xmlrpc
COPY . /var/www/html:rw
COPY ./config/wdpress.conf /etc/apache2/sites-available/000-default.conf
RUN a2enmod rewrite
# Define working directory.
WORKDIR /var/www/html
EXPOSE 80
CMD apachectl -D FOREGROUND


6 . Setup apache configuration file /config/wdpress.conf
 <VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/html
	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
    <Directory /var/www/html >
        AllowOverride All
    </Directory>
</VirtualHost>

7. Run the command to build Dockerfile
   docker build -t wp:1 .

8. Define our services in Docker-compose.yml
   version: '2'
services:
 
  mysql:
    image: mysql:5.7
    env_file:
      - .env
    volumes:
      - ./.docker/data/mysql/:/var/lib/mysql
      - ./.docker/logs/mysql/:/var/log/mysql
    ports:
      - "3306:3306"
    container_name: wp_mysql
  
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      -  8080:80
    env_file:
      - .env
    environment:
      PMA_HOST: mysql
      VIRTUAL_HOST: phpmyadmin.wp.local  
    container_name: wp_phpmyadmin
  app_dev:
    container_name: wp_app
    build: ./wordpress/
    environment:
      - VIRTUAL_HOST=app.wp.local
    volumes : 
      - ./wordpress:/var/www/html:rw
    restart: always
    ports:
      - 80:80
    links:
      - "mysql:wp_mysql"

8. Setup the environment variable for the database connection
      MYSQL_ROOT_PASSWORD: wdpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
9. Run this command to build docker compose file
    docker-compose up --build

10.Once the build is done successfully and all containers are up visit :
    18.191.71.240 ------> wordpress application 
    18.191.71.240:8080 ---> phpmyadmin





    
