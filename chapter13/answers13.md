## 13.1. Using the host node’s namespaces in a pod

## 13.1.1. Using the node’s network namespace in a pod

## 13.1.2. Binding to a host port without using the host’s network namespace

## 13.1.3. Using the node’s PID and IPC namespaces

## 13.2. Configuring the container’s security context

## 13.2.1. Running a container as a specific user

To run a pod under a different user ID than the one that’s baked into the container image, you’ll need to set the pod’s `securityContext.???` property

## 13.2.2. Preventing a container from running as root

you can specify that the pod’s container needs to run as a non-root user, as shown in the following listing.

     apiVersion: v1
     kind: Pod
     metadata:
       name: pod-run-as-non-root
     spec:
       containers:
       - name: main
         image: alpine
         command: ["/bin/sleep", "999999"]
         securityContext:                   
           ???: true               

## 13.2.3. Running pods in privileged mode

## 13.2.4. Adding individual kernel capabilities to a container

## 13.2.5. Dropping capabilities from a container

## 13.2.6. Preventing processes from writing to the container’s filesystem

Let’s imagine you’re running a PHP application with a hidden vulnerability, allowing an attacker to write to the filesystem. The PHP files are added to the container image at build time and are served from the container’s filesystem. Because of the vulnerability, the attacker can modify those files and inject them with malicious code.

These types of attacks can be thwarted by preventing the container from writing to its filesystem, where the app’s executable code is normally stored. This is done by setting the container’s `securityContext.???` property to `true`

## 13.2.7. Sharing volumes when containers run as different users

## 13.3. Restricting the use of security-related features in pods

## 13.3.1. Introducing the PodSecurityPolicy resource
