
# Docker – Day 1 Learning Notes

## Docker Fundamentals: Installing Docker and Running SonarQube

**Environment:** AWS EC2 | Ubuntu Linux

**Day 1 Objectives:**
- Verify Docker installation
- Install Docker on Ubuntu
- Configure Docker permissions
- Download Docker images
- Create and run Docker containers
- Understand Docker port mapping
- Access SonarQube through a browser

---

## 1. Verify Whether Docker Is Installed

Initially, I checked whether Docker was already installed on my EC2 Ubuntu instance.

**Command:**

```bash
docker --version
```

**Purpose:**
- Checks whether Docker is installed.
- Displays the installed Docker version.
- If Docker is not installed, it returns a command-not-found error.

**Observation:** Docker was not installed on my EC2 instance.

---

## 2. Update Ubuntu Package Repositories

Initially, I tried installing Docker directly:

```bash
sudo apt install docker.io
```

However, Ubuntu returned an error indicating that the package was unavailable.

To resolve this, I updated the Ubuntu package repositories.

**Command:**

```bash
sudo apt update
```

Alternatively:

```bash
sudo apt update -y
```

**Purpose:**
- Refreshes package information from configured Ubuntu repositories.
- Retrieves information about available packages and versions.
- Helps Ubuntu identify available package versions.

**Important:** `apt update` updates package indexes. It does not upgrade all installed packages.

---

## 3. Install Docker on Ubuntu

After updating the package repositories, I executed:

**Command:**

```bash
sudo apt install docker.io
```

Or:

```bash
sudo apt install docker.io -y
```

**Purpose:**
- Installs Docker Engine using the Ubuntu package repository.
- Installs required Docker dependencies.
- Makes Docker commands available on Ubuntu.

### Understanding sudo and -y

| Component | Meaning |
|---|---|
| `sudo` | Execute with administrative privileges |
| `apt` | Ubuntu package management tool |
| `install` | Installs a package |
| `docker.io` | Docker package |
| `-y` | Automatically confirms installation prompts |

---

## 4. Verify Docker Installation

After Docker installation, I executed:

```bash
docker --version
```

Then:

```bash
docker version
```

### Difference Between These Commands

| Command | Purpose |
|---|---|
| `docker --version` | Displays Docker version |
| `docker version` | Displays detailed Docker client and server information |

Initially, `docker version` displayed client information but returned a permission-denied error when communicating with the Docker daemon.

---

## 5. Configure Docker Permissions

### Why Did Permission Denied Occur?

Docker uses a client-server architecture.

- **Docker Client:** Accepts Docker commands from users.
- **Docker Daemon:** Runs in the background and manages containers, images, networks and volumes.

The Docker daemon typically communicates through a Unix socket:

```text
/var/run/docker.sock
```

By default, access to this socket is restricted.

Therefore, my Ubuntu user (`ubuntu`) did not initially have permission to communicate with the Docker daemon.

### Add Ubuntu User to the Docker Group

**Command:**

```bash
sudo usermod -aG docker ubuntu
```

### Understanding the Command

| Component | Meaning |
|---|---|
| `sudo` | Execute with administrative privileges |
| `usermod` | Modify an existing user account |
| `-a` | Append the user to an existing group |
| `-G` | Specify supplementary groups |
| `docker` | Docker group |
| `ubuntu` | Ubuntu username |

**Important:** Membership in the Docker group effectively grants root-level privileges. Only add trusted users.

### Apply Group Membership

Execute:

```bash
newgrp docker
```

Alternatively, log out and log back into the EC2 instance.

Now verify:

```bash
docker version
```

This time, Docker client and server information should be displayed successfully.

---

## 6. Download the SonarQube Docker Image

After Docker was installed and configured, I downloaded the SonarQube Docker image.

**Command:**

```bash
docker pull sonarqube:latest
```

**Purpose:**
- Downloads the SonarQube image from Docker Hub.
- Stores the downloaded image in the local Docker image repository.
- Does not start a container.

### Understanding the Image Name

```text
sonarqube:latest
```

| Component | Meaning |
|---|---|
| `sonarqube` | Docker image repository name |
| `latest` | Image tag |

The `latest` tag refers to the image designated as latest by its publisher.

**Important:** `latest` does not necessarily mean the newest version of every edition.

---

## 7. Verify Downloaded Docker Images

After pulling the SonarQube image, I verified whether it was downloaded successfully.

**Command:**

```bash
docker images
```

Example output:

```text
REPOSITORY    TAG       IMAGE ID
sonarqube     latest    4e43d3eaae92
ubuntu        latest    065aa8626841
```

**Purpose:**
- Lists Docker images available locally.
- Displays repository name, tag, image ID and image size.

### Important Distinction

A Docker image is a template used to create containers.

A Docker container is an instance created from a Docker image.

---

## 8. Create and Run the SonarQube Container

After downloading the image, I created and started a SonarQube container.

**Command:**

```bash
docker run -d \
  -p 9989:9000 \
  sonarqube:latest
```

### Understanding Each Component

| Command | Explanation |
|---|---|
| `docker run` | Creates and starts a container |
| `-d` | Runs the container in detached mode |
| `-p` | Maps host port to container port |
| `9989` | EC2 host port |
| `9000` | SonarQube container port |
| `sonarqube:latest` | Docker image used to create the container |

### What Is Detached Mode (-d)?

- Runs the container in the background.
- Returns control to the terminal immediately.
- Allows us to execute other commands while the container continues running.

To view container logs:

```bash
docker logs -f <container_id>
```

### Understanding Port Mapping

The syntax is:

```bash
-p HOST_PORT:CONTAINER_PORT
```

In my example:

```bash
-p 9989:9000
```

