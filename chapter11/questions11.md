## 11.1. Understanding the architecture

The Control Plane is what controls and makes the whole cluster function. To refresh your memory, the components that make up the Control Plane are:

??? (4 components)

***

The task of running your containers is up to the components running on each worker node:

??? (3 components)

***

Beside the Control Plane components and the components running on the nodes, a few add-on components are required for the cluster to provide everything discussed so far. This includes

??? (5 components, name at least 3)

## 11.1.1. The distributed nature of Kubernetes components

Checking the status of the Control Plane components

You can list the components and their statuses with kubectl: `$ kubectl get ???`

## 11.1.2. How Kubernetes uses etcd

The only component that talks to etcd directly is the ???. All other components read and write data to etcd indirectly through it.

***

Optimistic concurrency control (sometimes referred to as optimistic locking) is a method where instead of locking a piece of data and preventing it from being read or updated while the lock is in place, the piece of data includes ??? (what?).

***

All Kubernetes resources include a `metadata.r???????V??????` field, which clients need to pass back to the API server when updating an object.

***

As I’m writing this, Kubernetes can use either etcd version 2 or version 3, but version 3 is now recommended because of improved performance. etcd v2 stores keys in a ???, which makes key-value pairs similar to ??? in a ???. Each key in etcd is either a ???, which contains other keys, or is a regular key with a corresponding value. etcd v3 doesn’t support ???, but because the key format remains the same (keys can include slashes), you can still think of them as being grouped into ???.

## 11.1.3. What the API server does

If the request is trying to create, modify, or delete a resource, the request is sent through ??? Control.


## 11.1.4. Understanding how the API server notifies clients of resource changes

The API server doesn’t do anything else except what we’ve discussed. For example, it doesn’t create pods when you create a ReplicaSet resource and it doesn’t manage the endpoints of a service. That’s what controllers in the ??? do.

But the API server doesn’t even tell these controllers what to do. All it does is enable those controllers and other components to ??? to deployed resources.

***

For example, when deploying a pod, you don’t need to constantly poll the list of pods by repeatedly executing kubectl get pods. Instead, you can use the `--???` flag and be notified of each creation, modification, or deletion of a pod

## 11.1.5. Understanding the Scheduler

The Scheduler doesn’t instruct the selected node to run the pod. All the Scheduler does is update the ??? through the API server. The API server then notifies the ??? (again, through the watch mechanism described previously) that the pod has been scheduled

## 11.1.6. Introducing the controllers running in the Controller Manager

The single Controller Manager process currently combines a multitude of controllers performing various reconciliation tasks. Eventually those controllers will be split up into separate processes, enabling you to replace each one with a custom implementation if necessary. The list of these controllers includes the

??? (name at least 3)

## 11.1.7. What the Kubelet does

In a nutshell, the Kubelet is the component responsible for everything running on a worker node. Its initial job is to register the node it’s running on by creating a ??? (what? where?).
