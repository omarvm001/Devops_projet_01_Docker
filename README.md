

# Project Description
   This repo is a simple application to list student with a webserver (PHP) and API (Flask).
   
   Please find the spec [here](https://github.com/diranetafen/student-list.git "here").

I had to deploy an application named **"student_list"**, which is a basic app that enables **POZOS** to display a list of students with their ages.

The **student_list** application consists of two modules:

1. **Module 1: REST API**
   - This module provides a REST API with basic authentication.
   - It returns the desired list of students based on a JSON file.
   
2. **Module 2: Web Application (HTML + PHP)**
   - This module is a web app written in **HTML** and **PHP**.
   - It allows end-users to view the list of students through a simple interface.


![project](https://user-images.githubusercontent.com/18481009/84582395-ba230b00-adeb-11ea-9453-22ed1be7e268.jpg)

## Table of Contents
   [Project Description](#Project--Description)
1. [Build & Test with Docker](#build--test-with-docker)
   - [Create a Dockerfile](#create-a-dockerfile)
   - [Build the Docker Image](#build-the-docker-image)
   - [Create a Docker Network](#create-a-docker-network)
   - [Run the Back-End API Container](#run-the-back-end-api-container)
   - [Update the API URL](#update-the-api-url)
   - [Run the Front-End Container](#run-the-front-end-container)
   - [Execute a Request Inside the Container](#execute-a-request-inside-the-container)
   - [Test the Student List Application](#test-the-student-list-application)
2. [Infrastructure as Code](#infrastructure-as-code)
   - [Lancer l'application (Front-end & Back-end) automatiquement avec docker-compose](#lancer-lapplication-front-end--back-end-automatiquement-avec-docker-compose)
3. [Docker Registry](#docker--registry)
   - [Créer un Docker Registry & Interface Web](#créer-un-docker-registry--interface-web)
   - [Démarrer les services avec Docker Compose](#démarrer-les-services-avec-docker-compose)
   - [Se connecter au Registry privé](#se-connecter-au-registry-privé)
   - [Pousser une image vers le Registry privé](#pousser-une-image-vers-le-registry-privé)

   



## Build & test with Docker

In this first part of project, we will build and test a student list application using Docker to containerize both the back-end API and the front-end web interface. The process involves creating a Dockerfile to define the back-end environment, building the necessary Docker images, and setting up a Docker network to enable communication between the containers. We will then run the back-end API and front-end containers, configure the API connection, and test the interaction between the two components to ensure the application works as expected. This guide will walk through each of these steps to successfully deploy and test the application.

PS : Before Start !

- The `docker images` command lists all Docker images available on your system. It displays details such as the **repository**, **tag**, **image ID**, **creation date**, and **size** of each image.

```bash
docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```
- The `docker ps` command lists all running Docker containers. It displays key information such as the **container ID**, **image**, **command**, **creation time**, **status**, **ports**, and **names** of the running containers.

```bash
docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

![Mon image](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/Screenshot%20from%202024-09-22%2020-37-29.png)
### Instructions:

1. **Create a Dockerfile**:
      Ensure that you have a `Dockerfile` in your back-end project directory. This file should contain the necessary instructions to build your API image.

   ```dockerfile
   # Use the Python 3.8 base image with Debian Buster
   FROM python:3.8-buster

   # Add a label to specify the author of the image
   LABEL org.opencontainers.image.authors="Omar Bouchenak omar.vm.001@gmail.com"

   # Copy the source code of the API in the container at the root 
   COPY ./student_age.py /

   # Copy requirements.txt file into the root directory of the container
   COPY requirements.txt /

   # Update packages and install necessary dependencies
   RUN apt update -y && apt install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y

   # Install required packages
   RUN pip3 install -r /requirements.txt

   # Creation of the persistent data folder
   VOLUME [ "/data" ]

   # Expose port 5000 to allow incoming connections
   EXPOSE 5000

   # Define the default command to execute when the container starts
   CMD [ "python3", "student_age.py" ]
   ```
   
2. **Build the Docker image**:
   To build the Docker image for the project, run the following command in your terminal:

   ```bash
   docker build . -t back-end.student_list.img
   ```
   **Explanation of the Command**
   - `docker build .`              : This tells Docker to build an image using the Dockerfile in the current directory (.).
   - `t back-end.student_list.img` : This tags the resulting image with the name back-end.student_list.img.
   - `-t `                         : The flag allows you to name and optionally tag an image.

   **Output of the Command**
   
      When you run the command, you should see output similar to this:
      ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/build_image.png)
   ```bash
      [+] Building 0.9s (10/10) FINISHED
    => [internal] load build definition from Dockerfile                                        0.0s
    => => transferring dockerfile: 1.02kB                                                  0.0s
    => [internal] load metadata for docker.io/library/python:3.8-buster                     0.8s
    => [internal] load .dockerignore                                                        0.0s
    => => transferring context: 2B                                                           0.0s
    => [1/5] FROM docker.io/library/python:3.8-buster@sha256:04c3f641c2254c229fd2f704c5199ff4bea57d26c1c29008ae3a4afddde98709  0.0s
    => [internal] load build context                                                        0.0s
    => => transferring context: 1.96kB                                                      0.0s
    => CACHED [2/5] COPY ./student_age.py /                                                  0.0s
    => CACHED [3/5] COPY requirements.txt /                                                  0.0s
    => CACHED [4/5] RUN apt update -y && apt install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y  0.0s
    => CACHED [5/5] RUN pip3 install -r /requirements.txt                                   0.0s
    => exporting to image                                                                    0.0s
    => => exporting layers                                                                   0.0s
    => => writing image sha256:e3c290b12840eb87a46a92f6be7dc443e71228d0d72f9dd12ede356e4be034d5  0.0s
    => => naming to docker.io/library/back-end.student_list.img
   ```
   ```bash
   docker images                              
   REPOSITORY                  TAG       IMAGE ID       CREATED        SIZE
   back-end.student_list.img   latest    e3c290b12840   23 hours ago   1.13GB
   ```
   
3. **Creating a Docker Network**:
   To create a Docker network for your project, use the following command:
   
   ```bash
   docker network create student_list.network --driver=bridge
   ```
   **Explanation of the Command**
   - ` docker network create `: This command is used to create a new Docker network.
   - ` student_list.network ` : This is the name of the network you are creating. You can choose any name you prefer.
   - ` --driver=bridge `      : This specifies the type of network driver to use. The bridge driver is the default and creates a private internal network on your host, allowing containers to communicate with each other.

   **Output of the Command**  
   When you run the command, you should see output similar to this:
   ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/network.png)
   ```bash
   docker network create student_list.network --driver=bridge
   e85ed20d322b3add827787425c4a152419633ae518a8c7ae023105b3b5c196b9
   ```

   ```bash
   docker network ls
   NETWORK ID     NAME                   DRIVER    SCOPE
   7704155ef39e   bridge                 bridge    local
   9b56d6faa424   host                   host      local
   c9286cad696b   none                   null      local
   e85ed20d322b   student_list.network   bridge    local

   ```
This network will be used to enable communication between the front-end and back-end (the 2 modules of our API).

   
4. **Running the back-end API container**:
   To run the back-end API container, use the following command:
   
   ```bash
   docker run --rm -d --name=back-end.student_list --network=student_list.network -v ./simple_api/:/data/ back-end.student_list.img
   ```
   **Explanation of the Command**
   - `docker run`: This command is used to create and start a new container from a specified image.
   - `--rm`: Automatically removes the container when it exits, keeping your environment clean.
   - `-d`: Runs the container in detached mode (in the background).
   - `--name=back-end.student_list`: Assigns a specific name to the container for easier management.
   - `--network=student_list.network`: Specifies the network the container will join, allowing it to communicate with other containers on the same network.
   - `-v ./simple_api/:/data/`: Mounts a volume, mapping the local directory `./simple_api/` to the container's `/data/` directory, enabling data sharing between the host and the container.
   - `back-end.student_list.img`: The name of the image from which the container is created.
   
   **Output of the Command**
         When you run the command, you should see output similar to this:
   ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/FE_BE.png)
   ```bash
   docker run --rm -d --name=back-end.student_list --network=student_list.network -v ./simple_api/:/data/ back-end.student_list.img
   b0c21a17d5679fb06c4a544374e474bdbdbed232ff04d3bbfa4f831615c996a9
   
   ```

   ```bash
   docker ps
   CONTAINER ID   IMAGE                       COMMAND                  CREATED          STATUS          PORTS      NAMES
   b0c21a17d567   back-end.student_list.img   "python ./student_ag…"   19 seconds ago   Up 18 seconds   5000/tcp   back-end.student_list

   ```

5. **Update the API URL**:
   To run the Webapp, you need first to change in the `index.php` file : `<api_ip_or_name:port>`
   
   ```php
   $url = 'http://<api_ip_or_name:port>/pozos/api/v1.0/get_student_ages';
   ```
   In my case :
   
   ```php
   $url = 'http://api.student_list:5000/pozos/api/v1.0/get_student_ages';
   ```
   
   **Explanation of the Command**
   - The variable `$url` in the `index.php` file is used to define the endpoint of the API that retrieves student ages. Here's a detailed explanation of its purpose:
      
         . API Endpoint:
         The URL specifies the location of the API you want to query. In the code, it points to the path `/pozos/api/v1.0/get_student_ages`, which is designated          for fetching the ages of students.
         
         . Dynamic Placeholder:
         The portion `<api_ip_or_name:port>` serves as a placeholder that you need to replace with the actual IP address or hostname of your API server, along            with the port on which the API is listening. This allows your code to connect to the correct API instance.
         
         . Making the API Call:
         By using this URL in the `file_get_contents` function, you initiate an HTTP GET request to the API to fetch data. The API is expected to respond with a          structured data format (usually JSON) containing information about the students.
      
6. **Run the Front-end Container**:
   To run the frontend webapp container, use the following command :
   
   ```bash
   docker run --rm -d --name=front-end.student_list -p 80:80 --network=student_list.network -v ./website/:/var/www/html -e USERNAME=toto -e PASSWORD=python php:apache
   ```
   
   **Explanation of the Command**

   - `docker run`: This command is used to create and start a new container from a specified image.
   
   - `--rm`: Automatically removes the container when it exits, helping to keep your environment clean.
   
   - `-d`: Runs the container in detached mode, meaning it runs in the background.
   
   - `--name=front-end.student_list`: Assigns a specific name to the container for easier management.
   
   - `-p 80:80`: Maps port 80 on the host to port 80 in the container, allowing access to the web application via the host's IP address.
   
   - `--network=student_list.network`: Specifies the Docker network that the container will join, enabling communication with other containers on the same network.
   
   - `-v ./website/:/var/www/html`: Mounts a volume that maps the local `./website/` directory to the container's `/var/www/html` directory. This allows the web server to serve files from the local directory.
   
   - `-e USERNAME=toto`: Sets an environment variable `USERNAME` within the container, which can be accessed by the application.
   
   - `-e PASSWORD=python`: Sets an environment variable `PASSWORD` within the container.
   
   - `php:apache`: This is the base image used to create the container, which includes both PHP and the Apache web server.


7. **Execute request inside Container**:
   The following command is used to execute a CURL request inside a running Docker container:

   ```bash
   docker exec front-end.student_list curl -u toto:python -X GET http://back-end.student_list:5000/pozos/api/v1.0/get_student_ages
   ```
      This command effectively allows you to interact with the back-end API from within the front-end container, enabling data retrieval for student ages using        Basic Authentication. It ask the frontend container to request the backend api.
   
   **Explanation of the Command**
      - `docker exec front-end.student_list`: This part of the command specifies that you want to execute a command inside the running container named `front-end.student_list`.
      
      - `curl`: This is a command-line tool used to make HTTP requests. In this case, it is used to send a GET request to an API.
      
      - `-u toto:python`: This option provides the username (`toto`) and password (`python`) for Basic Authentication when accessing the API.
      
      - `-X GET`: This specifies the HTTP method to be used for the request. In this case, it indicates that the request is a GET request, which is typically used to retrieve data.
      
      - `http://back-end.student_list:5000/pozos/api/v1.0/get_student_ages`: This is the URL of the API endpoint being called. It points to the path that retrieves the ages of students from the back-end service running on port 5000.

   **Output of the Command**
      ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/execut.png)

 8. **Test student_list application**:
      If you're running the app into a remote server or a virtual machine (e.g provisionned by eazytraining's vagrant file), please find your ip address typing

      ```bash
      hostname -I
      ```

    **Output of the Command**
     ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/webapp.png)

9. **Clean the workspace**:
      To clean up your Docker environment and remove all containers, images, and networks created during the project, run the following commands:

      ```bash
      docker stop back-end.student_list
      docker stop front-end.student_list
      docker network ls
      docker ps
      docker images
      ```
      This will:
   
         - Stop both the back-end and front-end containers.
         - Remove the custom Docker network (student_list.network).
         - Finally, it verifies that no containers, networks are left running or present in your workspace.

    **Output of the Command**
     ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/stop.png)
   



## Infrastructure As Code

1. **Run the Application (Front-end & Back-end) automatically with docker-compose**:
      To simplify the deployment of the front-end and back-end services, we will use Docker Compose. This will allow us to manage both the API (back-end) and          the website (front-end) together. Below is the docker-compose.yml file configuration, which you can use to deploy both services in one command.

   Here's the `docker-compose.yml` configuration:
         
      ```yaml
         version: '3.3'
         
         services:
         # Front-End service configuration (Website)
              front-end.student_list:
                image: php:apache  # Use the official PHP + Apache image
                container_name: front-end.student_list  # Name of the container for easy management
                depends_on:
                  - back-end.student_list  # Ensure the back-end service starts first
                ports:
                  - "80:80"  # Map host port 80 to container port 80 (web access)
                volumes:
                  - ./website/:/var/www/html  # Mount local website directory to container's web server folder
                environment:
                  - USERNAME=toto  # Environment variable for API username
                  - PASSWORD=python  # Environment variable for API password
                networks:
                  - student_list.network  # Use the custom Docker network for inter-container communication
            
         # Back-End service configuration (API)
              back-end.student_list:
                image: back-end.student_list.img  # Use the custom-built API image
                container_name: back-end.student_list  # Name of the container for easy management
                volumes:
                  - './simple_api/:/data/'  # Mount local API data directory to container's /data directory
                networks:
                  - student_list.network  # Use the custom Docker network for inter-container communication
            
         # Define the custom network for both front-end and back-end services
            networks:
              student_list.network:
         ```

      To run the student_list application `(website & API REST)`, use this command :

      ```bash 
       docker-compose up -d 
       ```

**Output of the Command**
![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/docker-compose.png)
![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/click_api.png)


## Deploy Private Registry

1. **Create Docker Registry & Web Interface**:
         This Docker Compose configuration establishes a robust environment featuring two primary services:

      - Docker registry 
      - user interface.

   The pozos-registry service utilizes the official Docker registry image, ensuring that it automatically restarts unless manually stopped. It facilitates the management of container images by mapping host port 5000 to the registry's port and mounting necessary directories for data and authentication. The service also supports image deletion and user authentication via htpasswd, securing access with a designated username and password.

   Complementing this, the frontend.reg service provides a user-friendly interface for the registry, allowing users to interact with the registry through a web browser. This UI connects seamlessly to the registry, supporting features like image deletion and offering a cohesive experience for managing container images in a streamlined manner. Both services operate within a custom network, ensuring efficient communication and resource sharing.
      
   Here's the `docker-compose.registry.yml` configuration:
   
      ```yml
      version: '3.3'  # Specify the version of Docker Compose to use

      services:
        pozos-registry:  # Service for the Docker registry
          image: registry:2.8.1  # Use the official registry image version 2.8.1
          container_name: pozos-registry  # Name the container for easy management
          restart: always  # Always restart the container unless stopped
          ports: 
            - "5000:5000"  # Map port 5000 on the host to port 5000 in the container
          volumes:
            - /opt/docker/registry:/var/lib/registry  # Mount a host directory for registry data
            - ./registry/auth:/auth  # Mount a local directory for authentication files
          environment:
            - REGISTRY_STORAGE_DELETE_ENABLED=true  # Enable deletion of images
            - REGISTRY_AUTH=htpasswd  # Use htpasswd for authentication
            - "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm"  # Set the realm for htpasswd authentication
            - REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd  # Path to the htpasswd file
          command:
            - /bin/sh  # Execute the shell
            - -c  # Run the following command in the shell
            - 'apk add --no-cache apache2-utils && htpasswd -Bbn pozos omar > /auth/htpasswd && registry serve /etc/docker/registry/config.yml'  
            # Install apache2-utils, create a new htpasswd entry for user 'pozos' with password 'omar', 
            # and start the registry using the specified config file
          networks:
            - pozos_reg.network  # Attach the service to the custom network
      
        frontend.reg:  # Service for the registry UI
          image: joxit/docker-registry-ui:2  # Use the official Docker registry UI image
          container_name: frontend.reg  # Name the container for easy management
          depends_on:
            - pozos-registry  # Ensure the registry starts before the UI
          ports: 
            - "8080:80"  # Map port 8080 on the host to port 80 in the container
          environment:
            - NGINX_PROXY_PASS_URL=http://pozos-registry:5000  # URL for proxying requests to the registry
            - DELETE_IMAGES=true  # Allow deletion of images through the UI
            - REGISTRY_TITLE=Pozos  # Title for the registry UI
            - SINGLE_REGISTRY=true  # Indicate that this UI connects to a single registry
          networks:
            - pozos_reg.network  # Attach the service to the custom network
      
      networks:
        pozos_reg.network:  # Define a custom network for the services
      ```
      
 2. **Starting Services with Docker Compose**:
   
      To run and start all services `(web interface & Registry)`, use this command :

      ```bash 
       docker-compose -f docker-compose.registry.yml up -d
      ```
   
      The command `docker-compose -f docker-compose.registry.yml up -d` is used to start services defined in a specific Docker Compose file. Here's a breakdown         of each part:
      
         - `docker-compose`: This is the command-line tool used to define and manage multi-container Docker applications.
         
         - `-f` docker-compose.registry.yml: The `-f` flag specifies the filename of the Docker Compose configuration file you want to use. In this case, it's            `docker-compose.registry.yml`. This file contains the definitions for the services, networks, and volumes that will be used.
         
         - `up`: This command creates and starts the containers defined in the specified Docker Compose file. If the containers already exist, it will recreate            them only if their configuration has changed.
         
         - `-d`: This flag stands for "detached mode." It means that the containers will run in the background, allowing you to continue using your terminal               without being tied to the output of the containers. If you omit this flag, the logs from the containers will be displayed in your terminal.

     **Output of the Command**
     ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/docker-compose.png)

3. **Log on Private REGISTY**:
   
      To log in to a local Docker registry running on your machine at port 5000., use this command :

      ```bash 
       docker login localhost:5000
      ```

   **Output of the Command**
   ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/log_reg.png)

4. **Push an Image to the Private REGISTRY**:
   
      To push an image to the private Registry, use this command :

      ```bash 
       docker image tag back-end.student_list.img:latest localhost:5000/pozos/back-end.student_list.img:latest
       docker image push localhost:5000/pozos/back-end.student_list.img:latest

      ```

   **Output of the Command**
   ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/push.png)

   ![project](https://github.com/omarvm001/Devops_projet_01_Docker/blob/main/result.png)







