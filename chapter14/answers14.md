
## 14.1.1. Creating pods with resource requests

When you don’t specify a request for CPU, you’re saying you don’t care how much CPU time the process running in your container is allotted. In the worst case it doesn't get any CPU time at all

***

In the pod spec, you’re also requesting 10 mebibytes of memory for the container. By doing that, you’re saying that you expect the processes running inside the container to use at most 10 mebibytes of RAM.

## 14.1.2. Understanding how resource requests affect scheduling

When scheduling a pod, the Scheduler will only consider nodes with enough unallocated resources to meet the pod’s resource requirements. If the amount of unallocated CPU or memory is less than what the pod requests, Kubernetes will not schedule the pod to that node, because the node can’t provide the minimum amount required by the pod.

What’s important and somewhat surprising here is that the Scheduler doesn’t look at how much the resources are actually used but at the sum of resources requested by existing pods.

***

Among others, two prioritization functions rank nodes based on the amount of resources requested: `LeastRequestedPriority` and `MostRequestedPriority`. ... The Scheduler is configured to use only one of these functions

***

You may wonder why anyone would want to use the `MostRequestedPriority` function. After all, if you have a set of nodes, you usually want to spread CPU load evenly across them. However, in cloud environment with dynamic number of nodes you may want to minimize the number of nodes to reduce costs.

***

    $ kubectl describe nodes
    Name:       minikube
    ...
    Capacity:                         
      cpu:           2                
      memory:        2048484Ki        
      pods:          110              
    Allocatable:                      
      cpu:           2                
      memory:        1946084Ki        
      pods:          110              


***

The capacity represents the total resources of a node, which may not all be available to pods. Some resources are used by Kubernetes system components. The Scheduler bases its decisions only on the allocatable resource amounts.

## 14.1.3. Understanding how CPU requests affect CPU time sharing

The CPU requests don’t only affect scheduling—they also determine how the unused cpu time is distributed between pods.

***

Because your first pod requested 200 millicores of CPU and the other one 1,000 millicores, any unused CPU will be split among the two pods at 1:5 ratio

...

But if one container wants to use up as much CPU as it can, while the other one is sitting idle at a given moment, the first container will get 100% CPU

***

## 14.1.4. Defining and requesting custom resources

First, you obviously need to make Kubernetes aware of your custom resource by adding it to the Node object’s `capacity` field. The resource name can be anything, such as `example.org/my-resource`, as long as it doesn’t start with the `kubernetes.io` domain. The quantity must be an integer (for example, you can’t set it to 100 millis, because 0.1 isn’t an integer; but you can set it to 1000m or 2000m or, simply, 1 or 2). The value will be copied from the `capacity` to the `allocatable` field automatically.

Then, when creating pods, you specify the same resource name and the requested quantity under the `resource.requests` field in the container spec

## 14.2. Limiting resources available to a container
## 14.2.1. Setting a hard limit for the amount of resources a container can use

CPU is a compressible resource

***

    apiVersion: v1
    kind: Pod
    metadata:
      name: limited-pod
    spec:
      containers:
      - image: busybox
        command: ["dd", "if=/dev/zero", "of=/dev/null"]
        name: main
        resources:             
          limits:              
            cpu: 1             
            memory: 20Mi       

Because you haven’t specified any resource requests, they’ll be set to same values as limits

***

The sum of all limits of all the pods on a node is allowed to be higher than node capacity

## 14.2.2. Exceeding the limits

When a process tries to allocate memory over its limit, the process is killed (it’s said the container is OOMKilled, where OOM stands for Out Of Memory). If the pod’s restart policy is set to `Always` or `OnFailure`, the process is restarted immediately, so you may not even notice it getting killed. But if it keeps going over the memory limit and getting killed, Kubernetes will begin restarting it with increasing delays between restarts. You’ll see a `CrashLoopBackOff` status in that case.

***

On subsequent crashes, this delay is then increased exponentially ..., and finally limited to 300 seconds

## 14.2.3. Understanding how apps in containers see limits

The problem is visible when running Java apps, especially if you don’t specify the maximum heap size for the Java Virtual Machine with the `-XmX` option. In that case, the JVM will set the maximum heap size based on the host’s total memory instead of the memory available to the container.

***

Certain applications look up the number of CPUs on the system to decide how many worker threads they should run. Again, such an app will run fine on a development laptop, but when deployed on a node with a much bigger number of cores, it’s going to spin up too many threads, all competing for the (possibly) limited CPU time. Also, each thread requires additional memory, causing the apps memory usage to skyrocket.

You may want to use the Downward API (or tap into cgroups) to pass the CPU limit to the container and use it instead of relying on the number of CPUs your app can see on the system.

***

## 14.3. Understanding pod QoS classes

Kubernetes does (figuring out who to kill to free up memory) by categorizing pods into three Quality of Service (QoS) classes:
 - `BestEffort` (pods without any requests or limits)
 - `Burstable` (rest)
 - `Guaranteed` (pods where all requests=limits)

## 14.3.1. Defining the QoS class for a pod

For multi-container pods, if all the containers have the same QoS class, that’s also the pod’s QoS class. If at least one container has a different class pod is Burstable

## 14.3.2. Understanding which process gets killed when memory is low

When two single-container pods exist, both in the `Burstable` class, the system will kill the one using more of its requested memory than the other, percentage-wise.

## 14.4. Setting default requests and limits for pods per namespace

It’s a good idea to set requests and limits on every container. (no question here, just a note)

## 14.4.1. Introducing the LimitRange resource

It (LimitRange resource) allows you to specify (for each namespace) not only the minimum and maximum limit you can set on a container for each resource, but also the default resource requests for containers that don’t specify requests explicitly

## 14.4.2. Creating a LimitRange object

## 14.4.3. Enforcing the limits

## 14.4.4. Applying default resource requests and limits

But remember, the limits configured in a LimitRange only apply to each individual pod/container. It’s still possible to create many pods and eat up all the resources available in the cluster

## 14.5. Limiting the total resources available in a namespace

cluster admins also need a way to limit the total amount of resources available in a namespace. This is achieved by creating a ResourceQuota object.

. (intentional blanks)

.

.

.

.

.

.


## 14.5.1. Introducing the ResourceQuota object

    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: cpu-and-mem
    spec:
    hard:
        requests.cpu: 400m
        requests.memory: 200Mi
        limits.cpu: 600m
        limits.memory: 500Mi

***


One caveat when creating a ResourceQuota is that you will also want to create a Limit-Range object alongside it. Otherwise pods that don't specify any request or limit that is handled by ResourceQuota will not get default values and therefore will be automatically rejected

## 14.5.2. Specifying a quota for persistent storage                       

## 14.5.3. Limiting the number of objects that can be created

A ResourceQuota can also be configured to limit the number of Pods, Replication-Controllers, Services, Secrets, Jobs, etc (basically almost any resource type mentioned in previous chapters will work) inside a single namespace.

## 14.5.4. Specifying quotas for specific pod states and/or QoS classes

The quotas you’ve created so far have applied to all pods, regardless of their current state and QoS class. But quotas can also be limited to a set of quota scopes. Four scopes are currently available: BestEffort, NotBestEffort, Terminating, and NotTerminating.

## 14.6. Monitoring pod resource usage

## 14.6.1. Collecting and retrieving actual resource usages

Luckily, the Kubelet itself already contains an agent called cAdvisor, which performs the basic collection of resource consumption data for both individual containers running on the node and the node as a whole

Gathering those statistics centrally for the whole cluster requires you to run an additional component called heapster.
