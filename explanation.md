# Welcome to the dockerized Application for the ecommerce website.
 - The Application has been dockerized into containers so that it can run as microservice
 - There is a client container, backend container and a database container which are all in one network called yolomyapp-network 
# Choice of Image for Each Container
- For the client app, I chose to use node:13.12.0-alpine as the base image for the container because it is smaller in size
- For the backend application, I chose to use node:alpine base image so that the container is smaller in size


# Dockerfiles description.
- Two dockerfiles have been created, one for client application and the other for the backend application.
#  Client Dockerfile
- On the client dockerfile, node image is used, a working directory is defined and then dependencies are installed via npm install and then files are copied to the container via COPY command. After that, port 3000 is exposed and finally a command to run the container is defined.
#  Backend Application Dockerfile
- On the backend dockerfile, node alpine image is used, a working directory is defined and then dependencies are installed via npm install and then files are copied to the container via COPY command. After that, port 5000 is exposed and finally a command to run the container is defined.


# Network implementation
- A bridge network has been added to connect the client, backend and database containers so that they can communicate with each other

# Docker-compose volumes
- To handle data persistent , a volume was assigned for the database container and also the backend container.

# Git workflow tasks
- The first task in dockerising the project was to first fork it from the repository. Then a clone in the local machine was done. This followed creation of two dockerfiles; the client dockerfile and backend dockerfile.
- After creating the dockerfile, a docker compose file was created to be able to build the containers
- Each task was followed with a commit back to the github repo.

# Running the Containers
- The containers have been pushed to dockerhub under the below repo:
https://hub.docker.com/repositories/skalume
- The name of the containers are skalume/yoloclientimage for the client app
- The name of the backend container is skalume/yolobackendimage
- and finally the database container is skalume/mongo
Once you clone the project, all containers can be run via docker compose up command and once you browse the site, you can add products. Persistent of data has been achieved via volumes and if the containers go down or are removed, once you bring them up again, you will see the data(products) that you had created.


# Docker image tagging
- All docker images have been tagged with version numbers and follows the naming standards

