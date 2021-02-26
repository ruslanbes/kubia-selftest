## 1.1. Understanding the need for a system like Kubernetes

## 1.1.1. Moving from monolithic apps to microservices

## 1.1.2. Providing a consistent environment to applications

## 1.1.3. Moving to continuous delivery: DevOps and NoOps

## 1.2. Introducing container technologies

## 1.2.1. Understanding what containers are

how exactly containers can isolate processes if they’re running on the same operating system. Two mechanisms make this possible.
- L???? N?????????
- c?????? (L???? C?????? G?????)

***

(regarding Linux Namespaces) The following kinds of namespaces exist: ??? (there are 6, name at least 2)

## 1.2.2. Introducing the Docker container platform

A big difference between Docker-based container images and VM images is that container images are composed of ???, which can be shared and reused across multiple images.

***

Three main concepts in Docker comprise this scenario (packaging, distributing, and running applications):
- ??? is something you package your application and its environment into. It contains the filesystem that will be available to the application and other metadata, such as the path to the executable that should be executed
- When you build your image, you can either run it on the computer you’ve built it on, or you can push (upload) the image to a ??? and then pull (download) it on another computer and run it there.
- A running ??? is a process running on the host running Docker, but it’s completely isolated from both the host and all other processes running on it.

***

layers don’t only make distribution more efficient, they also help reduce ??? (what?).

## 1.2.3. Introducing rkt—an alternative to Docker

## 1.3. Introducing Kubernetes

## 1.3.1. Understanding its origins

## 1.3.2. Looking at Kubernetes from the top of a mountain

## 1.3.3. Understanding the architecture of a Kubernetes cluster

At the hardware level, a Kubernetes cluster is composed of many nodes, which can be split into two types:
- The m????? node which hosts the Kubernetes Control Plane that controls and manages the whole Kubernetes system
- W????? nodes that run the actual applications you deploy

***

The Control Plane is what controls the cluster and makes it function. It consists of multiple components that can run on a single master node or be split across multiple nodes and replicated to ensure high availability. These components are
- The Kubernetes A?? ??????, which you and the other Control Plane components communicate with
- The S????????, which assigns a worker node to each deployable component of your application
- The C????????? M??????, which performs cluster-level functions, such as replicating components, keeping track of worker nodes, handling node failures, and so on
- ????, a reliable distributed data store that persistently stores the cluster configuration.

***

The task of running, monitoring, and providing services to your applications is done by the following components:
- c???????? r??????, which runs your containers
- The K??????, which talks to the API server and manages containers on its node
- The k???-?????, which load-balances network traffic between application components

## 1.3.4. Running an application in Kubernetes

## 1.3.5. Understanding the benefits of using Kubernetes
