## 6.1. Introducing volumes

## 6.1.1. Explaining volumes in an example

## 6.1.2. Introducing available volume types

## 6.2. Using volumes to share data between containers

## 6.2.1. Using an emptyDir volume

```
apiVersion: v1
kind: Pod
metadata:
  name: fortune
spec:
  containers:
  - image: luksa/fortune                   
    name: html-generator                   
    volumeMounts:                          
    - name: html                           
      mountPath: /var/htdocs               
  - image: nginx:alpine                    
    name: web-server                       
    volumeMounts:                          
    - name: html                           
      mountPath: /usr/share/nginx/html     
      readOnly: true                       
    ports:
    - containerPort: 80
      protocol: TCP
  volumes:                                 
  - name: html                             
    emptyDir: {}
```

***

The emptyDir you used as the volume was created on the actual disk of the worker node hosting your pod, so its performance depends on the type of the node’s disks. But you can tell Kubernetes to create the emptyDir on tmpfs (in Memory)


## 6.2.2. Using a Git repository as the starting point for a volume

## 6.3. Accessing files on the worker node’s filesystem

## 6.3.1. Introducing the hostPath volume

If a pod is deleted and the next pod uses a hostPath volume pointing to the same path on the host, the new pod will see the data from the previous pod, but only if it was scheduled to the same node.

## 6.3.2. Examining system pods that use hostPath volumes

## 6.4. Using persistent storage

## 6.4.1. Using a GCE Persistent Disk in a pod volume

## 6.4.2. Using other types of volumes with underlying persistent storage

## 6.5. Decoupling pods from the underlying storage technology

## 6.5.1. Introducing PersistentVolumes and PersistentVolumeClaims

## 6.5.2. Creating a PersistentVolume

PersistentVolumes don’t belong to any namespace. They’re cluster-level resources like nodes.  

## 6.5.3. Claiming a PersistentVolume by creating a PersistentVolumeClaim

## 6.5.4. Using a PersistentVolumeClaim in a pod

## 6.5.5. Understanding the benefits of using PersistentVolumes and claims

## 6.5.6. Recycling PersistentVolumes

## 6.6. Dynamic provisioning of PersistentVolumes

## 6.6.1. Defining the available storage types through StorageClass resources

## 6.6.2. Requesting the storage class in a PersistentVolumeClaim

## 6.6.3. Dynamic provisioning without specifying a storage class

```
kind: PersistentVolumeClaim
spec:
  storageClassName: ""  
```

Specifying an empty string as the storage class name ensures the PVC binds to a pre-provisioned storage instead of provisioning a new one.  
