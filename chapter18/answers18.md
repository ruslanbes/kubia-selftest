## 18.1. Defining custom API objects

## 18.1.1. Introducing CustomResourceDefinitions

    apiVersion: apiextensions.k8s.io/v1beta1       
    kind: CustomResourceDefinition                 
    metadata:
      name: websites.extensions.example.com        
    spec:
      scope: Namespaced                            
      group: extensions.example.com                
      version: v1                                  
      names:                                       
        kind: Website                              
        singular: website                          
        plural: websites


## 18.1.2. Automating custom resources with custom controllers

The API server sends the `ADDED` watch event every time a new Website object is created.

***

My oversimplified controller isn’t implemented properly. The way it watches the API objects doesn’t guarantee it won’t miss individual watch events. The proper way to watch objects through the API server is to not only watch them, but also sync the list periodically to catch the misses in watched objects.

## 18.1.3. Validating custom objects

Is it possible to add validation to the controller and prevent invalid objects from being accepted by the API server? No, API server first adds a resource to etcd and only after that notifies the watchers (controller). It's too late to reject the object.

***

To have the API server validate your custom objects, you need to enable the `CustomResourceValidation` feature gate in the API server

## 18.1.4. Providing a custom API server for your custom objects

To add a custom API server to your cluster, you’d deploy it as a pod and expose it through a Service. Then, to integrate it into the main API server, you’d deploy a YAML manifest describing an APIService resource like the one in the following listing.

    apiVersion: apiregistration.k8s.io/v1beta1   
    kind: APIService                             
    metadata:
      name: v1alpha1.extensions.example.com
    spec:
      group: extensions.example.com              
      version: v1alpha1                          
      priority: 150
      service:                                   
        name: website-api                        
        namespace: default                       

## 18.2. Extending Kubernetes with the Kubernetes Service Catalog

## 18.2.1. Introducing the Service Catalog

Instead of adding custom resources to the API server for each type of service, the Service Catalog introduces the following four generic API resources:

- A ClusterServiceBroker, which describes an (external) system that provisions certain services
- A ClusterServiceClass, which describes a type of service
- A ServiceInstance, which is an individual service object
- A ServiceBinding, which represents a binding of the ServiceInstance to the clients (pods)

## 18.2.2. Introducing the Service Catalog API server and Controller Manager

## 18.2.3. Introducing Service Brokers and the OpenServiceBroker API

The Service Catalog talks to the broker through that API. The API is relatively simple. It’s a REST API providing the following operations:

- Get the list of services with GET /v2/catalog
- Provision a service instance (PUT /v2/service_instances/:id)
- Update a service instance (PATCH /v2/service_instances/:id)
- Bind a service instance (PUT /v2/service_instances/:id/service_bindings/:binding_id)
- Unbind an instance (DELETE /v2/service_instances/:id/service_bindings/:binding_id)
- Unprovision a service instance (DELETE /v2/service_instances/:id)

## 18.2.4. Provisioning and using a service

## 18.2.5. Unbinding and deprovisioning

## 18.2.6. Understanding what the Service Catalog brings

## 18.3. Platforms built on top of Kubernetes

## 18.3.1. Red Hat OpenShift Container Platform

## 18.3.2. Deis Workflow and Helm
Helm is comprised of two things:

 - A `helm` CLI tool (the client).
 - Tiller, a server component running as a pod in a Kubernetes cluster.

Helm application packages are called Charts
