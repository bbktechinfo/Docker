Docker Day 2 – Jenkins Installation and Execution
Hands-on DevOps Learning Notes

Environment: AWS EC2 | Ubuntu Linux | Docker Engine
Application: Jenkins
Docker Image: jenkins/jenkins:lts-jdk21
Host Port: 8080
Container Port: 8080

1. Learning Objectives

Today's practical exercise focused on installing and running Jenkins using Docker.

The learning objectives were:

Understand Docker Hub and image repositories.
Search for Docker images.
Identify official and trusted Docker images.
Understand Docker image tags.
Download the Jenkins Docker image.
Understand whether Jenkins images include Java.
Create and run Jenkins containers.
Understand Docker port mapping.
Retrieve the initial Jenkins administrator password.
Access the Jenkins dashboard through a browser.
2. Introduction to Docker Hub

Docker Hub is a cloud-based container registry where developers and organizations publish and distribute Docker images.

Website:

https://hub.docker.com/

Think of Docker Hub as an online repository for Docker images.

Examples of Docker images
Application	Docker Image
Jenkins	jenkins/jenkins
SonarQube	sonarqube
Ubuntu	ubuntu
Nginx	nginx
MySQL	mysql
PostgreSQL	postgres
Redis	redis
Python	python

Instead of manually installing applications on Ubuntu, we can download their Docker images and run them as containers.

Why should a DevOps engineer know Docker Hub?

Before downloading an image, a DevOps engineer should verify:

Image repository name.
Publisher or maintainer.
Official project documentation.
Available image tags.
Application version.
Java or other runtime dependencies.
Operating system and architecture compatibility.
Security and maintenance information.

This helps avoid downloading incorrect or untrusted images.

3. Searching for Docker Images

There are two ways to search for Docker images.

Method 1: Docker Hub Website

Steps:

Open https://hub.docker.com/
Sign in using your Docker account.
Search for Jenkins.
Identify the Jenkins-maintained repository.
Open the repository.
Read the Overview and documentation.
Open the Tags section.
Select the required version.

The Jenkins Docker repository is:

https://hub.docker.com/r/jenkins/jenkins

Repository name:

jenkins/jenkins

Method 2: Docker CLI

We can also search for images directly from the EC2 Ubuntu terminal.

Command:

bash
docker search jenkins


Purpose:

Searches Docker Hub for repositories matching Jenkins.
Displays matching repository names and descriptions.
Helps discover available images.

To search for official Docker images, we can use:

bash
docker search --filter is-official=true nginx


Important:

docker search helps discover repositories. It does not display the complete list of available image tags.

For tags, use the Docker Hub website.

4. Understanding the Jenkins Docker Image

The image selected during this practical exercise was:

bash
jenkins/jenkins:lts-jdk21


Let's understand its components.

Component	Meaning
First jenkins	Docker Hub organization
Second jenkins	Image repository
lts	Long-Term Support release
jdk21	Java Development Kit 21-based image

This is a Jenkins-maintained Docker image.

Official repository:

https://hub.docker.com/r/jenkins/jenkins

Why did we select lts-jdk21?
LTS stands for Long-Term Support.
It provides the Jenkins LTS release.
JDK 21 provides the Java runtime needed to run Jenkins.
It is suitable for learning and stable Jenkins environments.
5. Does the Jenkins Docker Image Include Java?

Yes!

The Jenkins Docker image includes the Java runtime required to run Jenkins.

For example:

bash
jenkins/jenkins:lts-jdk21


This image includes Java 21.

Therefore, we do not need to install Java separately on the EC2 Ubuntu host just to run Jenkins.

Important distinction

There are two different environments:

EC2 Ubuntu host machine.
Jenkins Docker container.

Java inside the Jenkins container is separate from Java installed on the EC2 host.

Verify Java inside Jenkins

After starting the Jenkins container:

bash
docker exec -it <container_id> java -version


Example:

