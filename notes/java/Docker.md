# Docker Commands:

-   `docker version`
-   `docker info`
-   `docker run hello-world` First it will search in local, pulls image from dockeer hub and create and runs container.
-   `docker pull ubuntu` Pulls latest tag image from hub.
-   `docker pull ubuntu:18.04` Pulls 18.04 tag from hub.
-   `docker build -t my-httpd .` Building an image <br>
    Syntax: docker build -t image-name location-of-dockerfile
-   `docker images` List all the images<br>
-   `docker history tomcat` Shows history of the image.
-   `docker inspect <image-id>` Inspect the Image (Display's image info in json)<br>
-   `docker inspect <container-id>` Inspect the container and image
-   `docker rm <container-id>` Remove a specific container
-   `docker rm $(docker ps -a -q)` Remove all containers -q is to get only container id's
-   `docker rmi <image-id>` remove image (note: no containers for this image should be running)
-   `docker rmi $(docker images -q)` remove all images
-   `docker run -it --name testubuntu1 ubuntu` -it for Interactive terminal
-   `docker run -itd --name testubuntu1 ubuntu` it's necessary to include -it even you -d, When the ENTRYPOINT is bash or sh
    docker run -d ubuntu:14.04 will immediately stop, cause bash can't find any pseudo terminal to be allocated. You have to specify -it so that bash or sh can be allocated to a pseudo terminal.
-   `docker ps` Shows all the containers which are running
-   `docker ps -a` Shows all the containers stopped and running
-   `docker logs <container-id>` View the logs of the container
-   `docker logs -ft <container-id>` Tail the logs of the container
-   `docker stop <container-id>` Stop the container
-   `docker start <container-id>` Stop the container
-   `docker exec -it <container-id> /bin/bash` Logging into the container, Note: The container must be started before we can do this.
-   `docker kill <container name>`
-   `docker commit <container-id>` To convert container to image. docker run for image to container.
-   `docker tag <image-id> tagname` to give another name to image.
-   `docker attach imagename` Attach local standard input, output, and error streams to a running container
Docker attach attaches to the process that runs in the container with PID 1. Where as Docker exec is used run a new process inside the container
-   `docker pull`
-   `docker push`

Running a container from the image
Syntax: docker run -itd --name <container-name> -p <host-port>:<port in container> image-name:tag
note in the above syntax:
-d : represents (detached mode), note that if you dont run this in detached mode, the life of the container will be the life of the terminal in which you are executing it.
-p : represents the host-port to container-port mapping, if you substitute it with -P you will get a random port allocated by docker
--name : represents the name of the container 

docker run -itd --name my-http-container-1 -p 5555:80 my-httpd:latest
docker run -itd --name my-http-container-2 -p 5556:80 my-httpd:latest
docker run -itd --name my-http-container-with-random-port -P my-httpd:latest

#note if you run this it will NOT run in detached mode.
docker run -it --name my-http-container-not-detached-mode -p 5557:80 my-httpd:latest

Step 5: View all the containers
-------------------------------------------------------------------



Step 7: View Container logs
-------------------------------------------------------------------





Step 11: Remove all the containers and images
-------------------------------------------------------------------
#Note: To remove an image the corresponding container built from that image will need to be removed.


Step 6: Logging into Dockerhub account on the terminal
-------------------------------------------------------------------
docker login 
docker tag <currentimage>:<tag> <repository-name>/<image-name>:<tag>
docker push <repository-name>/<image-name>:<tag>
docker commit <container-id> <repository-name>/<image-name>:<tag>

docker pull <repository-name>/<image-name>:<tag>


*****************************************************************
Docker - machine: Windows and MAC
*****************************************************************

#Create 2 Docker machines (Remember both are virtual PCs running with different IPs)
------------------------------------------------------------------------

Windows: docker-machine create -d hyperv --hyperv-virtual-switch "vs-1" hyperv-vm-1
MAC: docker-machine create --driver virtualbox --virtualbox-disk-size "20000" hyperv-vm-1

Windows: docker-machine create -d hyperv --hyperv-virtual-switch "vs-1" hyperv-vm-2
MAC: docker-machine create --driver virtualbox --virtualbox-disk-size "20000" hyperv-vm-2

#Verify if docker-machine is running on an IP
------------------------------------------------------------------------
docker-machine ip hyperv-vm-1
docker-machine ip hyperv-vm-2

#Run the following command and whatever output you get run it again
------------------------------------------------------------------------
docker-machine env hyperv-vm-1
docker-machine env hyperv-vm-2

#Run the output of the last line of the command which is there before.
------------------------------------------------------------------------
 & "C:\Program Files\Docker\Docker\Resources\bin\docker-machine.exe" env hyperv-vm-1 | Invoke-Expression
 & "C:\Program Files\Docker\Docker\Resources\bin\docker-machine.exe" env hyperv-vm-2 | Invoke-Expression

#Verify the docker-machine is active.
----------------------------------------
docker-machine ls

Turning OFF and ON docker-machine
-------------------------------------
docker-machine stop hyperv-vm-1
docker-machine stop hyperv-vm-2
docker-machine start hyperv-vm-1
docker-machine start hyperv-vm-2

Remove docker-machine (Turn off docker-machine on hyper-v manager for windows before executing this command, for MAC no extra steps are required)
docker-machine rm -f <name-of-machine>

*****************************************************************
Docker - compose commands to follow (Common for MAC and Windows)
Manual Reference: https://docs.docker.com/compose/reference/
*****************************************************************
Step-1 # Builds the image
-------------------------------------------------------------------
docker-compose build

#Build a specific image
docker-compose build <service-name>

#Builds and run the containers
docker-compose up --build -d

Step-2 # View the image (All started and stopped containers)
-------------------------------------------------------------------
docker-compose ps 


Step-3 # Run the image in detached/non-detached mode (use -d) 
-------------------------------------------------------------------
docker-compose up -d
docker-compose up
docker-compose up -d <service-name>


Step-4 # View logs and Tail logs
-------------------------------------------------------------------
# View logs
docker-compose logs <service-name>

# Tail logs
docker-compose  logs -ft <service-name>

Step-5 # Login to the container
-------------------------------------------------------------------
docker-compose run <service-name> /bin/bash 

Step-6 # Stops all containers related to the compose file
--------------------------------------------------
docker-compose stop

#Stop and start specific container
docker-compose stop <service-name>

#Stop and remove containers
docker-compose down

1. Pull MySql image from Docker Hub (https://hub.docker.com/_/mysql/)
`docker pull mysql:5.7`

2. Create MySql container from docker image.
`docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test -e MYSQL_USER=muni -e MYSQL_PASSWORD=root -d mysql:5.7`

3. In the Spring Boot Application, use the same container name of the mysql instance in the application.properties
`spring.datasource.url = jdbc:mysql://mysql-container:3306/test`

4. Using the Dockerfile create the Docker image.
From the directory of Dockerfile - `docker build . -t spring-boot-jpa-hibernate-docker`

5. Run the Docker image (spring-boot-jpa-hibernate-docker) created in #4.
`docker run -p 8086:8086 --name spring-boot-jpa-hibernate-docker --link mysql-container:mysql -d spring-boot-jpa-hibernate-docker`

# Pulling Image from Docker hub

1. Follow above steps 1 to 3.

2. Pull spring-boot-jpa-hibernate-docker project image from Docker Hub (https://hub.docker.com/r/muniswamy/springbootrep/)
`docker pull muniswamy/springbootrep`

3. Run the Docker image (spring-boot-jpa-hibernate-docker) created in #4.
`docker run -p 8086:8086 --name spring-boot-jpa-hibernate-docker --link mysql-container:mysql -d muniswamy/springbootrep`

# Sample Request

{
"id":1,
"name":"muni",
"salary":200
}

## Useful Docker commands
- `docker images`
- `docker container ls`
- `docker logs <container_name>`
- `docker container rm <container_name`
- `docker image rm <image_name`
