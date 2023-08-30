# Container Basics - Docker Basics

### This lab will cover the following topics:

* Basic Docker Commands
* Docker Images
* Docker Containers
* Docker Run Commands

<br/>
<br/>

---
---

## Lab Environment Setup
1. In this VSCode window, look for the 'burger' menu in the upper left corner of the window. The menu typically appears as three stacked horizontal lines.

2. Navigate through this menu, clicking on `Terminal`, then `New Terminal`. This action will bring up a new terminal window at the bottom of the VSCode environment.

3. All commands mentioned in this guide will be executed within this terminal window. To run these commands, do one of the following:

    * Type the commands in manually. This is the recommended method, as it will help build muscle memory for the commands.

    * Copy and paste the commands from this guide into the terminal window. To copy, highlight the text to copy, then right-click and select Copy. To paste, right-click and select Paste.

    * Click on the `Execute` button next to the command. This will automatically copy and paste the command into the terminal window and execute it.

<br/>
<br/>

---
---

## Lab 1 - Basic Docker Commands

Docker is a container runtime that allows a user to run applications in a container. This lab will cover the basic commands used to interact with Docker.

<br/>

---

<br/>


### View Current Running Containers

To see what containers are running on the current system:<br/>
`docker ps` {{ execute }}

Another way to see what containers are running on the current system:<br/>
`docker container ls` {{ execute }}

<br/>

---

<br/>

### List All Containers

To see all containers on the current system, including stopped containers:<br/>
`docker ps -a` {{ execute }}

Alternatively, to see all containers on the current system including stopped containers:<br/>
`docker container ls -a` {{ execute }}

<br/>

---

<br/>

### List all Container Images

To see all images (read-only container snapshot) on the system:<br/>
`docker image ls` {{ execute }}

<br/>

---

<br/>

### List all Docker Volumes

To see all volumes (persistent storage) on the system:<br/>
`docker volume ls` {{ execute }}

<br/>

---

<br/>


### List all Docker Networks

To list current networks on the system:<br/>
`docker network ls` {{ execute }}

<br/>

---

<br/>

### Complete List of Docker Commands

For a complete list of commands available in Docker, use the `--help` flag:<br/>
`docker --help` {{ execute }}

