## 10.2. Understanding StatefulSets

## 10.2.1. Comparing StatefulSets with ReplicaSets

unlike pods created by ReplicaSets, pods created by the StatefulSet aren’t exact replicas of each other. Each can have its own set of volumes. Pet pods also have a predictable (and stable) network identity instead of each new pod instance getting a completely random one.

## 10.2.2. Providing a stable network identity

a StatefulSet requires you to create a corresponding governing headless Service that’s used to provide the actual network identity to each pod. Through this Service, each pod gets its own DNS entry, so its peers and possibly other clients in the cluster can address the pod by its hostname.

***

Additionally, you can also use DNS to look up all the StatefulSet’s pods’ names by looking up SRV records for the `foo.default.svc.cluster.local` domain.

***

When a pod instance managed by a StatefulSet disappears (because the node the pod was running on has failed, it was evicted from the node, or someone deleted the pod object manually), the StatefulSet makes sure it’s replaced with a new instance—similar to how ReplicaSets do it. But in contrast to ReplicaSets, the replacement pod gets the same name and hostname as the pod that has disappeared

***

The nice thing about scaling down a StatefulSet is the fact that you always know which pod will be removed. Again, this is also in contrast to scaling down a ReplicaSet

***

Because certain stateful applications don’t handle rapid scale-downs nicely, StatefulSets scale down one-by-one.

## 10.2.3. Providing stable dedicated storage to each stateful instance

Scaling up a StatefulSet by one creates two or more API objects (the pod and one or more PersistentVoulmeClaims referenced by the pod)

***

Scaling down, however, deletes only the pod

## 10.2.4. Understanding StatefulSet guarantees

StatefulSet must be absolutely certain that a pod is no longer running before it can create a replacement pod. This has a big effect on how node failures are handled.

## 10.3. Using a StatefulSet

## 10.3.1. Creating the app and container image

## 10.3.2. Deploying the app through a StatefulSet


Listing 10.3. Three PersistentVolumes: persistent-volumes-gcepd.yaml

```
kind: List                                     
apiVersion: v1
items:
- apiVersion: v1
  kind: PersistentVolume                       
  metadata:
    name: pv-a                                 
  spec:
    capacity:
      storage: 1Mi                             
    accessModes:
      - ReadWriteOnce
    persistentVolumeReclaimPolicy: Recycle     
    gcePersistentDisk:                         
      pdName: pv-a                             
      fsType: nfs4                             
- apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: pv-b
 ...
```

***

You’ll create the StatefulSet now (with 2 pods):
```
$ kubectl create -f kubia-statefulset.yaml
statefulset "kubia" created
```
Now, list your pods:
```
$ kubectl get po
NAME      READY     STATUS              RESTARTS   AGE
kubia-0   0/1       ContainerCreating   0          1s
```
Notice anything strange? Remember how a ReplicationController or a ReplicaSet creates all the pod instances at the same time? Your StatefulSet is configured to create two replicas, but it created a single pod.

Don’t worry, nothing is wrong. The second pod will be created only after the first one is up and ready. StatefulSets behave this way because some clustered stateful apps are sensitive to race conditions


## 10.3.3. Playing with your pods

One useful feature of the API server is the ability to proxy connections directly to individual pods. If you want to perform requests against your kubia-0 pod, you hit the following URL:

    <apiServerHost>:<port>/api/v1/namespaces/default/pods/kubia-0/proxy/<path>

***

Because the API server is secured, sending requests to pods through the API server is cumbersome (among other things, you need to pass the authorization token in each request). Luckily, in chapter 8 you learned how to use `kubectl proxy` to talk to the API server without having to deal with authentication and SSL certificates.

## 10.4. Discovering peers in a StatefulSet

## 10.4.1. Implementing peer discovery through DNS

## 10.5.2. Deleting the pod manually

Suppose the node where `kubia-0` pod was located is down and is not coming back online.

    $ kubectl describe po kubia-0
    Name:        kubia-0
    Namespace:   default
    Node:        gke-kubia-default-pool-32a2cac8-m0g1/10.132.0.2
    ...
    Status:      Terminating (expires Tue, 23 May 2017 15:06:09 +0200)
    Reason:      NodeLost
    Message:     Node gke-kubia-default-pool-32a2cac8-m0g1 which was
                 running pod kubia-0 is unresponsive


Delete the pod the way you’ve always deleted pods:

    $ kubectl delete po kubia-0
    pod "kubia-0" deleted

All done, right? By deleting the pod, the StatefulSet should immediately create a replacement pod, which will get scheduled to one of the remaining nodes. List the pods again to confirm:

    $ kubectl get po
    NAME      READY     STATUS    RESTARTS   AGE
    kubia-0   1/1       Unknown   0          15m
    kubia-1   1/1       Running   0          14m
    kubia-2   1/1       Running   0          13m

That’s strange. You deleted the pod a moment ago and kubectl said it had deleted it. Why is the same pod still there?

The pod is in Terminating state and StatefulSet will not spin up a new pod until the API server receives notification that the pod was terminated (in this case, never). delete pod with `--force` if you don't want to wait
