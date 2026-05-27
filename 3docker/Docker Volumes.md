
## **Types of Data**

- ![](../attachments/Screenshot%20from%202026-05-23%2019-51-47.png)
- #### Application Data
	- Source Code, Environment Data
	- These are only Read Only
	- Container has this only as a layer
- #### Temporary App Data
	- Fetched/Produced in running container
	- Stored in Temporary files

	- **In-memory temporary data (RAM)** 
		- Python variables
		- Objects in memory
		- Cache stored in RAM
		- Active user sessions (if memory-based)
		- Running process state

	- **Temporary filesystem data inside container** 
		- Also includes files written inside the container’s writable layer:
```python
with open("/tmp/report.txt", "w") as f:
    f.write("hello")
    
OR

echo "test" > /app/log.txt
```
- 
	- 
		- This is **not RAM**.
		- It is disk storage inside the container’s writable layer.
		- ![](../attachments/Screenshot%20from%202026-05-23%2019-58-05.png)

- #### Permanent App Data
	- Example User Accounts
	- Files stored in Databases
	- Files that needs to persists even if the containers are stopped

## Container Data

- When the container is stopped and we have stored some files in the container internal system, the files will not we deleted and the files will exist in the container
- If we **REMOVE/ PURGE** the container then the files are deleted permanently 

## Volumes

- Volumes are folders on your host machines hard drives which are mounted into containers
- **` COPY . . `** command is just a 1 time snapshot copy into the host folder, there is NO connection
- Volumes are Folders on your host machine hard-drive which are mounted ( mapped ) to the container
	- ![](../attachments/Screenshot%20from%202026-05-24%2011-31-50.png)
- Any files added to container will be accessible/added to the mounted Volume and any files added to the container will be accessible to the container
- **Volume persists Data and Volume is NOT removed if container is removed**
	- Volumes persist if a container shuts down. If a container (re-)starts and mounts a volume, any data inside of that volume is available in the container.
	- A container can write data into a volume and read data from it.

- **Adding Anonymous Volumes in Dockerfile**
	- **` VOLUME [ "/app/feedback" ] `**
- Viewing Volumes
	- `docker volume ls`

