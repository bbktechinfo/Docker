Docker – Day 1 Learning Notes
Day 1: Docker Fundamentals

Completed
Docker + SonarQube
Environment: AWS EC2 | Ubuntu Linux

Today's learning objectives

Verify Docker installation

Install Docker on Ubuntu

Configure Docker permissions

Pull a Docker image

Create and run a Docker container

Understand port mapping

Access SonarQube through a browser

1. Verify whether Docker is installed
Initially, I wanted to check whether Docker was already installed on my EC2 Ubuntu instance.

Command:


bash
docker --version
Purpose:

Checks whether Docker is installed.

Displays the installed Docker version.

If Docker is not installed, it returns a command-not-found error.

Initial observation: Docker was not installed on my EC2 instance.

2. Update Ubuntu package repositories
Initially, I tried installing Docker directly:


bash
sudo apt install docker.io
However, Ubuntu returned an error indicating that the package was unavailable.

To resolve this, I updated the Ubuntu package repositories.

Command:


bash
sudo apt update
Alternatively:


bash
sudo apt update -y
Purpose:

Refreshes the package information from configured Ubuntu repositories.

Retrieves information about available packages and their versions.

Helps Ubuntu identify the latest available package versions.

Note: apt update updates package indexes; it does not upgrade all installed packages.

3. Install Docker on Ubuntu
After updating the package repositories, I executed:

Command:


bash
sudo apt install docker.io
Or:


bash
sudo apt install docker.io -y
Purpose:

Installs Docker Engine using the Ubuntu package repository.

Installs the required Docker dependencies.

Makes Docker commands available on the Ubuntu machine.

What is sudo?

sudo stands for Superuser Do.

It allows a user to execute commands with administrative privileges.

What is -y?

Automatically answers yes to installation confirmation prompts.

4. Verify Docker installation
After Docker installation was completed, I executed:


bash
docker --version
Then I executed:


bash
docker version
Difference between these commands:

Command

Purpose

docker --version

Displays Docker version

docker version

Displays detailed Docker client and server version information

Initially, docker version displayed client information but returned a permission-denied error when trying to communicate with the Docker daemon.

5. Configure Docker permissions
Why did permission denied occur?
Docker uses a client-server architecture.

Docker Client: Accepts Docker commands from users.

Docker Daemon: Runs in the background and manages containers, images, networks, and volumes.

The Docker daemon typically communicates through a Unix socket:


text
/var/run/docker.sock
By default, access to this socket is restricted.

Therefore, my Ubuntu user (ubuntu) did not initially have permission to communicate with the Docker daemon.

Add Ubuntu user to the Docker group
Command:


bash
sudo usermod -aG docker ubuntu
Understanding the command:

Component

Meaning

sudo

Execute with administrative privileges

usermod

Modify an existing user account

-a

Append the user to an existing group

-G

Specify supplementary groups

docker

Docker group

ubuntu

Ubuntu username

Important: Adding a user to the Docker group effectively grants root-level privileges. Only add trusted users.

Apply the group membership
Execute:


bash
newgrp docker
Alternatively, log out and log back into the EC2 instance.

Now verify:


bash
docker version
This time, Docker client and server information should be displayed successfully.

6. Download the SonarQube Docker image
Now that Docker was installed and configured, I wanted to download the SonarQube Docker image.

Command:


bash
docker pull sonarqube:latest
Purpose:

Downloads the SonarQube image from Docker Hub.

Stores the downloaded image in the local Docker image repository.

Does not start a container.

Understanding the image name

text
sonarqube:latest
Component

Meaning

sonarqube

Docker image repository name

latest

Image tag

The latest tag refers to the image designated as latest by its publisher. It does not necessarily mean the newest version of every edition.

7. Verify downloaded Docker images
After pulling the SonarQube image, I verified whether it was downloaded successfully.

Command:


bash
docker images
Example output:


text
REPOSITORY    TAG       IMAGE ID
sonarqube     latest    4e43d3eaae92
ubuntu        latest    065aa8626841
Purpose:

Lists Docker images available locally.

Displays repository name, tag, image ID, and image size.

Important distinction:

A Docker image is a template used to create containers.

A Docker container is an instance created from a Docker image.

8. Create and run the SonarQube container
After downloading the image, I created and started a SonarQube container.

Command:


bash
docker run -d \
  -p 9989:9000 \
  sonarqube:latest
Let's understand each component.

Command

Explanation

docker run

Creates and starts a container

-d

Runs the container in detached mode (background)

-p

Maps host port to container port

9989

EC2 host port

9000

SonarQube container port

sonarqube:latest

Docker image used to create the container

Understanding port mapping
The syntax is:


bash
-p HOST_PORT:CONTAINER_PORT
In my example:


bash
-p 9989:9000
Host port 9989: Port exposed on the EC2 instance.

