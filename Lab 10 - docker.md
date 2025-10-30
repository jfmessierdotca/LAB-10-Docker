# Docker

### Introduction

This lab borrows heavily from `https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04`

It is updated for Rapsberry PI Os. All commands have been tested on a PI400 running Bullseye 

## Step 1 — Installing Docker

First, update your existing list of packages:

```bash
sudo apt update
```

Next, remove any previous version of Docker

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

Update the `apt` package index and install packages to allow `apt` to use a repository over HTTPS:

```bash
sudo apt-get install ca-certificates curl gnupg lsb-release
```

Add Docker’s official GPG key:

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

Use the following command to set up the **stable** repository. To add the **nightly** or **test** repository, add the word `nightly` or `test` (or both) after the word `stable` in the commands below. [Learn about **nightly** and **test** channels](https://docs.docker.com/engine/install/).

```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Install Docker engine

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it’s running:

```bash
sudo systemctl status docker
```

The output should be similar to the following, showing that the service is active and running:

```
docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset>
     Active: active (running) since Sat 2022-02-05 15:55:33 GMT; 32s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 3543 (dockerd)
      Tasks: 9
        CPU: 606ms
     CGroup: /system.slice/docker.service
             └─3543 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/cont>
d -H fd:// --containerd=/run/containerd/containerd.sock
```

```Have a screenshot of the status of Docker currently running on a file named screen1.jpg```

Press `q` to exit to the shell prompt. Installing Docker now gives you not just the Docker service (daemon) but also the `docker` command line utility, or the Docker client. We’ll explore how to use the `docker` command later in this tutorial.

## Step 2 — Executing the Docker Command Without Sudo (Optional but recommended)

By default, the `docker` command can only be run the **root** user or by a user in the **docker** group, which is automatically created during Docker’s installation process. If you attempt to run the `docker` command without prefixing it with `sudo` or without being in the **docker** group, you’ll get an output like this:

```bash
docker stats
```

```
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/version": dial unix /var/run/docker.sock: connect: permission denied
```

If you want to avoid typing `sudo` whenever you run the `docker` command, add your username to the `docker` group:

```bash
sudo usermod -aG docker ${USER}
```

To apply the new group membership, log out of the server and back in, or type the following:

```bash
su - ${USER}
```

You will be prompted to enter your user’s password to continue.

Confirm that your user is now added to the **docker** group by typing:

```bash
id -nG
```

It will display something like

```
pi adm dialout cdrom sudo audio video plugdev games users input render netdev lpadmin docker gpio i2c spi
```

If you need to add a user to the `docker` group that you’re not logged in as, declare that username explicitly using:

```bash
sudo usermod -aG docker username
```
Example:

```bash
sudo usermod -aG docker pi
```
The rest of this lab assumes you are running the `docker` command as a user in the **docker** group. If you choose not to, please prepend the commands with `sudo`.

Let’s explore the `docker` command next.

## Step 3 — Using the Docker Command

Using `docker` consists of passing it a chain of options and commands followed by arguments. The syntax takes this form:

```bash
docker [option] [command] [arguments]
```

To view all available subcommands, type:

```bash
docker
```

To view the options available to a specific command, type:

```bash
docker docker-subcommand --help
```

To view system-wide information about Docker, use:

```bash
docker info
```

**Make a screenshot: screen2.jpg**

Let’s explore some of these commands. 

## Step 4 — Working with Docker Images

Docker containers are built from Docker images. By default, Docker pulls these images from [Docker Hub](https://hub.docker.com/), a Docker registry managed by Docker, the company behind the Docker project. Anyone can host their Docker images on Docker Hub, so most applications and Linux distributions you’ll need will have images hosted there.

To check whether you can access and download images from Docker Hub, type:

```bash
docker run hello-world
```

The output will indicate that Docker in working correctly:

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
93288797bd35: Pull complete 
Digest: sha256:507ecde44b8eb741278274653120c2bf793b174c06ff4eaa672b713b3263477b
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

Docker was initially unable to find the `hello-world` image locally, so it downloaded the image from Docker Hub, which is the default repository. Once the image downloaded, Docker created a container from the image and the application within the container executed, displaying the message.

You can search for images available on Docker Hub by using the `docker` command with the `search` subcommand. For example, to search for the Ubuntu image, type:

```bash
docker search mariadb
```

The script will crawl Docker Hub and return a listing of all images whose name match the search string. In this case, the output will be similar to this:

```
NAME                                   DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mariadb                                MariaDB Server is a high performing open sou…   4621      [OK]       
linuxserver/mariadb                    A Mariadb container, brought to you by Linux…   284                  
toughiq/mariadb-cluster                Dockerized Automated MariaDB Galera Cluster …   41                   [OK]
mariadb/server                         MariaDB Server is a modern database for mode…   40                   [OK]
colinmollenhour/mariadb-galera-swarm   MariaDb w/ Galera Cluster, DNS-based service…   34                   [OK]
webhippie/mariadb                      Docker image for mariadb                        23                   [OK]
panubo/mariadb-galera                  MariaDB Galera Cluster                          22                   [OK]
arm64v8/mariadb                        MariaDB Server is a high performing open sou…   22                   
jc21/mariadb-aria                      Extension of the mariadb image that forces a…   21                   
mariadb/maxscale                       MariaDB MaxScale - The world's most advanced…   18                   [OK]
lsioarmhf/mariadb                      ARMHF based Linuxserver.io image of mariadb     17                   
bianjp/mariadb-alpine                  Lightweight MariaDB docker image with Alpine…   16                   [OK]
centos/mariadb-101-centos7             MariaDB 10.1 SQL database server                12                   
wodby/mariadb                          Alpine-based MariaDB container image with or…   6                    [OK]
centos/mariadb-102-centos7             MariaDB 10.2 SQL database server                6                    
clearlinux/mariadb                     MariaDB relational database management syste…   3                    [OK]
tiredofit/mariadb                      Docker MariaDB server w/ S6 Overlay, Zabbix …   3                    [OK]
rightctrl/mariadb                      Mariadb with Galera support                     2                    [OK]
kitpages/mariadb-galera                MariaDB with Galera                             2                    [OK]
centos/mariadb-103-centos7             MariaDB 10.3 SQL database server                2                    
jonbaldie/mariadb                      Fast, simple, and lightweight MariaDB Docker…   2                    [OK]
ansibleplaybookbundle/mariadb-apb      An APB which deploys RHSCL MariaDB              1                    [OK]
jelastic/mariadb                       An image of the MariaDB SQL database server …   0                    
ccitest/mariadb                        CircleCI test images for MariaDB                0                    [OK]
demyx/mariadb                          Non-root Docker image running Alpine Linux a…   0        
```

In the **OFFICIAL** column, **OK** indicates an image built and supported by the company behind the project. Once you’ve identified the image that you would like to use, you can download it to your computer using the `pull` subcommand.

Execute the following command to download the official `ubuntu` image to your computer:

```bash
docker pull jsurf/rpi-mariadb
```

You’ll see the following output:

```
Using default tag: latest
latest: Pulling from jsurf/rpi-mariadb
582c0d8b35e6: Extracting [==================================================>]  38.29MB/38.29MB
c1a13b030fc8: Download complete 
a90c1b2fa966: Download complete 
1882c6e635ad: Download complete 
f150a7025dff: Download complete 
fa622207962a: Download complete 
ef8bcc4a7006: Download complete 
...
Status: Downloaded newer image for jsurf/rpi-mariadb:latest
docker.io/jsurf/rpi-mariadb:latest
```

After an image has been downloaded, you can then run a container using the downloaded image with the `run` subcommand. As you saw with the `hello-world` example, if an image has not been downloaded when `docker` is executed with the `run` subcommand, the Docker client will first download the image, then run a container using it.

To see the images that have been downloaded to your computer, type:

```bash
docker images
```

**Make a screenshot: screen3.jpg.**

The output should look similar to the following:

```
REPOSITORY    			TAG       IMAGE ID       CREATED        SIZE
jsurf/rpi-mariadb   latest    fa021a236bff   8 months ago   318MB
hello-world   			latest    18e5af790473   4 months ago   9.14kB
```

As you’ll see later in this lab, images that you use to run containers can be modified and used to generate new images, which may then be uploaded (*pushed* is the technical term) to Docker Hub or other Docker registries.

Let’s look at how to run containers in more detail.

## Step 5 — Running a Docker Container

The `hello-world` container you ran in the previous step is an example of a container that runs and exits after emitting a test message. Containers can be much more useful than that, and they can be interactive. After all, they are similar to virtual machines, only more resource-friendly.

As an example, let’s run a container using the latest image of Ubuntu. The combination of the **-i** and **-t** switches gives you interactive shell access into the container. You will then run ubuntu on Raspberry PI OS, isn't that cool:

```bash
docker run -it ubuntu
```

Your command prompt should change to reflect the fact that you’re now working inside the container and should take this form:

```
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
bbf2fb66fa6e: Already exists 
Digest: sha256:669e010b58baf5beb2836b253c1fd5768333f0d1dbcb834f7c07a4dc93f474be
Status: Downloaded newer image for ubuntu:latest
root@69b520fcfd66:/# 
```

Note the container id in the command prompt. In this example, it is `d9b100f2f636`. You’ll need that container ID later to identify the container when you want to remove it.

Now you can run any command inside the container. For example, let’s update the package database inside the container. You don’t need to prefix any command with `sudo`, because you’re operating inside the container as the **root** use:

```bash
apt update
```

Then install any application in it. Let’s install Node.js, ***you may need to interact with the update by entering some info***:

```bash
apt install nodejs
```

This installs Node.js in the container from the official Ubuntu repository. When the installation finishes, verify that Node.js is installed:

```bash
node -v
```

**Make a screenshot: screen4.jpg.**

You’ll see the version number displayed in your terminal, like:

```
v12.22.9
```

Any changes you make inside the container only apply to that container.

To exit the container, type `exit` at the prompt.

Let’s look at managing the containers on our system next.

## Step 6 — Managing Docker Containers

After using Docker for a while, you’ll have many active (running) and inactive containers on your computer. To view the **active ones**, use:

```bash
docker ps
```

Copy

You will see output similar to the following:

```
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

In this tutorial, you started two containers; one from the `hello-world` image and another from the `ubuntu` image. Both containers are no longer running, but they still exist on your system.

To view all containers — active and inactive, run `docker ps` with the `-a` switch:

```bash
docker ps -a
```

You’ll see output similar to this:

```
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                      PORTS     NAMES
69b520fcfd66   ubuntu        "bash"                   4 minutes ago    Exited (0) 59 seconds ago             wizardly_pasteur
6450d3472e4b   hello-world   "/hello"                 9 minutes ago    Exited (0) 9 minutes ago              hardcore_meninsky
```

To view the latest container you created, pass it the `-l` switch:

```bash
docker ps -l
```

```bash
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS                          PORTS     NAMES
69b520fcfd66   ubuntu        "bash"                   4 minutes ago    Exited (0) 59 seconds ago             wizardly_pasteur
```

To start a stopped container, use `docker start`, followed by the container ID or the container’s name. Let’s start the Ubuntu-based container with the ID of `e4dcb273b696`:

```bash
docker start 69b520fcfd66
```

The container will start, and you can use `docker ps` to see its status:

```
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS          PORTS     NAMES
69b520fcfd66   ubuntu    "bash"    6 minutes ago   Up 19 seconds             wizardly_pasteur
```

To stop a running container, use `docker stop`, followed by the container ID or name. This time, we’ll use the name that Docker assigned the container, which is `sharp_volhard`:

```bash
docker stop suspicious_hopper
```

Once you’ve decided you no longer need a container anymore, remove it with the `docker rm` command, again using either the container ID or the name. Use the `docker ps -a` command to find the container ID or name for the container associated with the `hello-world` image and remove it.

```bash
docker rm hardcore_meninsky
```

You can start a new container and give it a name using the `--name` switch. You can also use the `--rm` switch to create a container that removes itself when it’s stopped. See the `docker run help` command for more information on these options and others.

Containers can be turned into images which you can use to build new containers. Let’s look at how that works.

## Step 7 — Committing Changes in a Container to a Docker Image

When you start up a Docker image, you can create, modify, and delete files just like you can with a virtual machine. The changes that you make will only apply to that container. You can start and stop it, but once you destroy it with the `docker rm` command, the changes will be lost for good.

This section shows you how to save the state of a container as a new Docker image.

After installing Node.js inside the Ubuntu container, you now have a container running off an image, but the container is different from the image you used to create it. But you might want to reuse this Node.js container as the basis for new images later.

Then commit the changes to a new Docker image instance using the following command.

```bash
docker commit -m "What you did to the image" -a "Author Name" container_id repository/new_image_name
```

The **-m** switch is for the commit message that helps you and others know what changes you made, while **-a** is used to specify the author. The `container_id` is the one you noted earlier in the tutorial when you started the interactive Docker session. Unless you created additional repositories on Docker Hub, the `repository` is usually your Docker Hub username.

For example, for the user **PI**, with the container ID of `69b520fcfd66`, the command would be:

```bash
docker commit -m "added Node.js" -a "pi" 69b520fcfd66 pi/ubuntu-nodejs
```

When you *commit* an image, the new image is saved locally on your computer. Later in this tutorial, you’ll learn how to push an image to a Docker registry like Docker Hub so others can access it.

Listing the Docker images again will show the new image, as well as the old one that it was derived from:

```bash
docker images
```

**Make a screenshot: screen5.jpg**

You’ll see output like this:

```
REPOSITORY         	TAG       IMAGE ID       CREATED          SIZE
pi/ubuntu-nodejs   	latest    5afcab956d06   33 seconds ago   159MB
jsurf/rpi-mariadb    latest    fa021a236bff   8 months ago     318MB
ubuntu             	latest    a457a74c9aaa   3 days ago       65.6MB
hello-world        	latest    18e5af790473   4 months ago     9.14kB
```

In this example, `ubuntu-nodejs` is the new image, which was derived from the existing `ubuntu` image from Docker Hub. The size difference reflects the changes that were made. And in this example, the change was that NodeJS was installed. So next time you need to run a container using Ubuntu with NodeJS pre-installed, you can just use the new image.

You can also build Images from a `Dockerfile`, which lets you automate the installation of software in a new image. However, that’s outside the scope of this tutorial.

Now let’s share the new image with others so they can create containers from it.

## Step 8 — Pushing Docker Images to a Docker Repository

The next logical step after creating a new image from an existing image is to share it with a select few of your friends, the whole world on Docker Hub, or other Docker registry that you have access to. To push an image to Docker Hub or any other Docker registry, you must have an account there.

This section shows you how to push a Docker image to Docker Hub. To learn how to create your own private Docker registry, check out [How To Set Up a Private Docker Registry on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-14-04).

To push your image, first log into Docker Hub, you need to create an account on Docker Hub if you do not have one.

```bash
docker login -u mizonj1
```

You’ll be prompted to authenticate using your Docker Hub password. If you specified the correct password, authentication should succeed.

**Note:** If your Docker registry username is different from the local username you used to create the image, you will have to tag your image with your registry username. For the example given in the last step, you would type:

```bash
docker tag pi/ubuntu-nodejs mizonj1/ubuntu-nodejs
```

Then you may push your own image using: (replace mizonj1 with your own account name)

```bash
docker push mizonj1/ubuntu-nodejs
```

The process may take some time to complete as it uploads the images, but when completed, the output will look like this:

```
Using default tag: latest
The push refers to repository [docker.io/mizonj1/ubuntu-nodejs]
1c4dfeec401b: Pushed 
0c20a4bc193b: Mounted from library/ubuntu 
latest: digest: sha256:83cb2e27169e3d7e4397649d8e04bb7d6e6c07c947e9216a0aaf184421410684 size: 741
```

After pushing an image to a registry, it should be listed on your account’s dashboard, like that show in the image below.

**Make a screenshot: screen6.jpg**

![Docker](/Users/jeromemizon/Documents/8254/22W/Docker.jpg)

If a push attempt results in an error of this sort, then you likely did not log in:

```
The push refers to a repository [docker.io/mizonj1/ubuntu-nodejs]
e3fbbfb44187: Preparing
5f70bf18a086: Preparing
a3b5c80a4eba: Preparing
7f18b442972b: Preparing
3ce512daaf78: Preparing
7aae4540b42d: Waiting
unauthorized: authentication required
```

Log in with `docker login` and repeat the push attempt. Then verify that it exists on your Docker Hub repository page.

You can now use `docker pull mizonj1/ubuntu-nodejs` to pull the image to a new machine and use it to run a new container.

## Step 9 — Using mariadb via Docker

An image is not a running process; it is just the software needed to be launched. To run it, we must create a container first. The command needed to create a container can usually be found in the image documentation. For example, to create a container for the official MariaDB image that you downloaded.`mariadbtest` is the name we want to assign the container. If we don't specify a name, an id will be automatically generated:

Before you run the following command, make sure you stop the `mysql` service if `mysql` is installed on your `Pi`.

```
sudo service mysql stop

docker run --name mariadbtest -e MYSQL_ROOT_PASSWORD=mypass -p 3306:3306 -d jsurf/rpi-mariadb
```

Docker will respond with the container's id. But, just to be sure that the container has been created and is running, we can get a list of running containers in this way:

```
docker ps
```

We should get an output similar to this one:

```
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS          PORTS                                       NAMES
48ed412c8e20   mariadb   "docker-entrypoint.s…"   About a minute ago   Up 55 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   mariadbtest
```

The container can also be stopped like this:

```
docker stop mariadbtest
```

The container will not be destroyed by this command. The data will still live inside the container, even if MariaDB is not running. To restart the container and see our data, we can issue:

```
docker start mariadbtest
```

Docker allows us to restart a container with a single command:

```
docker restart mariadbtest
```

### Connecting to MariaDB from Outside the Container

If we try to connect to the MariaDB server on `localhost`, the client will bypass networking and attempt to connect to the server using a socket file in the local filesystem. However, this doesn't work when MariaDB is running inside a container because the server's filesystem is isolated from the host. The client can't access the socket file which is inside the container, so it fails to connect.

Therefore connections to the MariaDB server must be made using TCP, even when the client is running on the same machine as the server container.

Find the IP address that has been assigned to the container:

```
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mariadbtest
```

```
172.17.0.2
```

You can now connect to the MariaDB server using a TCP connection to that IP address.

To access the container via Bash, we can run this command:

```
docker exec -it mariadbtest bash
```

Now we can use normal Linux commands like *cd*, *ls*, etc. We will have root privileges. We can even install our favorite file editor, for example:

```
apt-get update
apt-get install nano
```

#### Forcing a TCP Connection

After enabling network connections in MariaDB as described above, we will be able to connect to the server from outside the container.

On the host, run the client and set the server address ("-h") to the container's IP address that you found in the previous step:

```
mysql -h 172.17.0.2 -u root -p
```

**Make a screenshot: screen7.jpg.**

The default password is `mypass`

You can now use mysql on your PI without installing it!

## Step 10 — Using the NGINX Open Source Docker Image

You can create an NGINX instance in a Docker container using the [NGINX Open Source image](https://registry.hub.docker.com/_/nginx/) from Docker Hub.

Let’s start with a very simple example. To launch an instance of NGINX running in a container and using the default NGINX configuration, run this command: Download grades

Download a CSV containing students' assignment repository information and their autograding results if applicable.

```
docker run --name mynginx1 -p 8080:80 -d nginx
```

You will see an output like this:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
8998bd30e6a1: Pull complete 
6fba654dd4ee: Pull complete 
661b4150d3a3: Pull complete 
13b3c8cc8cde: Pull complete 
573040833908: Pull complete 
5de8f8b958d2: Pull complete 
Digest: sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767
Status: Downloaded newer image for nginx:latest
d5f7f4121fe7a724db3656b155d9e6dbcf2e23eced927f751f11945851ae121e
```

This command creates a container named **mynginx1** based on the NGINX image. The command returns the long form of the container ID, which is used in the name of log files; see [Managing Logging](https://www.nginx.com/blog/deploying-nginx-nginx-plus-docker/#docker-logging).

The `-p` option tells Docker to map the port exposed in the container by the NGINX image – port 80 – to the specified port on the Docker host. The first parameter specifies the port in the Docker host, while the second parameter is mapped to the port exposed in the container.

The `-d` option specifies that the container runs in detached mode, which means that it continues to run until stopped but does not respond to commands run on the command line. In the [next section](https://www.nginx.com/blog/deploying-nginx-nginx-plus-docker/#Working-with-the-NGINX-Docker-Container) we explain how to interact with the container.

To verify that the container was created and is running, and to see the port mappings, we run `docker` `ps`. (We’ve split the output across multiple lines here to make it easier to read.)

```
docker ps
```

```
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS          PORTS                                       NAMES
d5f7f4121fe7   nginx     "/docker-entrypoint.…"   About a minute ago   
Up 42 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp       mynginx1

48ed412c8e20   mariadb   "docker-entrypoint.s…"   17 minutes ago       Up 13 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp  
```

The `PORTS` field in the output reports that port 8080 on the Docker host is mapped to port 80 in the container. Another way to verify that NGINX is running is to make an HTTP request to that port 8080.  The code for the default NGINX welcome page appears:

```
curl http://localhost:8080

```

**Make a screenshot: screen8.jpg.**

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
 body {
 width: 35em;
 margin: 0 auto;
 font-family: Tahoma, Verdana, Arial, sans-serif;
 }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="https://www.nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```



Upload all screenshots to Github. Also put the Github URL of your repo in the Brightspace Activity for this lab.
