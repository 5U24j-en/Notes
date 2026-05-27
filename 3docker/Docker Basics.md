
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
COPY . /app
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
	- And then create an application on top .of the base image
- #### `docker image inspect <image_id> `
	- You can get complete details of the image
	- Sample
```json
[
    {
        "Id": "sha256:7f554190c23f419360da96249b4ed834b53caf0cdc3232ea721a0d2184c6811c",
        "RepoTags": [
            "myapp:latest"
        ],
        "RepoDigests": [
            "myapp@sha256:7f554190c23f419360da96249b4ed834b53caf0cdc3232ea721a0d2184c6811c"
        ],
        "Comment": "buildkit.dockerfile.v0",
        "Created": "2026-05-21T10:11:39.576141545+05:30",
        "Config": {
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NODE_VERSION=26.2.0"
            ],
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "Cmd": [
                "node",
                "server.js"
            ],
            "WorkingDir": "/app",
            "ArgsEscaped": true
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 445023714,
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:17d38572a7dcb03eff5bfd7354c717d7ee9c69b9d6a29f523722534201e411f4",
                "sha256:47dffecc554065cd728ca9db016fb3b7f3b5577dc8964ac34256e3869ada8db9",
                "sha256:8dc335c5db2d262d9c7a006c747d6102580d3797a3d3fc196b3690e61ba53022",
                "sha256:0e6530456c980dd1b447066f1cb9c2b28e32b3ddde18b48ee25491efb38f0382",
                "sha256:36f8b9c2a40fad592e5ede970f9160aff6a2b9e25b92fe056d777cb8abae641c",
                "sha256:163caf682e98170ebec9be7b2488094b92b8388d7abd34e90971d39e7f68bbfb",
                "sha256:3a2fd11428a5ec6ac86c3cf9de2ccd53bd6b38a2fb1ee0556386e47f9797e8c8",
                "sha256:2dffd4ce18e1add9a729bb4ce33d0e2e2046d0aada23e153b625a4a053b62fa4",
                "sha256:4ca7ddce54ede01df652963f41a2d0d0fcb9df2f6ad41135e1ab961167aff0f9",
                "sha256:eeb419fafa2b57a2ae78fea22577b06616ce94b527884656461c51513c372dac"
            ]
        },
        "Metadata": {
            "LastTagTime": "2026-05-21T04:41:40.127419216Z"
        },
        "Descriptor": {
            "mediaType": "application/vnd.oci.image.index.v1+json",
            "digest": "sha256:7f554190c23f419360da96249b4ed834b53caf0cdc3232ea721a0d2184c6811c",
            "size": 856
        },
        "Identity": {
            "Build": [
                {
                    "Ref": "pf4tt6cne9u3a0dtyotbq4hi1",
                    "CreatedAt": "2026-05-21T10:11:40.409066181+05:30"
                }
            ]
        }
    }
]
```

- ### Dockerfile

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
	
	- **COPY . .**  ( Copying files From code to Container)
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

	- **`CMD ["node", "server.js"]`** ( Executing command when container is created)
		- We need to start the node js server when a container is created to start the application
		- So we use **CMD** and **NOT RUN** , as **RUN** is used to **execute commands to create a Image NOT a container**

	- **EXPOSE 80** ( Exposing the Port)
		- The port in Container is a separate environment from the Host OS
		- so we need to expose the docker port to our OS
		- This is for Documentation purpose , the actual command is done in the shell, runtime

 - #### Creating Image and Container
	 - **Building Image**
		- **CLI**
			- **`docker build .`** - ` . ` means current directory
			- This builds an image
	- **Running Container**
		- **CLI**
			- `docker ps`
				- this will list all the process/containers running in docker
			- `docker run -p 3000:80 <image id>
				- 3000 - Local Port
				- 80  - Docker Port
				- run will run the container
			- HOST PORT : CONTAINER PORT
				   3000   :      80
	- Images are locked once built , every time code changes we need to re build 


- #### Image Layer base Architecture

	- Every instruction in dockerfile represents a layer
	- Each layer is caches once built
	-  If no commands changes then the layers are in the cache are used
	- **If a command changes then all the instructions after the command will be re run and NOT FROM Cache**
		- Other layers after the new command/layer are rebuilt
	- Layers will speed up execution
	- **Image**
		- ![](../attachments/Screenshot%20from%202026-05-21%2010-25-37.png)
	- **Container**
		- Container adds another layer a Container Layer , which is used for running code / executing
		- ![](../attachments/Screenshot%20from%202026-05-21%2010-27-12.png)

- #### Optimization Use Case

	- If our code changes we need to rebuild our image
	- The rebuilding will depend on cache and command
```dockerfile
FROM node
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 80
CMD ["node", "server.js"]
```
- 
	- If our code changes , then the  COPY will be built completely again and the the other commands as well
	- But we DON'T need to install **`npm`** again
	- Instead we can only copy the `package.json` file and then **`RUN npm install`**
	- This will be more efficient and faster

```dockerfile
FROM node
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .  
EXPOSE 80
CMD ["node", "server.js"]
```

- #### Points

	- ![](../attachments/Screenshot%20from%202026-05-21%2010-46-34.png)
	- **The Image is the one that contains the code**
	- **Containers is the running application , IT DOES NOT COPY THE CODE INTO NEW FILE**
		- It will allocate resources and add a execution layer
		- Containers just utilize the code
		- In the image the code is not copied 2 times


## Attached and Detached Containers

- #### Detached Container
	- The output/log of the application is not visible to the Host OS
	- `sudo docker start relaxed_wilson`
		- starting a existing stopped container will be a detached container by default
		- The container was in OS PORT 8000
		- ![](../attachments/Screenshot%20from%202026-05-21%2015-45-58.png)
		- No output in console

- #### Attached Container
	- The output/log of the application will be visible in Host OS Terminal
	- `sudo docker run -p 4000:80 myappg`
		- By default the run command will create a Attached Container
		- ![](../attachments/Screenshot%20from%202026-05-21%2015-47-58.png)
		- ![](../attachments/Screenshot%20from%202026-05-21%2015-48-20.png)
- #### Configuration
	- `sudo docker run -p 4000:80 -d myappg`
		- **` -d `**  config to make the container detached
	- `docker attach <container_name>`
		- For running container, we are attaching converting a detached container to an attached container
	- To get the Logs.
		- `docker logs <container_name>`
			- Only getting the logs and not attaching
		- `sudo docker logs  -f  <eager_swirles>`
			- Getting logs and attaching ( Listening ports)
			- ![](../attachments/Screenshot%20from%202026-05-21%2016-09-14.png)

## Interactive Mode

- Input Application with python , where we need to take input from the terminal/console
```python
from random import randint

