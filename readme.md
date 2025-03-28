# Dockerize a simple PHP, APACHE MYSQL, PHPMYADMIN  🐋

This guide explains how to setup a docker environment running a simple php page with MySQL, Apache and Phpmyadmin.

## Prerequisites
* Download [docker desktop](https://www.docker.com/products/docker-desktop/)
* Run the docker desktop application
* Create a docker-compose.yml in the root of your project directory

### 1. Add the Server

Create a docker-compose.yml file

```bash
version: '3'

services:
  web:
    build: ./docker/web
    volumes:
      - ./:/var/www/html/
    ports:
      - "80:80"
```

* Version 3 indicates the version of the docker file format you are using
* build: this builds php apache from the docker > web > Dockerfile
* volumes: ./ means the current directory of the repo and /var/www/html will be the target path inside the docker container (meaning fiels from current directory will be shared with docker)
* Ports: port 80 on the host is mapped to port 80 on the container

### 2. Add the database

```bash
  db:
    image: mysql:latest 
    volumes:
      - db:/var/lib/mysql/
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: blog
      MYSQL_USER: admin
      MYSQL_PASSWORD: password
```

* Add the necessary credentials

```
volumes:
    db:
```
* This allows docker to persist data when container restarts

### 3. Add the phpmyadmin (optional)

```bash
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8001:80"
    environment:
      PMA_HOST: db
      PMA_PORT: 3306
```

### 4. Dockerfile

Now create a folder called "docker" and add a Dockerfile without extensions

```bash
FROM php:apache
RUN docker-php-ext-install mysqli
```
* This installs the mysqli extension

### 5. Add your test php script

```bash
<?php
$servername = "db";
$username = "admin";
$password = "password";
$dbname = "blog";
// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);
// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
echo "Connected successfully";
// Close the connection
$conn->close();
?>
```

### 6. Running the application


To power on
```docker-compose up --build -d```

* -d means detached

To power off
```docker-compose down```

To visit the site, go to [localhost](http://localhost/)

To go to phpmyadmin, [localhost](http://localhost:8001/)



Source
https://www.youtube.com/watch?v=_UEZX-dA-O4