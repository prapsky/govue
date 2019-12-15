# Golang - vue.js - Docker

This repository is contains tutorial about dockerize golang webserver.

## Initiate modules

Module support is disabled by default for all code inside $GOPATH so that 1.11 behaves like 1.10 did. To enable it, you have to set GO111MODULE=on in your environment.

	# initiate modules
	export GO111MODULE=on
	go mod init
	go get github.com/labstack/echo
	go get github.com/mattn/go-sqlite3

Then 2 new files have been created in our folder: go.mod and go.sum.

## Build the image

We can now build an image using Dockerfile by execute this command:

	# build the image
	docker build -t govue .

We can now verify that our image exists on our machine by typing this command:

	# verify image exists
	docker images

## Running container in the background

In order to run this newly created image and if we want to have it run permanently in the background, execute this command:

	# running container in the background
	docker run -p 8080:8000 -d govue

If we open up http://localhost:8080 within our browser, we should see that our application is successfully running.

In order to view the list of containers running in the background, execute this command:

	# view the list of containers in the background
	docker ps
