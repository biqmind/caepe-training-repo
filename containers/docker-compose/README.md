# Container Basics - Docker Compose

### This lab will cover the following topics:

* [Docker Compose](https://docs.docker.com/compose/)

<br/>
<br/>

---
---

## Lab 1 - Running Wordpress using CLI commands

This lab highlights the benefit of using Docker Compose over CLI commands to manage containers or groups of containers.

<br/>

---

<br/>

### Create Docker Networks

Create the `database_net` network:<br/>
`docker network create database_net` {{ execute }}

Create the `external_net` network:<br/>
`docker network create external_net` {{ execute }}

<br/>

---

<br/>

### Create a MariaDB Database Container

Create a MariaDB database container which attaches to the `database_net` network:<br/>
`docker run -itd -e MARIADB_ROOT_USER=wordpress -e MARIADB_ROOT_PASSWORD=yourpassword -e MARIADB_DATABASE=wordpress --name mariadb -v mariadb_data:/var/lib/mysql --net database_net docker.io/bitnami/mariadb:10.3` {{ execute }}

<br/>

---

<br/>

### Create a Wordpress Container

Create a Wordpress container which attaches to the `external_net` network:<br/>
`docker run -itd -e WORDPRESS_DATABASE_HOST=mariadb -e WORDPRESS_DATABASE_PORT_NUMBER=3306 -e WORDPRESS_DATABASE_USER=wordpress -e WORDPRESS_DATABASE_PASSWORD=yourpassword -e WORDPRESS_DATABASE_NAME=wordpress -e WORDPRESS_BLOG_NAME=AB_Training_Docker_CLI --name wordpress -p 8080:8080 -p 8443:8443 -v wordpress_data:/var/www/html --net external_net docker.io/bitnami/wordpress:5` {{ execute }}

<br/>

---

<br/>

### Connect the Wordpress Container to the Database Network

Finally, attach the Wordpress container to the `database_net` network so it can communicate with the MariaDB container:<br/>
`docker network connect database_net wordpress` {{ execute }}

<br/>

---

<br/>

### Visit the Wordpress Site

Visit http://8080.LABSERVERNAME and verify the default Wordpress page up and running.

> **NOTE:**<br/>The Wordpress site will take a few moments to start. If an error is received, wait a few more seconds and try again.

<br/>

---

<br/>

### Review the Container Logs

Review the `mariadb` logs:<br/>
`docker logs mariadb` {{ execute }}

Review the `wordpress` logs:<br/>
`docker logs wordpress` {{ execute }}

<br/>

---

<br/>

### Container Cleanup

Clean up these containers, volumes and networks:<br/>
`docker rm -f wordpress mariadb && docker volume rm mariadb_data wordpress_data && docker network rm database_net external_net` {{ execute }}

<br/>
<br/>

---
---

## Lab 2 - Running Wordpress using docker-compose

Docker Compose simplifies things quite a bit while also allowing users define declarative configuration and generate an artifacts that could be put into source code control like Git.

<br/>

---

<br/>

### Review the Docker Compose File

Review the `docker-compose.yml` file in the directory shown below:<br/>
`/ab/labs/containers/docker-compose/docker-compose.yml` {{ open }}

> **NOTE:**<br/>The Docker Compose file contains all of the elements defined in the Docker CLI, but it is much easier to read and modify.

<br/>

---

<br/>

### Execute the Docker Compose File 

Run this docker-compose against this file. Switch to the directory where the `docker-compose.yml` file is located:<br/>
`cd /ab/labs/containers/docker-compose/` {{ execute }}

> **NOTE:**<br/>By default docker-compose will look for a file named `docker-compose.yml` in the present working directory (PWD) and run it. If the file is named something else, it is possible to specify a `-f filename.yml` for docker-compose to find it.

<br/>

---

<br/>

Bring the containers up using docker-compose:<br/>
`docker-compose up -d` {{ execute }}

> **NOTE:**<br/>The `-d` flag tells docker-compose to run the containers in the background.

<br/>

---

<br/>


### Visit the Wordpress Site

In a few seconds, visit http://8080.LABSERVERNAME and see that Wordpress is up and running.

> **NOTE:**<br/>Wordpress is a complex application and will take a few moments to start. If an error is received, wait a few more seconds and try again.

<br/>

---

<br/>

### Review Docker Compose Logs

To see the logs for the currently running docker-compose deployment, from the same directory as the `docker-compose.yml` file, run:<br/>
`docker-compose logs` {{ execute }}

> **NOTE:**<br/>Logs from both containers in the file are integrated into a single log output with labels for which container produced the log.

<br/>

---

<br/>

### Take Down the Docker Compose Deployment

To cleanup all containers, volumes, and networks Docker Compose just created, run:<br/>
`docker-compose down -v` {{ execute }}

<br/>
<br/>

---
---
**Congrats! You have completed the Docker-Compose lab.**
