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

The playbook can be launched via vagrant up or vagrant provision(once vm is up) and ansible play playbook.rml.
In case Task 4 shows doesnt return a 200, run vagrant provision or try to browse the site via http://192.168.56.10:3000 

The site can be launched via http://192.168.56.10:3000.



# IP 4 STARTS HERE...........
# The below changes of the explanation.md file are for IP4 on Deployment of the App to GKE
# 1. Decisions...
  
  # Choice of the Kubernetes Objects used for deployment (Use of - or the lack of use - of StatefulSets for storage solutions).
  Backend Deployment file(yoloserver-deployment.yml)
  
  The StatefulSet object is used for managing stateful applications and provides guarantees around the order of deployment, scaling, and deletion of pods. In this case, the StatefulSet defines two containers, one for the server and one for MongoDB, and specifies the number of replicas to be 1. It also creates a volume for the MongoDB data and mounts it to the container's file system. This ensures that the MongoDB data is persisted even if the container is restarted or rescheduled to a different node.

  # Method used to expose your pods to internet traffic
  A Service object in Kubernetes provides a stable IP address and DNS name for a set of pods and enables communication with those pods by using a selector. From the client-service.yml file, the selector is set to app: client, which means that this Service object will select all pods with the app: client label.

  The type field in the client-service.yml file is set to LoadBalancer, which instructs Kubernetes to provision an external load balancer that forwards traffic to the pods selected by the Service object. This will expose the pod to the internet, allowing external traffic to access the pod.

  The ports field in the client-service.yml file specifies the port mappings between the Service and the pod. In this case, the http port is mapped to port 80 of the Service and port 3000 of the pod. This means that traffic to port 80 of the Service will be forwarded to port 3000 of the pod.

  # Use-of or there-lack-of of persistent storage
 
  From the backend deployment file(yoloserver-deployment.yml),  PersistentVolumeClaim object is used to request storage resources from the Kubernetes cluster. In this case, it requests 1Gi of storage with ReadWriteOnce access mode, meaning that only one pod can access the volume at a time. The storage class used is "standard", which typically maps to the default storage class of the cluster.
  I used a StatefulSet for managing the deployment of the application as it provides guarantees around the ordering and scaling of pods. The use of a PersistentVolumeClaim ensures that the data is persisted even if the pod is restarted or rescheduled to a different node. 

 # 2. Tasks
   ## task 1 - Creating Google Kubernettes cluster in Google Cloud Platform(GCP)
   The first task involved creating a Kubernetes cluster in GCP so that I could deploy containers and also services

  ## task 2
  Task 2 involved creation of deployment manifest files.

  - Client Deployment file
   The client deployment YAML file(yoloclient-deployment.yml) is a configuration for the client application. It specifies the container image to use, the number of replicas to run, and the container port to expose.
  The deployment is named "yoloclient" and has a single replica. The selector matches the label "app: client", which is also specified in the template metadata. The template specifies the container details, including the name, image, and the port to expose.
  In this case, the client container listens on port 3000 and has two environment variables SERVER_HOST and SERVER_PORT which are set to the IP address and port of the backend server respectively.

  - Client Service file
  This was created to expose client pod to internet traffic via a loadbalancer.
  This YAML file is used to create a Kubernetes Service that exposes a client application to internet traffic.
  It specifies that we are creating a Service resource in Kubernetes and also specifies metadata for the Service resource, such as its name and annotations.
  The file also specifies the type of the Service as LoadBalancer, which means that the Service will be exposed externally using a cloud provider's load balancer.It also specifies the ports that the Service should listen on and forward traffic to.
  port: 80: This specifies the port number that the Service should listen on.
  targetPort: 3000: This specifies the port number that the Service should forward traffic to on the Pods selected by the selector. In this case, it forwards traffic to port 3000 on the client Pods.


  - Backend deployment file
  The backend YAML file (yoloserver-deployment.yml) defines a Kubernetes StatefulSet object that manages a stateful application consisting of two containers: server, and a MongoDB database. The StatefulSet also includes a PersistentVolumeClaim object that defines the storage requirements for the StatefulSet.

  The metadata field of the StatefulSet object contains the name of the StatefulSet, and the spec field defines the desired state of the StatefulSet. The serviceName field in the spec field specifies the name of the Kubernetes Service object that this StatefulSet should be associated with.

  The replicas field in the spec field specifies the desired number of replicas (in this case, 1) of the StatefulSet.
  The selector field in the spec field specifies the labels that are used to select the pods that this StatefulSet object will manage.
  The template field in the spec field specifies the desired state of the pods that this StatefulSet object creates. This includes the labels to be applied to the pods and the container specifications.

  The containers field in the template field specifies the two containers that make up the stateful application. The server container runs an image called skalume/yolobackendimage:v1.0.0, exposes port 5000, and sets an environment variable called MONGODB_URI to connect to the MongoDB container. The mongo container runs the mongo image, exposes port 27017, and mounts the same persistent volume as the server container.
  
  - Backend Service
  This file exposes the backend api so that it can be accessed by the client. I wanted to also use this to test the api from external traffic.
  The metadata field contains the name of the Service object and an annotation describing the purpose of the Service.

  The selector field in the YAML file specifies the labels that are used to select the pods that this Service object will route traffic to. In this case, the selector is set to app: server, which means that this Service object will select all pods with the app: server label.

  The ports field in the YAML file specifies the port mappings between the Service and the pods. In this case, the http port is mapped to port 5000 of both the Service and the pod.

  Finally, the type field in the YAML file is set to LoadBalancer, which instructs Kubernetes to provision an external load balancer that forwards traffic to the selected pods. This will expose the backend service to external traffic, allowing clients to connect to it through the public IP address of the load balancer.


  ## Accessing the Website
  The website can be accessed via http://35.202.191.155:80/


