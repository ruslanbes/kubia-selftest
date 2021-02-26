## 3.1. Introducing pods

The key thing about pods is that when a pod does contain multiple containers, all of them are always run on ???

## 3.1.1. Understanding why we need pods

## 3.1.2. Understanding pods

You want containers inside each group to share certain resources, although not all, so that they’re not fully isolated. Kubernetes achieves this by configuring Docker to have all containers of a pod share the same set of L???? ??????????

***

One thing to stress here is that because containers in a pod run in the same Network namespace, they share the same ?? ??????? and ???? space

***

Communication between pods is always simple. It doesn’t matter if two pods are scheduled ??? or ???; in both cases the containers inside those pods can communicate with each other across the flat NAT-less network

## 3.1.3. Organizing containers across pods properly

You should think of pods as separate machines, but where each one hosts only ???

## 3.2. Creating pods from YAML or JSON descriptors

## 3.2.1. Examining a YAML descriptor of an existing pod

The pod definition consists of a few parts. First, there’s the Kubernetes API version used in the YAML and the type of resource the YAML is describing. Then, three important sections are found in almost all Kubernetes resources:

- ??? includes the name, namespace, labels, and other information about the pod.
- ??? contains the actual description of the pod’s contents, such as the pod’s containers, volumes, and other data.
- ??? contains the current information about the running pod

***

## 3.2.2. Creating a simple YAML descriptor for a pod

## 3.2.3. Using kubectl create to create the pod

## 3.2.4. Viewing application logs

## 3.2.5. Sending requests to the pod

## 3.3. Organizing pods with labels

## 3.3.1. Introducing labels

## 3.3.2. Specifying labels when creating a pod

## 3.3.3. Modifying labels of existing pods

## 3.4. Listing subsets of pods through label selectors

## 3.4.1. Listing pods using a label selector

## 3.4.2. Using multiple conditions in a label selector

## 3.5. Using labels and selectors to constrain pod scheduling

## 3.5.1. Using labels for categorizing worker nodes

## 3.5.2. Scheduling pods to specific nodes
Listing 3.4. Using a label selector to schedule a pod to a specific node: kubia-gpu.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-gpu
spec:
  n???????????:               
    gpu: "true"               
  containers:
  - image: luksa/kubia
    name: kubia
```
## 3.5.3. Scheduling to one specific node  

## 3.6. Annotating pods

## 3.6.1. Looking up an object’s annotations

## 3.6.2. Adding and modifying annotations

You’ll try adding an annotation to your kubia-manual pod now:

```
kubectl ??? pod kubia-manual mycompany.com/someannotation="foo bar"
```

## 3.7. Using namespaces to group resources

## 3.7.1. Understanding the need for namespaces

## 3.7.2. Discovering other namespaces and their pods

## 3.7.3. Creating a namespace

## 3.7.4. Managing objects in other namespaces

## 3.7.5. Understanding the isolation provided by namespaces

## 3.8. Stopping and removing pods

## 3.8.1. Deleting a pod by name

## 3.8.2. Deleting pods using label selectors

## 3.8.3. Deleting pods by deleting the whole namespace

## 3.8.4. Deleting all pods in a namespace, while keeping the namespace

## 3.8.5. Deleting (almost) all resources in a namespace
