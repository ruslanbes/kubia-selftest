## 2.1. Creating, running, and sharing a container image

## 2.1.1. Installing Docker and running a Hello World container

When referring to images without explicitly specifying the tag, Docker will assume you’re referring to the so-called ??? tag

## 2.1.2. Creating a trivial Node.js app

## 2.1.3. Creating a Dockerfile for the image

## 2.1.4. Building the container image

`$ docker build -t kubia .` ... The build process  isn’t performed by the Docker client. Instead, ??? (what happens?).

***

You may think that each Dockerfile creates only a single new layer, but that’s not the case. When building an image, a new layer is created for ???

## 2.1.5. Running the container image

## 2.1.6. Exploring the inside of a running container

## 2.1.7. Stopping and removing a container

## 2.1.8. Pushing the image to an image registry

## 2.2. Setting up a Kubernetes cluster

## 2.2.1. Running a local single-node Kubernetes cluster with Minikube

## 2.2.2. Using a hosted Kubernetes cluster with Google Kubernetes Engine

## 2.2.3. Setting up an alias and command-line completion for kubectl

## 2.3. Running your first app on Kubernetes

## 2.3.1. Deploying your Node.js app

You may be wondering if you can see your container in a list showing all the running containers. Maybe something such as kubectl get containers? Well, that’s not exactly how Kubernetes works. It doesn’t deal with individual containers directly. Instead, it uses the concept of multiple co-located containers. This group of containers is called a ???.

***

To see more information about the pod, you can also use the `kubectl d??????? pod` command,

## 2.3.2. Accessing your web application

## 2.3.3. The logical parts of your system

## 2.3.4. Horizontally scaling the application

## 2.3.5. Examining what nodes your app is running on

## 2.3.6. Introducing the Kubernetes dashboard