bash
docker exec -it fc3199734bba java -version


This displays the Java version available inside the Jenkins container.

To verify the Java compiler:

bash
docker exec -it fc3199734bba javac -version


Note: Jenkins' own Java runtime and the JDK versions needed by application build jobs are separate configuration considerations.

6. Download the Jenkins Docker Image

After identifying the correct Jenkins image on Docker Hub, we downloaded it to our EC2 instance.

Command:

bash
docker pull jenkins/jenkins:lts-jdk21

Purpose
Downloads the Jenkins Docker image from Docker Hub.
Stores it in the local Docker image repository.
Does not create or start a container.
Understanding the output

Example:

text
lts-jdk21: Pulling from jenkins/jenkins

28ac0f42bec5: Pull complete
bfa1e0807eb8: Pull complete
b4c5b3edde88: Pull complete

Digest: sha256:xxxxxxxx

Status: Downloaded newer image for jenkins/jenkins:lts-jdk21


Docker images consist of multiple layers.

The Pull complete messages indicate that Docker successfully downloaded the required image layers.

Verify downloaded images

Command:

bash
docker images


My actual output:

text
IMAGE                       ID             DISK USAGE   CONTENT SIZE
jenkins/jenkins:lts-jdk21   74401de6a6f9   712MB        244MB
sonarqube:latest            4e43d3eaae92   2.48GB       1.06GB


This confirms that the Jenkins image was downloaded successfully.

7. Create and Run the Jenkins Container

After downloading the image, we created and started the Jenkins container.

Command executed:

bash
docker run -d -p 8080:8080 jenkins/jenkins:lts-jdk21

Understanding each component
Component	Explanation
docker run	Creates and starts a container
-d	Runs the container in detached mode
-p	Maps host port to container port
8080 (first)	EC2 host port
8080 (second)	Jenkins container port
jenkins/jenkins:lts-jdk21	Docker image used to create the container
What is detached mode (-d)?

Detached mode runs the container in the background.

It allows us to:

Start Jenkins.
Return control to the terminal.
Execute other Docker commands.
Keep Jenkins running in the background.
Understanding port mapping

Syntax:

bash
-p HOST_PORT:CONTAINER_PORT


Our command:

bash
-p 8080:8080


Meaning:

Host port 8080: Port exposed on the EC2 instance.
Container port 8080: Port where Jenkins listens inside the container.

Jenkins uses port 8080 by default for its web interface.

Port mapping visualization
text
Web Browser
    |
    | http://EC2-PUBLIC-IP:8080
    |
    v
AWS EC2 Ubuntu Instance
    |
    | Host Port: 8080
    |
    v
Docker Port Forwarding
    |
    | 8080 --> 8080
    |
    v
Jenkins Docker Container
    |
    | Container Port: 8080
    |
    v
Jenkins Application

8. Verify the Running Jenkins Container

After executing docker run, Docker returned a container ID.

My container ID:

text
fc3199734bba


To verify running containers:

bash
docker ps


My actual output:

text
CONTAINER ID   IMAGE                       COMMAND
fc3199734bba   jenkins/jenkins:lts-jdk21   /usr/bin/tini -- ...

PORTS
0.0.0.0:8080->8080/tcp, 50000/tcp

NAMES
recursing_panini


This confirms:

Jenkins container was created.
Jenkins container is running.
Host port 8080 is mapped to container port 8080.
Docker automatically generated a container name: recursing_panini.
List all containers
bash
docker ps -a


Difference:

Command	Purpose
docker ps	Lists running containers
docker ps -a	Lists all containers, including stopped and created containers
9. Understanding Container Name vs Image Name

This was an important concept learned during the Jenkins exercise.

Image name:

text
jenkins/jenkins:lts-jdk21


Container name:

text
recursing_panini


Container ID:

text
fc3199734bba


These three identifiers are different.

