Install Docker

Install all required dependency packages.

console
Copy
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
Add the Docker GPG key to your server's keyring.

console
Copy
$ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
Add the latest Docker repository to your APT sources.

console
Copy
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
Update the server package index.

console
Copy
$ sudo apt update
Install Docker.

console
Copy
$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
The above command installs the latest Docker version with the following plugins:

docker-ce: The Docker engine community edition package.
docker-ce-cli: Enables the Docker command line interface (CLI).
containerd.io: A container runtime that monitors the lifecycle of Docker containers.
docker-buildx-plugin: Improves Docker's image-building capabilities for multi-platform builds.
docker-compose-plugin: Enables the management of multi-container Docker applications using YAML files.
View the installed Docker version on your server.

console
Copy
$ sudo docker --version
Output:

Docker version 26.1.4, build 5650f9b
Manage the Docker System Service
Enable the Docker system service to start automatically at boot time.

console
Copy
$ sudo systemctl enable docker
View the Docker service status and verify that it's running.

console
Copy
$ sudo systemctl status docker
Output:

● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: enabled)
     Active: active (running) since Mon 2024-06-17 21:07:25 UTC; 1min 2s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 120312 (dockerd)
      Tasks: 8
     Memory: 34.5M (peak: 35.3M)
        CPU: 281ms
     CGroup: /system.slice/docker.service
             └─120312 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
Run the following command to stop Docker.

console
Copy
$ sudo systemctl stop docker
Restart the Docker service.

console
Copy
$ sudo systemctl restart docker
Run a Containerized Application
Docker runs containerized applications using local or remote container images from registries such as Docker Hub. Follow the steps below to run a sample Nginx containerized application to test Docker on your server.

Pull the latest Nginx image from Docker Hub.

console
Copy
$ sudo docker pull nginx:latest
View all Docker images on the server and verify that the Nginx image is available.

$ sudo docker images
Output:

REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
nginx        latest    dde0cca083bc   2 weeks ago   188MB
Run a new Docker container using the image.

console
Copy
$ sudo docker run --name mynginx -d -p 80:80 nginx:latest
The above command runs a new Docker container on the server using your Nginx image with the following values:

-name mynginx: Sets the container name to mynginx.
d: Starts the container in detached mode as a background process on the server.
p 80:80: Maps the host port 80 to the container port 80. This allows you to access the conatiner using the host port on your server.
nginx:latest: Sets the Docker image to use when creating the container.
List all the running containers on the server and verify that the new container is up.

console
Copy
$ sudo docker ps
Output:

CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                               NAMES
d79cad9ea8d2   nginx:latest   "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   mynewnginx
Allow the HTTP port 80 through the firewall to enable connections to the port.

console
Copy
$ sudo ufw allow 80/tcp
Access your server IP address using a web browser such as Chrome to test access to the Docker container.

http://SERVER-IP
Access the Nginx Docker Container Application

Deploy a Containerized Application to Vultr Container Registry
Docker supports public and private registries to build and deploy container images on your server. The Vultr Container Registry allows you to build Docker images, manage multiple versions, and distribute the images to different environments. Follow the steps below to tag and deploy your local Nginx image to the Vultr Container Registry using Docker.

Open the Vultr customer portal.

Access your Vultr Container Registry management panel and note the credentials to use when accessing the registry.

Access your server terminal session and run the following Docker command to login to the Vultr container registry. Replace samplecontainerregistry, example-user and apikey1234 with your actual registry details.

console
Copy
$ sudo docker login https://sjc.vultrcr.com/samplecontainerregistry -u example-user -p apikey1234
Tag your Nginx Docker image with your Vultr Container Registry.

console
Copy
$ sudo docker tag nginx:latest  sjc.vultrcr.com/samplecontainerregistry/mynginx:latest
View all Docker images on the server and verify that the new image is available.

$ sudo docker images
Output:

REPOSITORY                                              TAG       IMAGE ID       CREATED       SIZE
nginx                                                   latest    dde0cca083bc   2 weeks ago   188MB
sjc.vultrcr.com/samplecontainerregistry/mynginx         latest    dde0cca083bc   2 weeks ago   188MB    
Push the tagged image to your Vultr container registry.

console
Copy
$ sudo docker push sjc.vultrcr.com/samplecontainerregistry/mynginx:latest
Access your Vultr Container Registry management panel.

Navigate to the Repositories tab and verify that the new Docker image is available.

Vultr Container Registry Repository

Run the following command to pull the image from the Vultr Container Registry.

console
Copy
$ sudo docker pull sjc.vultrcr.com/samplecontainerregistry/mynginx:latest


https://docs.vultr.com/how-to-install-docker-on-ubuntu-24-04
