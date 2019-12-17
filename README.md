# Golang - vue.js - Docker - Kubernetes

Tutorial about dockerize golang web application with vue.js then deploy it to Kubernetes.

## Part 1 - Dockerize the App
1. [Initiate modules](#initiate-modules)
2. [Build the image](#build-the-image)
3. [Running container in the background](#running-container-in-the-background)

### Initiate modules

Module support is disabled by default for all code inside $GOPATH so that 1.11 behaves like 1.10 did. To enable it, you have to set GO111MODULE=on in your environment.

	export GO111MODULE=on
	go mod init
	go get github.com/labstack/echo
	go get github.com/mattn/go-sqlite3

Then 2 new files have been created in our folder: go.mod and go.sum.

### Build the image

We can now build an image using Dockerfile by execute this command:

	docker build -t govue .

We can now verify that our image exists on our machine by typing this command:

	docker images

### Running container in the background

In order to run this newly created image and if we want to have it run permanently in the background, execute this command:

	docker run -p 8080:8000 -d govue

If we open up http://localhost:8080 within our browser, we should see that our application is successfully running.

In order to view the list of containers running in the background, execute this command:

	docker ps


## Part 2 - Deploy the Dockerize App to Kubernetes
1. [Build the container image](#build-the-container-image)
2. [Upload the container image](#upload-the-container-image)
3. [Run container locally](#run-container-locally)
4. [Create a container cluster](#create-a-container-cluster)
5. [Deploy the application](#deploy-the-application)
6. [Expose the application to the Internet](#expose-the-application-to-the-internet)

### Build the container image

Authenticate by execute this command:

	gcloud auth login

Set the PROJECT_ID environment variable to your Google Cloud project ID. This variable will be used to associate the container image with your project's Container Registry by execute this command:

	export PROJECT_ID=charged-gravity-258513

To build the container image of this application and tag it for uploading, run the following command:

	docker build -t gcr.io/charged-gravity-258513/govue:v1 .

You can run docker images command to verify that the build was successful:

	docker images

### Upload the container image

First, configure Docker command-line tool to authenticate to Container Registry (you need to run this only once):

	gcloud auth configure-docker

You can now use the Docker command-line tool to upload the image to your Container Registry:

	docker push gcr.io/charged-gravity-258513/govue:v1

### Run container locally

To test your container image using your local Docker engine, run the following command:

	docker run --rm -p 8080:8000 gcr.io/charged-gravity-258513/govue:v1

### Create a container cluster

Set your project ID and Compute Engine zone options for the gcloud tool:

	gcloud config set project charged-gravity-258513
	gcloud config set compute/zone asia-east1-a

Run the following command to create a two-node cluster named govue-cluster:

	gcloud container clusters create govue-cluster --num-nodes=2

It may take several minutes for the cluster to be created. Once the command has completed, run the following command and see the cluster's two worker VM instances:

	gcloud compute instances list

### Deploy the application

Pull the image from the Repository and create a Container on the Cluster:

	kubectl run govue-web --image=gcr.io/charged-gravity-258513/govue:v1 --port=8000

To see the Pod created by the Deployment, run the following command:

	kubectl get pods

### Expose the application to the Internet

Expose the Kubernetes Deployment through a Load Balancer:

	kubectl expose deployment govue-web --type=LoadBalancer --port=8080 --target-port=8000

Note: GKE assigns the external IP address to the Service resource—not the Deployment. If you want to find out the external IP that GKE provisioned for your application, you can inspect the Service with the kubectl get service command:

	kubectl get service

If we open up http://35.201.194.137:8080/ within our browser, we should see that our application is successfully running.

## References

https://cloud.google.com/kubernetes-engine/docs/tutorials/hello-app
https://codeburst.io/getting-started-with-kubernetes-deploy-a-docker-container-with-kubernetes-in-5-minutes-eb4be0e96370