Identifier	Meaning
Image name	Docker image used to create the container
Container name	Name assigned to a container
Container ID	Unique identifier assigned to the container
Important Docker command

When using docker exec, we must specify a container name or container ID.

Correct:

bash
docker exec -it fc3199734bba java -version


Or:

bash
docker exec -it recursing_panini java -version


Incorrect:

bash
docker exec -it jenkins/jenkins:lts-jdk21 java -version


Why?

Because jenkins/jenkins:lts-jdk21 is an image name, not a container name.

10. Retrieve the Initial Jenkins Administrator Password

Unlike SonarQube, Jenkins does not initially use a simple admin/admin login.

During the first startup, Jenkins generates an initial administrator password.

The password is stored inside the Jenkins container at:

text
/var/jenkins_home/secrets/initialAdminPassword

First attempt

I executed:

bash
sudo cat /var/jenkins_home/secrets/initialAdminPassword


It returned:

text
cat: /var/jenkins_home/secrets/initialAdminPassword:
No such file or directory

Why did it fail?

Because the Jenkins directory exists inside the Docker container, not necessarily on the EC2 Ubuntu host.

Remember:

EC2 Ubuntu is the host machine.
Jenkins runs inside the Docker container.
The container has its own filesystem.
Second attempt

I executed:

bash
docker exec fc3199734bba /var/jenkins_home/secrets/initialAdminPassword


It returned:

text
permission denied

Why did it fail?

Because I attempted to execute the password file as if it were a command.

But it is only a text file containing the administrator password.

Correct command
bash
docker exec fc3199734bba cat /var/jenkins_home/secrets/initialAdminPassword


This executes cat inside the Jenkins container and displays the password.

Alternative using the container name:

bash
docker exec recursing_panini cat /var/jenkins_home/secrets/initialAdminPassword

Understanding the command
Component	Meaning
docker exec	Executes a command inside an existing container
fc3199734bba	Jenkins container ID
cat	Displays file contents
/var/jenkins_home/secrets/initialAdminPassword	Password file location inside the container
11. Access Jenkins Through the Browser

After retrieving the initial administrator password, I opened Jenkins using the EC2 public IP address.

URL:

text
http://<EC2-PUBLIC-IP>:8080

AWS Security Group configuration

For browser access, the EC2 Security Group must allow inbound traffic on port 8080.

Configuration:

Setting	Value
Type	Custom TCP
Protocol	TCP
Port	8080
Source	My IP

Using My IP is preferable to exposing Jenkins publicly to everyone.

Jenkins initial setup

When I opened the Jenkins URL, the Unlock Jenkins screen appeared.

Steps followed:

Copied the initial administrator password.
Pasted it into the Unlock Jenkins screen.
Clicked Continue.
Selected Install suggested plugins.
Waited for plugin installation.
Created the administrator account.
Completed the initial configuration.
Accessed the Jenkins dashboard successfully.

Result: Jenkins dashboard launched successfully!

12. Important Jenkins Docker Commands
Command	Purpose
docker search jenkins	Search Docker Hub
docker pull jenkins/jenkins:lts-jdk21	Download Jenkins image
docker images	List downloaded images
docker run -d -p 8080:8080 jenkins/jenkins:lts-jdk21	Create and start Jenkins container
docker ps	List running containers
docker ps -a	List all containers
docker logs -f <container_id>	View Jenkins logs
docker exec -it <container_id> bash	Enter Jenkins container
docker exec -it <container_id> java -version	Check Java version
docker exec <container_id> cat <file>	Read a file inside the container
docker stop <container_id>	Stop Jenkins container
docker start <container_id>	Start a stopped Jenkins container
docker restart <container_id>	Restart Jenkins container
docker rm -f <container_id>	Forcefully remove a container
docker rmi <image_name>	Remove a Docker image
13. Important Docker Concepts Learned
Concept	Understanding
Docker Hub	Cloud-based container registry
Docker Image	Blueprint used to create containers
Docker Container	Instance created