Container port 9000: Port on which SonarQube listens inside the container.

SonarQube runs on port 9000 by default inside the container.

Docker forwards requests from EC2 port 9989 to container port 9000.

How the request reaches SonarQube

Web Browser

http://EC2-PUBLIC-IP:9989 

Docker port forwarding
9989 → 9000

SonarQube Docker Container

Application listening on port 9000

What is detached mode (-d)?
Runs the container in the background.

Returns control to the terminal immediately.

Allows us to execute other commands while the container continues running.

To view container logs:


bash
docker logs -f <container_id>
9. Verify the running container
After executing docker run, Docker returned a container ID.

To verify the container status:


bash
docker ps
To list all containers, including stopped containers:


bash
docker ps -a
Difference:

Command

Output

docker ps

Only running containers

docker ps -a

All containers, including stopped ones

10. Access SonarQube through the browser
After the container was started successfully, I accessed SonarQube using the EC2 public IP address and mapped host port.

URL:


text
http://<EC2-PUBLIC-IP>:9989
The SonarQube login page appeared successfully.

Mastering SonarQube with Jenkins & Multi-Branch Support: Complete Hands-On Walkthrough | by @Harsh | Medium
 
Default login credentials
Field

Default value

Username

admin

Password

admin

After logging in:

SonarQube requested a password change.

I entered a new password.

The password was updated successfully.

I could access the SonarQube dashboard.

Day 1 practical exercise completed
11. Important Docker commands learned today
Here is a quick reference of the commands covered during today's practical session.

Command

Purpose

docker --version

Check Docker version

docker version

Check client and server details

sudo apt update

Update Ubuntu package indexes

sudo apt install docker.io

Install Docker

sudo usermod -aG docker ubuntu

Add user to Docker group

newgrp docker

Apply new group membership to current shell

docker pull sonarqube:latest

Download SonarQube image

docker images

List downloaded images

docker run -d -p 9989:9000 sonarqube:latest

Create and run SonarQube container

docker ps

List running containers

docker ps -a

List all containers

docker logs -f <container_id>

View container logs

docker exec -it <container_name> bash

Enter a running container

docker stop <container_id>

Stop a container

docker rm <container_id>

Remove a container

docker rmi <image_name>

Remove an image

12. Important concepts learned
These are the concepts I should remember from Day 1.

Concept

Understanding

Docker Image

Blueprint/template for creating containers

Docker Container

Running or stopped instance of an image

Docker Hub

Registry for downloading and sharing Docker images

Docker Daemon

Background service that manages Docker objects

Docker Client

Interface used to execute Docker commands

Port Mapping

Connects a host port to a container port

Detached Mode

Runs containers in the background

Docker Group

Allows authorized users to access Docker daemon

13. Day 1 architecture – What did I build?
Understanding Docker Architecture: Docker Client, Docker Daemon, Docker Host, Registry, Images, Containers & Volumes
 
Your practical implementation can be summarized as:

AWS EC2 instance

Ubuntu Linux virtual machine.

Docker installation

Docker Engine installed and configured.

Docker image downloaded

sonarqube:latest

Docker container created

SonarQube application running inside the container.

Port mapping configured

EC2 9989 → Container 9000.

Browser access successful

SonarQube dashboard accessed using EC2 public IP.

14. What should I explore on Docker Day 2?
I recommend learning these topics next, in this order:

Docker architecture – Client, Daemon, Registry, Images and Containers.

Docker container lifecycle – Created, Running, Paused, Stopped and Removed.

Difference between docker run, docker start and docker exec.

Dockerfile – How to create your own Docker image.

Docker volumes – How to persist SonarQube data even after container removal.

Docker networks – How containers communicate with each other.

Docker Compose – How to run multiple containers together.

One particularly important next exercise: Learn Docker volumes. Your current SonarQube container was started without persistent volumes. If you remove that container, its writable-layer data will be lost. A proper SonarQube deployment should persist its data, extensions and logs using Docker volumes.

Day 1 self-assessment
Test what you've learned today.

Docker Day 1 quiz

5/5 answered
1. Which command downloads a Docker image without starting a container?


docker run

docker pull

docker exec
Correct

docker pull downloads the image. docker run creates and starts a container.

2. In -p 9989:9000, what does 9989 represent?


Container port

SonarQube internal port

EC2 host port
Correct

9989 is the host port. 9000 is the container port.

3. What does -d do in docker run?


Runs in background

Deletes the image

Enables debugging
Correct

Detached mode runs the container in the background.

4. Which command lists running containers?


docker images

docker ps

docker rmi
Incorrect – docker ps

docker ps lists running containers.

5. What is the difference between an image and a container?


They are identical

Image is a template; container is an instance

Container is a registry
Correct

An image is the blueprint. A container is created from that blueprint.

Your score: 4/5
