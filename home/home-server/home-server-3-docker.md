The home server that I’ll be building will primarily be run using Docker containers. This is for all of the glory that comes with containerised services. In this article we will discuss the installation of Docker, Docker-Compose and Portainer for the true foundation of our home server.

<!-- Table of Contents -->

Docker installation.
====================

SSH into your docker installation to begin the installation process. We will be following the official Docker installation instructions for our install [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/).

In my opinion the most fantastic way to interact with your Docker system is to use the remote shell on VS code: [https://www.homeautomationguy.io/home-assistant-tips/using-visual-studio-to-edit-your-home-assistant-configuration-yaml-file-over-ssh/](https://www.homeautomationguy.io/home-assistant-tips/using-visual-studio-to-edit-your-home-assistant-configuration-yaml-file-over-ssh/)

Remove existing Docker installations.
-------------------------------------

Just in case there are old versions of Docker installed on your machine, run the following command to remove them:

```java
sudo apt-get remove docker docker-engine docker.io containerd runc
```

Installing Docker Engine.
-------------------------

We will be installing the Docker Engine using the repository installation method as advised by Docker. The instructions are as follows:

### Set up the Docker Engine repository.

Update the apt packages:

```java
sudo apt-get update
```

Install the packages required for us to download the Docker Engine repository using https:

```java
sudo apt-get install \
   ca-certificates \
   curl \
   gnupg \
   lsb-release
```

Once this has completed, create a directory for the official Docker GPG key and download the key.

```java
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Finally, run the following command to download the Docker Engine repository:

```java
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Install Docker Engine.

Update the apt packages one more time and then install the latest versions of Docker Engine, containerd and Docker Compose.

```java
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### Testing your installation.

In order to test that all has been installed correctly, you can spin up the Docker hello world container with the following command:

```java
sudo docker run hello-world
```

Configure Docker user.
----------------------

It would be useful to maintain our Docker environment with our Ubuntu user without needing to use root access for every command. In order to do this, we first need to create a Docker user group:

```java
sudo groupadd docker
```

Once complete, run the following command to add your Ubuntu user to the Docker user group:

```java
sudo usermod -aG docker <USER>
```

In order for the changes to take affect, log out and back in again with your user and test with the hello world Docker image without sudo:

```java
docker run hello-world
```

At this point you have successfully configured Docker and Docker-Compose. In our next step we will create our fist permanent container which will be used to manage our Docker landscape.

Installing Portainer.
=====================

Portainer is a GUI for the management of Docker containers, and helpfully runs in it’s own container.

Create a Docker Compose file.
-----------------------------

Our Docker environment will be maintained using the `/opt` directory in our Ubuntu installation. This directory will host our `docker-compose.yaml` configuration file as well as any additional configuration files required for our Docker containers.

Navigate to the `/opt` directory, and create a file called `docker-compose.yaml`.

```java
cd /opt
sudo nano docker-compose.yaml
```

We will go into more detail regarding Docker compose and configuring our Docker environment, however as a start, we will begin with adding the following information to our configuration file.

YAML configuration files read indentation in order to parse. Make sure your indentation is consistent when editing this file.

```java
version: '3.0'

services:
  portainer:
    container_name: portainer
    image: portainer/portainer-ce
    restart: always
    ports:
      - "9000:9000/tcp"
    environment:
      - TZ=Africa/Johannesburg
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /opt/portainer:/data
```

What this configuration does, is first confirm the Docker Compose version that we are running. The services list introduces the set of containers we would like to spin up. In this case, we have only added the container called “portainer” however in future we can expand this list with many more containers.

For more detail on docker compose service entries, have a look at the following documentation: [https://docs.docker.com/compose/compose-file/](https://docs.docker.com/compose/compose-file/)

Once this information has been saved, we’ll use the following command to spin up the containers and apply any changes based on updates to the `docker-compose.yaml` file.

```java
docker-compose up -d
```

The -d here means that the new containers will start up in detached mode.

Configuring Portainer.
----------------------

Once the container has completed building, you should now be able to navigate to the container web interface exposed on port 9000: [http://192.168.0.10:9000](http://192.168.0.10:9000)

The first time you open Portainer, you will be asked to create a username and password before accessing the interface. Create your credentials and proceed to the next screen.

![](attachments/1900552/13631504.png)

Select the **Local** option followed by **Connect** to start managing the containers on the local server of the Portainer instance.

![](attachments/1900552/14319645.png)

You should now see the Portainer interface! You can use this GUI to manage your entire Docker container system.

For more info on how to use Portainer, you can refer to the Portainer documentation: [https://docs.portainer.io/user/home](https://docs.portainer.io/user/home)

Conclusion.
===========

In this article, we’ve covered the initial configuration of Docker, Docker-Compose and Portainer for the foundation of our Home Server instance.

At this point you’ve covered the required work for starting to look through the following additional sections:

*   [Home Automation](Home-Automation_655375.html)
    
*   [Home Media Server](Home-Media-Server_1442078.html)
    

**←** [Home Server, Part 2: Ubuntu OS](14024705.html)

[Home Server, Part 4: Docker Networks](1212448.html) **→**

References
==========

1. [https://www.homeautomationguy.io/home-assistant-tips/installing-docker-home-assistant-and-portainer-on-ubuntu-linux/](https://www.homeautomationguy.io/home-assistant-tips/installing-docker-home-assistant-and-portainer-on-ubuntu-linux/)
2. [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
3. [https://docs.docker.com/compose/compose-file/](https://docs.docker.com/compose/compose-file/)
4. [https://docs.portainer.io/user/home](https://docs.portainer.io/user/home)
5. [https://www.homeautomationguy.io/home-assistant-tips/using-visual-studio-to-edit-your-home-assistant-configuration-yaml-file-over-ssh/](https://www.homeautomationguy.io/home-assistant-tips/using-visual-studio-to-edit-your-home-assistant-configuration-yaml-file-over-ssh/)