> **NOTE:**<br/>A complete list of commands can be found at [the Docker command line documentation page](https://docs.docker.com/engine/reference/commandline/).


<br/>
<br/>

---
---

## Lab 2 - Running a Container

This lab will cover the basic commands used to run a container using the information garnered from Lab 1 above. The lab will make use of NGINX, a small web server.

<br/>

---

<br/>

### Pull the NGINX Image

Pull the nginx container from Docker Hub:<br/>
`docker pull nginx:latest` {{ execute }}

> **NOTE:**<br/>Docker Hub and Container Registries will be covered in-depth in the next section of the labs.

<br/>

---

<br/>

### Run the NGINX Container

Now that the image has been pulled, run an nginx container based on that image. This will run an nginx container and serve the default web page.

Run the NGINX container:<br/>
`docker run -it nginx` {{ execute }}

> **NOTE:**<br/>There are two important things to note regarding this command:<br/>1. The user is locked into the process in the terminal window and <br/>2. There is no way to access the web server external to the machine which is running the container.

> **NOTE:**<br/>To escape the container, click on the terminal window, then press `Ctrl+C`.

<br/>

---

<br/>

### Find the Last Created Container

It is possible to find the last created container using the `docker ps` command:<br/>
`docker ps -n 1` {{ execute }}

> **NOTE:**<br/>Using `-n 1` will list the last executed Docker container. It is also possible to use the flag `--last 1` to achieve the same result.

> **NOTE:**<br/>After running the command, the last created nginx container with a random name like `funky_rooster` will be shown. This dynamically generated name was auto assigned to the container because a name was not specified manually.

<br/>

---

<br/>

### Delete the Container

It is possible to delete a container using the `docker rm` command:<br/>
`docker rm funky_rooster`

> **NOTE:**<br/>Replace `funky_rooster` with the name of the container.

<br />
<br />

---
---

## Lab 3 - Additional Run Options

In this section, additional options for running containers will be explored:

1. Define a specific name of our choosing using using the `--name` switch.
2. Define a specific external port to expose the container using the `-p` switch.
3. Run the container in the background using the `-d` switch.

<br/>

---

<br/>

### Run the NGINX Container with a Name, Port, and in the Background

Create a container with the name `nginx-sample`, assign port `8070:80`, and run it in the background using the `-d` switch:<br/>
`docker run -itd --name nginx-sample -p 8070:80 nginx` {{ execute }}

<br/>

---

<br/>

### View the Web Page

Access the Web page at http://8070.LABSERVERNAME in a browser. The following text should be displayed:

**"Welcome to nginx!"**

<br/>

---

<br/>

### Stop the Running Container

Stop the container using the name `nginx-sample`:<br/>
`docker stop nginx-sample` {{ execute }}

Try revisiting the URL, http://8070.LABSERVERNAME.  It should show an error that the site cannot be reached, indicating the container is stopped and no longer accessible.

> **NOTE:**<br/>Due to caching, it may be necessary to click the web browser refresh button if the **"Welcome to nginx!"** page is still showing, as it may be cached.

<br/>

---

<br/>

### Start the Stopped Container

It is possible to start a container that has been stopped using the `docker start` command:<br/>
`docker start nginx-sample` {{ execute }}

> **NOTE:**<br/>It is also possible to start a stopped container using the `restart` command instead of `start`.

<br/>

---

<br/>

### Stop and Remove the Container

Stop and permanently delete the running container:<br/>
`docker rm -f nginx-sample` {{ execute }}

<br/>

---

<br/>

### Confirm the Container has been Deleted

Run the following command to confirm the container has been removed:<br/>
`docker ps` {{ execute }}

<br/>
<br/>

---
---

## Lab 4 - Adding Volumes for Persistence

Until this point, ephemeral storage has been used when deploying containers. Ephemeral storage disappears once the container is deleted and cannot be recovered. This lab covers adding persistent storage to solve this problem.

<br/>

---

<br/>

### Volume Types in Docker

There are 2 types of volumes in Docker. Host Mounts and Docker Volumes.

* **Host/Bind Mount**<br/>
A path on the system where Docker will store file. Indicated by a full path ie: `/srv/storage/nginx:/usr/share/nginx/html`.

* **Docker Volume**<br/>
A path managed completely by Docker. While it is accessible via a path on the host, it is not intended to be accessed that way. Implemented as `nginx1:/usr/share/nginx/html`.

<br/>

---

<br/>

### Using the Bind Mount

The Bind Mount volume show how a custom `index.html` file can be loaded into Nginx. Deploy NGINX using the default `index.html` file:<br/>
`docker run -itd --name nginx-novol -p 8080:80 nginx:latest` {{ execute }}

<br/>

---

<br/>

### Visit the Web Page

Visit http://8080.LABSERVERNAME and verify the following text is shown:

**"Welcome to nginx!"**

<br/>

---

<br/>

### Mount a Docker Volume

Mount a volume using the custom `index.html` in this lab directory:<br/>
`docker run -itd --name nginx-vol -v /ab/labs/containers/docker-basics:/usr/share/nginx/html -p 8081:80 nginx:latest` {{ execute }}

<br/>

---

<br/>

### Visit the Web Page

Visit http://8081.LABSERVERNAME and verify the following text is shown:

**"Welcome to the AlphaBravo Container Bootcamp!"**

> **NOTE:**<br/>This also allows data to persist from a container. Deleting the container leaves the `index.html` file intact.

> **NOTE:**<br/>The `-v` switch format is `-v <host path>:<container path>`. In this case, the host path `/ab/labs/containers/docker-basics` is mounted to the container path `/usr/share/nginx/html`.

<br/>

---

<br/>

### Container Cleanup

Run the following command to cleanup:<br/>
`docker rm -f nginx-novol nginx-vol` {{ execute }}

<br/>
<br/>

---
---

## Lab 5 - Adding Networks

Up till this point, when containers have been created the default bridge network has been used. This is fine for most use cases, but there are times when additional networks are required. In this lab, 2 networks will be created to control container communications.

<br/>

---

<br/>

### Create Two Networks

Create the `external` bridge network:<br/>
`docker network create external` {{ execute }}

Create the `database` bridge network:<br/>
`docker network create database` {{ execute }}

> **NOTE:**<br/>Unless defined, the networks created will be of type `bridge`. For more information on network types, see [the Docker network documentation page](https://docs.docker.com/network/).

<br/>

---

<br/>

### Verify the Networks

Verify the networks have been created successfully:<br/>
`docker network ls` {{ execute }}

> **NOTE:**<br/>If created successfully, the `external` and `database` networks will appear in the network list provided.

> **NOTE:**<br/>Two distinct networks have been created which can be assigned to any containers created. Imagine a scenario where two external-facing containers exist. For security or operational reasons, only one of these containers must establish communication with the database server. In such a situation, a dual-network system can be particularly useful.

<br/>

---

<br/>

### NGINX Frontend

Create an container called `nginx1` which can use the `external` network:<br/>
`docker run -itd --name nginx1 -v nginx1:/usr/share/nginx/html -p 8080:80 --network="external" alphabravoio/ubuntu-nginx:latest` {{ execute }}

> **NOTE:**<br/>This example uses Docker Volumes for each container.

<br/>

---

<br/>

### Connect the NGINX Container to the Database Network

Connect the `nginx1` container to the `database` network:<br/>
`docker network connect database nginx1` {{ execute }}

> **NOTE:**<br/>The `nginx1` container needs to be connected to the `database` network. This will allow the NGINX container to communicate with the database container.

<br/>

---

<br/>

### Verify the Container Networks

Verify if the container is connected to the `external` and `database` networks:<br/>
`docker container inspect nginx1 | jq '.[].NetworkSettings.Networks'` {{ execute }}

> **NOTE:**<br/>The `jq` command is used to format the JSON output from the `docker container inspect` command to make it more readable.

<br/>

---

<br/>

### Apache Frontend

Create a container called `apache1` which can use the `external` network:<br/>
`docker run -itd --name apache1 -v /ab/labs/tmp/apache1:/var/www/html -p 8081:80 --network="external" alphabravoio/ubuntu-apache2:latest` {{ execute }}

<br/>

---

<br/>

### MySQL Backend

Create a container called `mysql1` which can use the `database` network:<br/>
`docker run -itd --name mysql1 -v mysql1:/var/lib/mysql --network="database" -e MYSQL_ROOT_PASSWORD=mysecretpassword alphabravoio/ubuntu-mysql:latest` {{ execute }}

<br/>

---

<br/>

### Verify all Containers are Running

The following containers should be running: `mysql1`, `apache1`, and `nginx1`. Run the following command to confirm that the three containers are running:<br/>
`docker ps -n 3` {{ execute }}

<br/>

---

<br/>

### Verify the Container Web Pages

Confirm that nginx and apache are available externally on the lab server:

* Nginx: http://8080.LABSERVERNAME
* Apache: http://8081.LABSERVERNAME

<br/>
<br/>

---
---

## Lab 5a - Test Container Network Communications

There are now 3 containers running: `mysql1`, `apache1`, and `nginx1`.  This lab will test and verify the network separation between the containers.

<br/>

---

<br/>

### Access the NGINX1 Container

Access the `nginx1` container `Bash` shell:<br/>
`docker exec -it nginx1 /bin/bash` {{ execute }}

<br/>

---

<br/>

### Ping the MYSQL1 Container from the NGINX1 Container

From the command line inside the container, ping `mysql1`:<br/>
`ping -c 4 mysql1` {{ execute }}

> **NOTE:**<br/>The ping test should succeed because both `nginx1` and `mysql1` containers are part of the `database` network.

<br/>

---

<br/>

### Exit the NGINX1 Container

Escape from the `nginx1` container shell:<br/>
`exit` {{ execute }}

<br/>

---

<br/>

### Access the APACHE1 Container

Access the `apache1` container `Bash` shell:<br/>
`docker exec -it apache1 /bin/bash` {{ execute }}

<br/>

---

<br/>

### Ping the MYSQL1 Container from the APACHE1 Container

From the command line inside the container, ping `mysql1`:<br/>
`ping -c 4 mysql1` {{ execute }}

> **NOTE:**<br/>This command will fail because `apache1` is not on the `database` network and therefore cannot reach the `mysql1` container.

<br/>

---

<br/>

### Ping the NGINX1 Container from the APACHE1 Container

From the command line inside the `apache1` container, ping the `nginx1` container:<br/>
`ping -c 4 nginx1` {{ execute }}

> **NOTE:**<br/>This will succeed because the `apache1` and `nginx1` containers share the `external` network.

<br/>

---

<br/>

### Exit the APACHE1 Container

Escape from the `apache1` container shell:<br/>
`exit` {{ execute }}

<br/>

---

<br/>

### Ping Test from the MYSQL1 Container to the NGINX1 Container

Ping the `nginx1` container. This command will succeed:<br/>
`docker exec -it mysql1 ping -c 4 nginx1` {{ execute }}

<br/>

---

<br/>

### Ping Test from the MYSQL1 Container to the APACHE1 Container

Ping the `apache1` container. This command will fail:<br/>
`docker exec -it mysql1 ping -c 4 apache1` {{ execute }}

<br/>
<br/>

---
---

## Lab 5b - One more container volume interaction

When the `apache1` container was deployed, a Bind Mount was used instead of a Docker Volume. As explained in Lab 4, this allows users to easily manipulate files in that mount point. In this case, the custom `index.html` can be modified live.

<br/>

---

<br/>

### Modify the Apache Index File

Run the following from the command line in the terminal to updated the Apache index file:<br/>
`echo "<center><h2>I MODIFIED THE APACHE INDEX FILE VIA DOCKER BIND MOUNT.</h2></center>" | sudo tee /ab/labs/tmp/apache1/index.html` {{ execute }}

<br/>

---

<br/>

### Visit the Web Page

Visit the Apache URL, http://8081.LABSERVERNAME, in a browser. It should present an updated page.

> **NOTE:**<br/>Due to browser caching, it may be necessary to manually refresh the page to see the changes.

<br/>

---

<br/>


### Container Cleanup

Remove the containers:<br/>
`docker rm -f nginx1 mysql1 apache1` {{ execute }}

<br/>

---

<br/>

Because of the persistent storage, notice that even though the containers are gone, the volumes still exist:<br/>
`docker volume ls` {{ execute }}

<br/>

---

<br/>

Remove those volumes:<br/>
`docker volume rm nginx1 mysql1` {{ execute }}

<br/>

---

<br/>

Verify the volumes have been removed:<br/>
`docker volume ls` {{ execute }}

<br/>

---

<br/>

Clean up the networks:<br/>
`docker network rm external database` {{ execute }}

<br/>

---

<br/>

Verify the networks have been removed:<br/>
`docker network ls` {{ execute }}

<br/>
<br/>

---
---

**Congrats! You have completed the Docker Basics lab.**