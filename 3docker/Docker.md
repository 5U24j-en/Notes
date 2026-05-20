
![](../attachments/Screenshot%20from%202026-05-19%2020-06-32.png)

## Containers

  - Isolated Environment which share the same OS Kernels
  -  Containers vs Virtual Machines
	  - VM
		  - Each Virtual Machine has its own OS
		  - Higher Disk Space Usage
		  - VMs take time to boot up
		  - Complete Isolation
		  - ![](../attachments/Screenshot%20from%202026-05-19%2020-12-36.png)
	- Containers
		- Low Disk Usage
		- Faster Boot-up time.
		- Shared OS 
		- ![](../attachments/Screenshot%20from%202026-05-19%2020-13-15.png)
- #### Using Both VMs and Containers
	- ![](../attachments/Screenshot%20from%202026-05-19%2020-17-48.png)

## Docker Image

 - Package, Template ( similar to Blueprint )
 - Containers are the running Instances

## Docker Basics 

  - **Docker Engine is the core runtime that needs to be installed to build and run containers**
    - It includes:
        - **dockerd** → Docker daemon (background service)
        - **Docker CLI (`docker`)** → command-line client
        - Docker APIs
    - This is what actually manages images, containers, networks, and volumes.
- **Linux natively supports Docker because containers use Linux kernel features**
    - Docker relies on:
        - **Namespaces** → process isolation
        - **cgroups** → resource limits (CPU/memory)
        - Union file systems → layered images
    - Since Linux already has these kernel capabilities, Docker containers run **directly on the host OS**, without a VM.
- **Windows/macOS require a Linux environment for Linux containers**
    - Because Docker containers are primarily Linux-based and need a Linux kernel.
    - So Docker Desktop runs a **lightweight Linux VM** in the background.
        - Windows → WSL2 or Hyper-V
        - macOS → lightweight Linux VM (Hypervisor framework)

- #### Basic Walk-through
	- Sample JavaScript Code
		 - ![](../attachments/Screenshot%20from%202026-05-20%2018-49-28.png)
	- package.json
		- This is a **Node.js project manifest file** called `package.json`.
		- Think of it as the **metadata + dependency + command configuration file** for a JavaScript application.
		- When you run:
			- `npm install`
		- npm reads this file and installs everything listed under `dependencies`.
```json
{
	"name": "docker-complete",
	"version": "1.0.0",
	"description": "",
	"main": "index.js",
	"scripts": {
		"test": "echo \"Error: no test specified\" && exit 1"
	},
	"repository": {
		"type": "git",
		"url": "https://git-codecommit.us-east-1.amazonaws.com/v1/repos/docker-complete-guide"
	},
	"author": "",
	"license": "ISC",
	"dependencies": {
		"express": "^4.17.1"
	}
}
```
- 
	- **Dockerfile**
		- Docker File name should have capital D
```dockerfile
FROM node:14
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.mjs"]
```
- 
	- Building an Image
		- `sudo docker build -t myapp:latest .`
			- after `-t` is the image name
	- Running a container
		- `sudo docker run -p 3000:3000 myapp:latest`
			- after `-p` the 3000:3000 means we are opening a port 3000 for our container in localhost
	- Web server application now running
		- ![](../attachments/Screenshot%20from%202026-05-20%2019-00-53.png)
	- Listing Active containers
		- ![](../attachments/Screenshot%20from%202026-05-20%2019-02-41.png)
	- Stopping the container
		- `sudo docker stop modest_spence`

## Images vs Container

- **Images** 
	- Templates / Blueprints for containers
	- Containers + required tools/ runtime
- **Containers**
	- The running unit of the software
	- 1 image can be used to create multiple containers on different machines , servers, etc
	- Containers are the running instance of the application
- ![](../attachments/Screenshot%20from%202026-05-20%2020-08-39.png)
- **Example**
	- Containers can be found in Dockerhub
	- We can run node js container
	- the Node js are maintained by the official devs
	- You can download the image `docker pull node`
	- Run the container `docker run node`
	-  `docker run -it {anything}`
		- This will open the Application shell 
		- Like Node js shell

## Image

- Base Image
	- First you pull a base image like python
	- And then create an application on top of the base image

- #### Dockerfile

	- Dockerfile is **USED** to create a Image
	- A image is a blueprint to create containers
	- **Creating Dockerfile**
```dockerfile
FROM node
WORKDIR /app
COPY . /app
RUN npm install
EXPOSE 80
CMD ["node", "server.js"]
```
- 
	- **FROM node** ( Using Base Image)
		- Building our container from a base Image
		- We have used `node`  BaseImage
		- If this image is available in the dockerhub then it will fetch
	
	- **COPY . .  ( Copying files From code to Container)
		- Which Files from local/code needs to be in the container 
		- First **` . `** is the path outside the image and . means the code is in the same file as the DockerFile
		- Second **` . `** is the path inside the image where the files should be stored, and **` . `**  is the root 
		- **Every Image/Container has its own File System, hidden in a Docker container separate from outside**
			- ![](../attachments/Screenshot%20from%202026-05-20%2022-30-32.png)
		- **`COPY . /app`** , This will create a app directory/folder in the Container if the directory/folder does not exist
	
	- **WORKDIR /app** ( Setting working directory )
		- **Every Image/Container has its own File System, hidden in a Docker container separate from outside**
		- We are setting the /app folder/directory as the **root Directory**
	
	- **RUN npm install** ( Installing in container )
		-  We want to install the `npm` in our container
		- Since we have set the root directory as `/app` , `npm` will be installed in the same directory

	- **NOTE: All the commands in the `Dockerfile` is to create a Image , NOT a Container**

	- **`CMD ["node", "server.js"]`  ( Executing command when container is created)
		- We need to start the node js server when a container is created to start the application
		- So we use **CMD** and **NOT RUN** , as **RUN** is used to **execute commands to create a Image NOT a container**

	- **EXPOSE 80** ( Exposing the Port)
		- The port in Container is a separate environment from the Host OS
		- so we need to expose the docker port to our OS

 - 