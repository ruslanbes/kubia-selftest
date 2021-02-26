## 7.1. Configuring containerized applications

## 7.2. Passing command-line arguments to containers

## 7.2.1. Defining the command and arguments in Docker

## 7.2.2. Overriding the command and arguments in Kubernetes

## 7.3. Setting environment variables for a container

## 7.3.1. Specifying environment variables in a container definition

## 7.3.2. Referring to other environment variables in a variable’s value

## 7.3.3. Understanding the drawback of hardcoding environment variables

## 7.4. Decoupling configuration with a ConfigMap

## 7.4.1. Introducing ConfigMaps

## 7.4.2. Creating a ConfigMap

## 7.4.3. Passing a ConfigMap entry to a container as an environment variable

```
apiVersion: v1
kind: Pod
metadata:
  name: fortune-env-from-configmap
spec:
  containers:
  - image: luksa/fortune:env
    env:                             
    - name: INTERVAL                 
      v????F???:                     
        ???:             
          name: fortune-config       
          key: sleep-interval        
```

## 7.4.4. Passing all entries of a ConfigMap as environment variables at once

```
spec:
  containers:
  - image: some-image
    envFrom:                      
    - prefix: CONFIG_             
      ???:               
        name: my-config-map
```

***

Did you notice I said two variables, but earlier, I said the ConfigMap has three entries (FOO, BAR, and FOO-BAR)? Why is there no environment variable for the FOO-BAR ConfigMap entry?

???
