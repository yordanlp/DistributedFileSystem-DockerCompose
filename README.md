# Project description

Using this project you should be able to spin up a simple distributed file system (similar to Google File System implementation) that is implemented using 3 components.

[Master Server](https://github.com/yordanlp/DistributedFileSystem-MasterServer): Is the one in charge to store and manage the metadata of the file like what files are saved in the system, where are the chunks and what chunk servers are registered.

[Chunk Server](https://github.com/yordanlp/DistributedFileSystem-Client): This piece is in charge of storing the chunks on its local file system and retrieving them when requested.

[Client](https://github.com/yordanlp/DistributedFileSystem-Client): This is used to expose the functionality of the system via HTTP exposing method to save, read, delete and get the size of a file

## Notes

The file system is configured to replicate the file to multiple chunk servers, to add redundancy and support the failure of one or more replicas of the chunk servers. In this case is configured to replicate 3 times each chunk, so if one chunk server is stopped the system should be able to read the file if the chunk server that is down is not the only one that has one of the parts of the file.

Here we can see that each ChunkNumber is repeated 3 times and the goal is that each ChunkNumber appears on at least two or more different chunk servers so if one is down the other can respond to the request
![image](https://github.com/yordanlp/DistributedFileSystem-DockerCompose/assets/56166215/849f87fe-47b9-47fa-8b43-df20d1d6672b)

If we have all services running then we can see that the request only takes a few miliseconds to run and runs successfully
![image](https://github.com/yordanlp/DistributedFileSystem-DockerCompose/assets/56166215/48316235-05db-4a7a-b4c6-b40e945795c2)

But if one of the services is down
![image](https://github.com/yordanlp/DistributedFileSystem-DockerCompose/assets/56166215/add422c0-c961-4b77-bcdb-0de962177f41)

The request is still successfull but it takes longer beacuse it is trying to reach a server that is not responding but it manages to get the result from another server
![image](https://github.com/yordanlp/DistributedFileSystem-DockerCompose/assets/56166215/c496061d-c4b8-47a8-b3a0-fa61b850eec9)

So the system is tolerant to failures but it needs to improve in this case the speed of the response, maybe by adding some timeout or periodically checking what chunk servers are down to not send requests to them.

# Docker Compose Instructions

This guide will show you how to run multiple services using Docker Compose, including three API chunk services, a master server, and a client server.

## Prerequisites

Ensure you have Docker and Docker Compose installed on your system. 

## Getting Started

1. Clone this repository to your local system.

2. Navigate to the root directory of the project (where the `docker-compose.yml` file is located).

3. Run the following command to start the services:

   ```bash
   docker-compose up

4. Configure the load master server to register the chunk servers instances. To do it you have to provide the urls of the hosted instances.

Make POST request to this url where the master server is listening (http://localhost:5005/api/ChunkServers) with the following json. Because each container has a name defined these examples should work as data to pass to the load request.

```json
{
    "host": "http://chunkserver1"
}

{
    "host": "http://chunkserver2"
}

{
    "host": "http://chunkserver3"
}

```

then you can make a GET request to the url http://localhost:5005/api/ChunkServers and you should get the list of chunk servers registered

## Instructions to use the file system

1. Create a file. 

To create a file in the system you should make a POST request to http://localhost:5004/api/File/CreateFile and in the body of the request send the file as form data with the key "file" and the value the actual file you want to save (only text files allowed).

Example using postman: 

![image](https://github.com/yordanlp/DistributedFileSystem-DockerCompose/assets/56166215/0d433742-845a-44f6-8295-0291c8532080)

2. Read file.

To read a file in the system you should make a GET request to http://localhost:5004/api/File/ReadFile/{fileName}

Example:

![image](https://github.com/yordanlp/DistributedFileSystem-DockerCompose/assets/56166215/ce77c472-0a66-4470-a671-067d9a81b010)

3. Get size

To get the size of a file in the system you should make a GET request to http://localhost:5004/api/File/GetSize/{fileName}

Example:

![image](https://github.com/yordanlp/DistributedFileSystem-DockerCompose/assets/56166215/77e9d78a-4016-4b9f-9fbf-b6db3acc9203)

4. Delete file

To delete a file in the system you should make a DELETE request to http://localhost:5004/api/File/DeleteFile/{fileName}

Example:

![image](https://github.com/yordanlp/DistributedFileSystem-DockerCompose/assets/56166215/2651f209-168c-4e02-9564-12b244f6661b)









