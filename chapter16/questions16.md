## 16.1. Using taints and tolerations to repel pods from certain nodes

Node selectors and node affinity rules make it possible to select which nodes a pod ??? by specifically adding that information to the pod, whereas taints allow ??? deployment of pods to certain nodes

## 16.1.1. Introducing taints and tolerations

Taints have a k??, v????, and an e?????, and are represented as <k??>=<v????>:<e?????>

***

Each taint has an effect associated with it. Three possible effects exist:
- N?????????
- P???????????????
- N????????

## 16.1.2. Adding custom taints to a node

To add a taint, you use the command:

`kubectl ??? node node1.k8s node-type=production:NoSchedule`

## 16.1.3. Adding tolerations to pods

 A production Deployment with a toleration: production-deployment.yaml 

    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: prod
    spec:
      replicas: 5
      template:
        spec:
          ...
          t??????????:
          - ???: node-type         
            ???: Equal        
            ???: production      
            ???: NoSchedule     

## 16.1.4. Understanding what taints and tolerations can be used for

## 16.2. Using node affinity to attract pods to certain nodes

## 16.2.1. Specifying hard node affinity rules

A pod using a nodeAffinity rule

    apiVersion: v1
    kind: Pod
    metadata:
      name: kubia-gpu
    spec:
      ????????:
        n???????????:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeS???????T????:
            - matchExpressions:
              - key: gpu
                operator: In
                values:
                - "true"

## 16.2.2. Prioritizing nodes when scheduling a pod

Making affinity not a hard rule:

    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:    
      - ???: 80                                        
        ???:                                       
          matchExpressions:                               
          - key: availability-zone                        
            operator: In                                  
            values:
            - zone1

***

Out of the five pods that were created, four of them landed on node1 and only one landed on node2. Why did one of them land on node2 instead of node1? The reason is that besides the node affinity prioritization function, the Scheduler also uses other prioritization functions to decide where to schedule a pod. One of those is the Selector-SpreadPriority function, which ??? (does what approximately?)

## 16.3. Co-locating pods with pod affinity and anti-affinity

## 16.3.1. Using inter-pod affinity to deploy pods on the same node

You’ll deploy a backend pod and five frontend pod replicas with pod affinity configured so that they’re all deployed on the same node as the backend pod. 

Pod using podAffinity: frontend-podaffinity-host.yaml

    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: frontend
    spec:
      replicas: 5
      template:
        ...
        spec:
          affinity:
            podAffinity                                        
              requiredDuringSchedulingIgnoredDuringExecution:   
              - t???????K??: kubernetes.io/hostname             
                labelSelector:                                  
                  matchLabels:                                  
                    app: backend                 

***

Instead of the simpler `matchLabels` field, you could also use the more expressive `matchE??????????` field.

***

All the frontend pods were indeed scheduled to the same node as the backend pod. When scheduling the frontend pod, the Scheduler first found all the pods that match the labelSelector defined in the frontend pod’s podAffinity configuration and then scheduled the frontend pod to the same node.

What’s interesting is that if you now delete the backend pod, the Scheduler will ??? (do what?)

## 16.3.2. Deploying pods in the same rack, availability zone, or geographic region

## 16.3.3. Expressing pod affinity preferences instead of hard requirements

## 16.3.4. Scheduling pods away from each other with pod anti-affinity

You may want to keep pods away from each other. This is called pod anti-affinity. It’s specified the same way as pod affinity, except that you use the `???` property
