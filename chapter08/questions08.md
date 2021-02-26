## 8.1. Passing metadata through the Downward API

## 8.1.1. Understanding the available metadata

## 8.1.2. Exposing metadata through environment variables

```
apiVersion: v1
kind: Pod
metadata:
  name: downward
spec:
  containers:
  - name: main
    image: busybox
    command: ["sleep", "9999999"]
    resources:
      requests:
        cpu: 15m
        memory: 100Ki
      limits:
        cpu: 100m
        memory: 4Mi
    ???:
    - name: POD_NAME
      valueFrom:                                   
        fieldRef:                                  
          fieldPath: metadata.name
```          

## 8.1.3. Passing metadata through files in a downwardAPI volume

```
apiVersion: v1
kind: Pod
metadata:
  name: downward
  labels:                                     
    foo: bar                                  
  annotations:                                
    key1: value1                              
    key2: |                                  
      multi                                   
      line                                    
      value                                   
spec:
  containers:
  - name: main
    image: busybox
    command: ["sleep", "9999999"]
    resources:
      requests:
        cpu: 15m
        memory: 100Ki
      limits:
        cpu: 100m
        memory: 4Mi
    ???:                             
    - name: downward                          
      mountPath: /etc/downward                
  volumes:
  - name: downward                            
    ???:                              
      items:
      - path: "podName"                       
        fieldRef:                             
          fieldPath: metadata.name   
```          