- Host port `9989`: Port exposed on the EC2 instance.
- Container port `9000`: Port on which SonarQube listens inside the container.

SonarQube runs on port `9000` by default inside the container.

Docker forwards requests from EC2 port `9989` to container port `9000`.

### Port Mapping Visualization

```text
Web Browser
    |
    | http://EC2-PUBLIC-IP:9989
    |
    v
AWS EC2 Ubuntu Instance
    |
    | Host Port: 9989
    |
    v
Docker Port Forwarding
    |
    | 9989 --> 9000
    |
    v
SonarQube Docker Container
    |
    | Container Port: 9000
    |
    v
SonarQube Application
```

---

## 9. Verify the Running Container

After executing `docker run`, Docker returned a container ID.

To verify the container status:

```bash
docker ps
```

To list all containers, including stopped containers:

```bash
docker ps -a
```

### Difference Between These Commands

| Command | Output |
|---|---|
| `docker ps` | Only running containers |
| `docker ps -a` | All containers, including stopped ones |

---

## 10. Access SonarQube Through the Browser

After the container was started successfully, I accessed SonarQube using the EC2 public IP address and mapped host port.

**URL:**

```text
http://<EC2-PUBLIC-IP>:9989
```

The SonarQube login page appeared successfully.

### Default Login Credentials

| Field | Default Value |
|---|---|
| Username | admin |
| Password | admin |

After logging in:

1. SonarQube requested a password change.
2. I entered a new password.
3. The password was updated successfully.
4. I could access the SonarQube dashboard.

---

## 11. Important Docker Commands Learned Today

| Command | Purpose |
|---|---|
| `docker --version` | Check Docker version |
| `docker version` | Check client and server details |
| `sudo apt update` | Update Ubuntu package indexes |
| `sudo apt install docker.io` | Install Docker |
| `sudo usermod -aG docker ubuntu` | Add user to Docker group |
| `newgrp docker` | Apply new group membership to current shell |
| `docker pull sonarqube:latest` | Download SonarQube image |
| `docker images` | List downloaded images |
| `docker run -d -p 9989:9000 sonarqube:latest` | Create and run SonarQube container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker logs -f <container_id>` | View container logs |
| `docker exec -it <container_name> bash` | Enter a running container |
| `docker stop <container_id>` | Stop a container |
| `docker rm <container_id>` | Remove a container |
| `docker rmi <image_name>` | Remove an image |

---

## 12. Important Concepts Learned

| Concept | Understanding |
|---|---|
| Docker Image | Blueprint/template for creating containers |
| Docker Container | Running or stopped instance of an image |
| Docker Hub | Registry for downloading and sharing Docker images |
| Docker Daemon | Background service that manages Docker objects |
| Docker Client | Interface used to execute Docker commands |
| Port Mapping | Connects a host port to a container port |
| Detached Mode | Runs containers in the background |
| Docker Group | Allows authorized users to access Docker daemon |

---

## 13. Day 1 Architecture – What Did I Build?

My practical implementation can be summarized as:

1. **AWS EC2 Instance**
   - Ubuntu Linux virtual machine.

2. **Docker Installation**
   - Docker Engine installed and configured.

3. **Docker Image Downloaded**
   - `sonarqube:latest`

4. **Docker Container Created**
   - SonarQube application running inside the container.

5. **Port Mapping Configured**
   - EC2 port `9989` mapped to container port `9000`.

6. **Browser Access Successful**
   - SonarQube dashboard accessed using EC2 public IP.

---

## 14. What Should I Explore on Docker Day 2?

Recommended learning topics:

1. Docker architecture – Client, Daemon, Registry, Images and Containers.
2. Docker container lifecycle – Created, Running, Paused, Stopped and Removed.
3. Difference between `docker run`, `docker start` and `docker exec`.
4. Dockerfile – How to create your own Docker image.
5. Docker volumes – How to persist SonarQube data even after container removal.
6. Docker networks – How containers communicate with each other.
7. Docker Compose – How to run multiple containers together.

### Recommended Next Exercise: Docker Volumes

The current SonarQube container was started without persistent volumes.

If the container is removed, its writable-layer data will be lost.

A proper SonarQube deployment should persist its data, extensions and logs using Docker volumes.

---

## 15. Day 1 Self-Assessment

Test your understanding with these questions.

### Question 1
Which command downloads a Docker image without starting a container?

- A. `docker run`
- B. `docker pull`
- C. `docker exec`

**Answer:** B. `docker pull`

### Question 2
In `-p 9989:9000`, what does 9989 represent?

- A. Container port
- B. SonarQube internal port
- C. EC2 host port

**Answer:** C. EC2 host port

### Question 3
What does `-d` do in docker run?

- A. Runs in background
- B. Deletes the image
- C. Enables debugging

**Answer:** A. Runs in background (detached mode).

### Question 4
Which command lists running containers?

- A. `docker images`
- B. `docker ps`
- C. `docker rmi`

**Answer:** B. `docker ps`

### Question 5
What is the difference between an image and a container?

- A. They are identical.
- B. Image is a template; container is an instance.
- C. Container is a registry.

**Answer:** B. An image is a blueprint. A container is created from that blueprint.

---

## Day 1 Summary

Today I successfully:

- Installed Docker on an AWS EC2 Ubuntu instance.
- Configured Docker user permissions.
- Downloaded the SonarQube Docker image.
- Created and started a SonarQube container.
- Understood Docker detached mode and port mapping.
- Accessed the SonarQube dashboard through a browser.
- Changed the default SonarQube password.

**Day 1 practical exercise completed successfully!**

**Next milestone:** Learn Dockerfiles, Docker volumes, Docker networking and Docker Compose.
