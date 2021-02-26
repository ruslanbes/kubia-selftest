## 15.1.1. Understanding the autoscaling process

Automatic scaling of the number of pod replicas managed by a controller. It’s performed by the Horizontal controller, which is enabled and configured by creating a HorizontalPodAutoScaler (HPA) resource.

***

The Autoscaler doesn’t perform the gathering of the pod metrics itself. It gets the metrics from a different source. As we saw in the previous chapter, pod and node metrics are collected by an agent called cAdvisor, which runs in the Kubelet on each node, and then aggregated by the cluster-wide component called Heapster.

***

This allows the Autoscaler to operate on any scalable resource, as long as the API server exposes the Scale sub-resource for it. Currently, it’s exposed for ReplicationControllers, ReplicaSets, Deployments, StatefulSets

***

Once the Autoscaler has metrics for all the pods belonging to the resource the Autoscaler is scaling, it can use those metrics to figure out the required number of replicas. It needs to find the number that will bring the average value of the metric across all those replicas as close to the configured target value as possible.

***

When autoscaling is based on multiple pod metrics (for example, both CPU usage and Queries-Per-Second [QPS]), the calculation isn’t that much more complicated. The Autoscaler calculates the replica count for each metric individually and then takes the highest value

***

The Autoscaler controller modifies the replicas field of the scaled resource through the Scale sub-resource

## 15.1.2. Scaling based on CPU utilization

As far as the Autoscaler is concerned, only the pod's guaranteed CPU amount (the CPU requests) is important when determining the CPU utilization of a pod.

***

You could prepare and post the YAML manifest for the HPA, but an easier way exists—using the `kubectl autoscale` command.

***

List multiple resource types with `kubectl get` by separating them with comma

***

The autoscaler will at most double the number of replicas in a single operation, if more than two current replicas exist. If only one or two exist, it will scale up to a maximum of four replicas in a single step.

## 15.1.3. Scaling based on memory consumption

Memory-based autoscaling is much more problematic than CPU-based autoscaling. The main reason is there is no automatic way to tell the existing pods to release the memory - has to be done inside an app.

## 15.1.4. Scaling based on other and custom metrics

You have three types of metrics you can use in an HPA object:
- Resource
- Pods
- Object

## 15.1.6. Scaling down to zero replicas

The horizontal pod autoscaler currently doesn’t allow setting the minReplicas field to 0

## 15.2. Vertical pod autoscaling

## 15.2.1. Automatically configuring resource requests

An experimental feature sets the CPU and memory requests on newly created pods, if their containers don’t have them set explicitly. The feature is provided by an Admission Control plugin called InitialResources. When a new pod without resource requests is created, the plugin tries to figure out the requests by looking at historical resource usage

## 15.3. Horizontal scaling of cluster nodes

Kubernetes includes the feature to automatically request additional nodes from the cloud provider as soon as it detects additional nodes are needed. This is performed by the Cluster Autoscaler.

. (intentional break)

.

.

.

.

.

.

.

## 15.3.1. Introducing the Cluster Autoscaler

Cloud providers usually group nodes into groups (or pools) of same-sized nodes (or nodes having the same features). The Cluster Autoscaler thus can’t simply say “Give me an additional node.” It needs also to specify the type.

***

A node will only be returned to the cloud provider if the Cluster Autoscaler knows for sure that the pods running on that node will be rescheduled to other pods

***

`kubectl cordon <node>` marks the node as unschedulable (but doesn’t do anything with pods running on that node).

***

`kubectl drain <node>` marks the node as unschedulable and then evicts all the pods from the node.

## 15.3.3. Limiting service disruption during cluster scale-down

Certain services require that a minimum number of pods always keeps running; this is especially true for quorum-based clustered applications. For this reason, Kubernetes provides a way of specifying the minimum number of pods that need to keep running while performing these types of operations. This is done by creating a `PodDisruptionBudget` resource.