- ### Types of Volumes

	- #### Volumes - Managed By Docker
		- ###### Anonymous Volumes
			- These volumes are created if we define Volume in the Dockerfile 
			- These volumes will be somewhere in the localhost managed by Docker 
			- If we stopped the container , the Volume is gone and deleted
			- **Removing Anonymous Volumes**
				-  If we create anonymous volume without -rm ( automatically removing container after stopping ) the volume will never be accessed
				- If we restart the container , new volume will be created
				- Removing old anonymous volume 
					- ` docker volume rm <VOL_NAME> 
		- ###### Named Volumes
			- The Volumes will still exist even after stopping the container
			- New containers can access the data of old containers
			- But user wont have access to it as the Volume is still managed by Docker
			- Hidden and managed by docker
			- Command to create Named Volume
				- **` docker  run  -d  -p 8000:80  --rm  --name  <container_name>  -v <named_volume>:<destination>  <image_name> `**
				-  **` docker  run  -d  -p 8000:80  --rm  --name  myapp  -v app_data:/app/data my-python-app `**
					- ` myapp ` = container name
					- ` app_data ` = volume name
					- ` /app/data ` = Container internal file system path
					- ` my-python-app ` = Image Name

	- #### Bind Volumes
		- Volumes Managed by the developer
		- USE CASE
			- Now you can edit code locally and container instantly sees changes 
		- The Container File-system is completely mapped to the LocalHost Folder
		- No need for `COPY . .` in Dockerfile since there is live connection
		- Terminal Command
			-  **` docker  run  -d  -p 8000:80  --rm  --name  <container_name> -v "<host_machine_absolute_paht>:<container_workdir_path>"  <image_name> `**
			- ADD -> ` -v "<host_machine_absolute_paht>:<container_workdir_path>" `
		- Using Bind Volumes mean that you need to install the packages in the local Code as well , since Docker is not pulling / installing packages
			- Contents inside The container are overwritten by the contents of the local file
		- NOTE:
			- In Dockerfile for Node application we have ` RUN npm install ` , which installs the **npm modules** into the container
			- But Bind Volumes overwrite the contents in the local file, and the local files do not have **npm modules** installed
			- Hence the container will not work and it will break

	- #### Managing Multiple Volumes in Containers
		- ![](../attachments/Screenshot%20from%202026-05-24%2019-10-06.png)
		- We can have multiple volumes that have different parts of the files
```dockerfile
FROM node
WORKDIR /app
COPY package.json
RUN npm install
COPY . .
EXPOSE 80
CMD ["node", "server.js" ]
```
 - 
	 - 
		- #### Now we will use 3 Volumes
			- Code Structure
				- `feedback` has all the files that are the code creates during runtime
				- ![](../attachments/Screenshot%20from%202026-05-24%2019-26-24.png)
			- **Named Volume**
				- **` node-named-vol:/app/feedback `** - Named Volume which only has the /app/feedback contents, which are created during run time
			- **Bind Volume**
				- **` -v "/home/diablo/Desktop/Docker/Volumns_Nodeapp:/app" `** - Bind Volume which copies the entire codebase from localhost and has live connection
				- So any changes in the code , those changes will be updated in the container
			- **Temporary Volume**
				- In docker , the most absolute path defined will have the precedence of the file structure
				- So in Bind Volume we are just copying  /app , the top level
				- In Temporary we are defining more absolute path /app/node/node_modules, hence that folder will be a temporary volume
				- **` -v /app/node_modules `** - This temporary volume will have the files that the  command `RUN npm install` will have. So now even the npm install files will be there and the container will run perfectly
			- **CODE  :-** ` docker run -d -p 8000:80 --name node-app -v node-named-vol:/app/feedback  -v "/home/diablo/Desktop/Docker/Volumns_Nodeapp:/app"  -v /app/node_modules node-vol-app-image  `
			- Summary
				- ![](../attachments/Screenshot%20from%202026-05-24%2020-36-52.png)
			
	 - **NOTE: If we change backend code , like add extra log event in  server.js , we need to restart container to view the changes**
	 - **Output**
		 - App Input
			 - ![](../attachments/Screenshot%20from%202026-05-24%2020-22-48.png)
			 - After Save , this will create a text file with name `testfile.txt` and the file will have the document text
			 - The `testfile.txt` is stored in the container  file system path `/app/feedback` and named volume `node-named-vol` 
			 - We can access this in URL path -  http://localhost:8000/feedback/hello.txt
				 - ![](../attachments/Screenshot%20from%202026-05-24%2020-26-31.png)
			- Now we can change HTML code ( Since its static file ) and it will reflect live

	- ### Read Only Volumes
		- When we use bind volumes the container should not be able to change the files in Host system
		- By Default containers are Read Write Volumes, containers can read and write to volumes
		-    **` :ro `**    will make the container Read Only
		- **` -v "/home/diablo/Desktop/Docker/Volumns_Nodeapp:/app:ro" `**
		- ` -v /app/temp ` - we need to `server.js` code to write files
		- CODE
			- **` docker run -d -p 8000:80 --name node-app -v node-named-vol:/app/feedback  -v "/home/diablo/Desktop/Docker/Volumns_Nodeapp:/app:ro"  -v /app/temp -v /app/node_modules node-vol-app-image  `**

	- ### Managing Docker Volumes
		- **Creating Volumes on your own**
			- ` docker volume create [OPTIONS] [VOLUME_NAME] `
		 - **Inspecting Volume**
			 - ` docker volume inspect [VOLUME_NAME] `
			 - **OUTPUT**
```json
[
    {
        "CreatedAt": "2026-05-24T20:14:17+05:30",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/node-named-vol/_data",
        "Name": "node-named-vol",
        "Options": null,
        "Scope": "local"
    }
]
```
- 
	- 
		- **Moutpoint**  - Path of volume where it is stored , this path is not in the Host Machine and the path is inside a VM docker setup.
		- **Options** - will show if read only or any other config . null is Default 
	- **Removing Volume**
		- ` docker volume rm [VOLUME_NAME] `
	- **COPY vs Bind Volumes**
		- Bind Volumes are used only in Development
		- In Production we just want a snapshot/Image of the code, as there should be no changes to the code
		- Thats why **` COPY . . `** is needed in production containers
	- **` .dockerignore `**
		- To track files which should NOT be added into the Docker Image

## Environment Variables

- 
