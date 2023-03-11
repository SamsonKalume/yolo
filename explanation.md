# Welcome to the dockerized Application for the ecommerce website.
 - The Application has been dockerized into containers so that it can run as microservice
 - There is a client container, backend container and a database container which are all in one network called yolomyapp-network 
# Choice of Image for Each Container
- For the client app, I chose to use node:13.12.0-alpine as the base image for the container because it is smaller in size
- For the backend application, I chose to use node:alpine base image so that the container is smaller in size


Dockerfile directives used in the creation and running of each container.
Docker-compose Networking (Application port allocation and a bridge network implementation) where necessary.
Docker-compose volume definition and usage (where necessary).
Git workflow used to achieve the task.
Successful running of the applications and if not, debugging measures applied.
Good practices such as Docker image tag naming standards for ease of identification of images and containers. 