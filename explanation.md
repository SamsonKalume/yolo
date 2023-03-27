# Welcome to the dockerized Application for the ecommerce website.
 - The Application has been dockerized into containers so that it can run as microservice
 - There is a client container, backend container and a database container which are all in one network called yolomyapp-network 
# Choice of Image for Each Container
- For the client app, I chose to use node:13.12.0-alpine as the base image for the container because it is smaller in size
- For the backend application, I chose to use node:alpine base image so that the container is smaller in size


# Dockerfiles description.
- Two dockerfiles have been created, one for client application and the other for the backend application.
##  Client Dockerfile
- On the client dockerfile, node image is used, a working directory is defined and then dependencies are installed via npm install and then files are copied to the container via COPY command. After that, port 3000 is exposed and finally a command to run the container is defined. Below is the client dockerfile:

FROM node:13.12.0-alpine 
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY .  .
#RUN npm run build
EXPOSE 3000

CMD ["npm","run", "start"]
##  Backend Application Dockerfile
- On the backend dockerfile, node alpine image is used, a working directory is defined and then dependencies are installed via npm install and then files are copied to the container via COPY command. After that, port 5000 is exposed and finally a command to run the container is defined. Below is the definition of the backend application dockerfile:

FROM node:alpine 
WORKDIR /app
COPY package.json package-lock.json*. /
RUN npm install
COPY .  .
EXPOSE 5000
CMD ["npm", "start"]

# Network implementation
- A bridge network has been added to connect the client, backend and database containers so that they can communicate with each other

# Docker-compose volumes
- To handle data persistent , a volume was assigned for the database container and also the backend container.

# Git workflow tasks
- The first task in dockerising the project was to first fork it from the repository. Then a clone in the local machine was done. This followed creation of two dockerfiles; the client dockerfile and backend dockerfile.
- After creating the dockerfile, a docker compose file was created to be able to build the containers
- Each task was followed with a commit back to the github repo after a change.

# Running the Containers
- The containers have been pushed to dockerhub under the below repo:
https://hub.docker.com/repositories/skalume
- The name of the containers are skalume/yoloclientimage for the client app
- The name of the backend container is skalume/yolobackendimage
- and finally the database container is skalume/mongo
Once you clone the project, all containers can be run via docker compose up command and once you browse the site, you can add products. Persistent of data has been achieved via volumes and if the containers go down or are removed, once you bring them up again, you will see the data(products) that you had created.


# Docker image tagging
- All docker images have been tagged with version numbers and follows the naming standards



# The below changes of the explanation.md file are for IP3 on ansible automation - Configuration Management
# IP 3 involved the below tasks:

# Creation of Vagrant File to Power a VM
A vagrant file was created with an ubuntu Box and a defined IP Address. Ansible has been set as the provisioner via a file called playbook.yml.
The file is as definition below:

        Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"  
  config.vm.hostname = "yoloappvm"
  config.vm.provision "ansible", playbook: "playbook.yml"
  config.vm.network "private_network", ip:"192.168.56.10"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end
end

Its configured on a private network and virtual box as the provider. 

# Creation of Inventory.ini File
An inventory file containing the hosts to be used in the playbook have been defined

# Creation of Ansible Playbook File
An ansible playbook file has been created for the project to automate a number of tasks. In the ansible file, I have defined some variables that are used under the vars keyword.
These variables include the github Repo path, the root path, the repo branch, the Ip and a role.
These variables have been defined so that they can be referenced in the tasks that follow.

The Ansible playbook is to run Yolo application from a GitHub repository using Docker. The playbook defines several variables, including the URL of the app repository, the branch to clone, the root directory of the app, the name of the virtual machine, the IP address of the network, and the name of the role.

The playbook then contains several tasks that perform the following actions:

 - Clone the Yolo App code from the defined GitHub repository.
 - Install Docker and Docker Compose on the machine.
 - Build and run Docker containers using the docker-compose YAML file in the app's root directory.
 - Test whether the app can be accessed on the browser by sending a GET request to the specified IP address and port.
 - Display the response of the GET request for troubleshooting purposes.

# The Ansible Playbook tasks
# Task 1
The first task that the playbook is automating is cloning the project from Github repo to the vagrant VM, using git command
The repo path is specified, the destination and also the branch is specified.

# Task 2
The second automation task is setting up Docker, installing docker and docker compose and also setting up a docker repository
This task installs Docker and Docker Compose on the remote host using the apt_key and apt_repository modules to add the Docker GPG key and repository, and the apt module to install docker-ce.

# Task 3
Once Docker has been set up we Build and Run the containers using the docker-compose yml file in the project(the file that was created in IP2 - client docker image, backend docker image and mongoDB images are created )
This task uses the docker command to build and run the Docker containers for the Yolo app using the docker-compose.yml file in the app_root_dir directory.
This is via a command docker compose up, which builds the client, backend and mongodb containers
command: docker compose up -d
      args:
        chdir: "{{ app_root_dir }}" 
I also specify the directory where the docker compose file is located so that the command picks it and runs it.



# task 4
Once the containers are run, i included a task to test the app(Test to see if the App can open on browser after settig up containers), to see if the app is running on the browser.
This task uses the uri module to test if the app is running by accessing its URL in the browser. The URL is constructed using the IP address specified in the vm_network_ip variable and the default port 3000.

I point it to the client URL, and register an output which i print it in the next task


# Task 5
This is just to debug the previous Task, which is mainly to display the Response from the above test
This task prints out the content of the response received from the app's URL in the previous task. The app_response variable is registered in the uri task and then accessed using the debug module.

The playbook can be launched via vagrant up or vagrant provision(once vm is up) and ansible play playbook.rml