min_number = int(input('Please enter the min number: '))
max_number = int(input('Please enter the max number: '))

if (max_number < min_number):
	print('Invalid input - shutting down...')
else:
	rnd_number = randint(min_number, max_number)
	print(rnd_number)
```
- Directly Running will lead to error
	- ![](../attachments/Screenshot%20from%202026-05-21%2016-19-41.png)
- **`sudo docker run -it pyapp:latest `**
	- **` -i `** is interactive
	- **`- t `** will create a terminal
	- ![](../attachments/Screenshot%20from%202026-05-21%2016-22-42.png)
- **`sudo docker start -a -i pyapp:latest`**
	- This is for running a container in interactive mode

## Removing and Deleting Containers and Images

- **Containers**
	- `docker rm <container_name>`
		- Deletes a container
	- **You can NOT remove a running container**
	- Stopping docker
		- `docker stop <container_name>`
	- Removing Multiple containers
		- `docker rm <container1> <container2> .. so on`
	- **Automatically removing containers when stopped** 
		- We need to execute command during run.
		- **`docker run -p 3000:80 -d --rm <container_id>`**
- **Images**
	- `docker images`  lists images
	- `docker rmi <image_id>`
	- **To Remove Images , we have remove all containers of the image EVEN STOPPED CONTAINERS**
	- `docker image prune`
		- Removes all unused images

## Copying Files Into and From a Container

- Copying Files into Container from Host ( example local )
	- `docker cp {from} {to}`
	- So This can work from container to local and from local into container
	- `docker cp dummy/. <container_name>:/test`
- Copying Files into host from Container
	- ` docker cp <container_name>:/test dummy 
	- /test folder and test.txt file (main content) is copied


## Setting Name for Images and Containers

- **Containers**
	- **` docker run -p 3000:30 -d --rm --name <custom_container_name> <image_id> `**
		- **` --rm `** will automatically remove the container once stopped
- Images
	- Image Tags
		-  Image Tags consists of 2 parts a -> ` name or repository` and ` tag `
		- ![](../attachments/Screenshot%20from%202026-05-22%2016-44-41.png)

	- **` docker build -t <image_name>:<image_tag> .  `**

## Sharing Images

- You can share a Dockerfile and the code , and then build images
- OR you can share built images
- ![](../attachments/Screenshot%20from%202026-05-23%2012-38-38.png)
- ### Pushing Images in Dockerhub
	- You can share images by uploading to DockerHub or Private Registry
	- ![](../attachments/Screenshot%20from%202026-05-23%2012-41-24.png)
	- Steps
		- Create a  Repository in dockerhub
		- ![](../attachments/Screenshot%20from%202026-05-23%2012-46-20.png)
		- Rename the local image with repository name and prefix docker-hub account name
		- **5u24j/first-app-push**
		- Rename Command
			- **` docker tag <old_iamge_name> 5u24j/first-app-push `**
			- When you rename a image , a clone/copy with new name is created and the old one still exist
		- Log in your Docker in Local CLI
			- ` docker login `
		- Pushing Image command
			- ` docker push 5u24j/first-app-push `

- ### Pulling and Sharing Images
	- **` docker pull 5u24j/first-app-push `**
	-  The pull always pull the latest image

