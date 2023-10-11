NAME : Abhimanyu kumar singh
Email id. - singhabhimanyukumar591@gmail.com

Dockerize a WordPress application with Dockefile and Docker-compose file and Database Optimization 
Wordpress :
WordPress is a free and open-source content management system written in PHP and paired with a MySQL or MariaDB database. Features include a plugin architecture and a template system, referred to within WordPress as Theme.
Dockerizing a WordPress application might be helpful for testing or developing new themes, and plugins. In this article we will make the complete ready to use setup.

1 .Prerequisite : 
   1. Docker
   2. Docker Compose

   Setup development environment :
1. Now let’s see what containers we need to build the environment :
    1.wordpress with Apache server installed
    2.Mysqlserver 
2. Here is our project structure :
    1. ---- Dockerfile
    2. ----docker-compose.yml
   


3 .Lets Make the Dockerfile :
FROM wordpress:latest

LABEL maintainer="abhi15@gmail.com"

ARG DB_NAME=mywordpressdb
ARG DB_USER=mywordpressuser
ARG DB_PASSWORD=mysecretpassword
ARG DB_HOST=db

ENV WORDPRESS_DB_NAME=${DB_NAME}
ENV WORDPRESS_DB_USER=${DB_USER}
ENV WORDPRESS_DB_PASSWORD=${DB_PASSWORD}
ENV WORDPRESS_DB_HOST=${DB_HOST}

RUN apt-get update \
    && apt-get install -y \
        # Additional packages if needed \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

CMD ["apache2-foreground"]



4. Run the command to build Dockerfile
   docker build -t wp:1 .

5. Define our services in Docker-compose.yml
   version: '3'
services:
  wordpress:
    build: .
    ports:
      - "80:80"
    environment:
      MYSQL_DATABASE: mywordpressdb
      MYSQL_USER: mywordpressuser
      MYSQL_PASSWORD: mysecretpassword
      MYSQL_HOST: db
    volumes:
      - wordpress_data:/var/www/html

  db:
    image: mysql:latest
    environment:
      MYSQL_DATABASE: mywordpressdb
      MYSQL_USER: mywordpressuser
      MYSQL_PASSWORD: mysecretpassword
      MYSQL_ROOT_PASSWORD: myrootpassword
    volumes:
      - db_data:/var/lib/mysql

volumes:
  wordpress_data:
  db_data:

6. Run this command to build docker compose file
    docker-compose up --build
7. Go to the browser :
   http://18.188.0.66:80
   

    





    
