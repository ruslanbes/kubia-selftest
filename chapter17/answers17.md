## 17.1. Bringing everything together

A typical application manifest contains one or more Deployment and/or StatefulSet objects. Those include a pod containing one or more containers, with a liveness probe for each of them and a readiness probe for the service(s) the container provides (if any). Pods that provide services to others are exposed through one or more Services. When they need to be reachable from outside the cluster, the Services are either configured to be LoadBalancer or NodePort-type Services, or exposed through an Ingress resource.

***

The pod templates (and the pods created from them) usually reference two types of Secrets—those for pulling container images from private image registries and those used directly by the process running inside the pods.

***

Secrets are usually assigned to Service-Accounts, which are assigned to individual pods.

***

The application also contains one or more ConfigMaps, which are either used to initialize environment variables or mounted as a `configMap` volume in the pod.

***

Certain pods use additional volumes, such as an `emptyDir` or a `gitRepo` volume, whereas pods requiring persistent storage use `persistentVolumeClaim` volumes

***

DaemonSets aren’t normally part of application deployments, but are usually created by sysadmins to run system services on all or a subset of nodes.

***

The cluster administrator also creates LimitRange and ResourceQuota objects to keep compute resource usage of individual pods and all the pods (as a whole) under control.

## 17.2. Understanding the pod’s lifecycle

## 17.2.1. Applications must expect to be killed and relocated

Using a volume to preserve files across container restarts is a great idea sometimes, but not always. What if the data gets corrupted and causes the newly created process to crash again? This will result in a continuous crash loop (the pod will show the `CrashLoopBackOff` status)

## 17.2.2. Rescheduling of dead or partially dead pods

If you create a ReplicaSet with a desired replica count of three, and then one of the containers in one of those pods starts crashing, Kubernetes will not delete the pod nor reschedule it to another node.

## 17.2.3. Starting pods in a specific order

The Kubernetes API server does process the objects in the YAML/JSON in the order they’re listed, but this only means they’re written to etcd in that order. You have no guarantee that pods will also be started in that order.

***

A pod may have any number of init containers. They’re executed sequentially and only after the last one completes are the pod’s main containers started.

***

An init container defined in a pod

    spec:
      initContainers:                                                        1
      - name: init
        image: busybox
        ...

***

When running the `kubectl logs` command, you need to specify the name of the init container with the `-c` switch (It's the same flag as for normal containers)

***

If an app can’t do its job because one of its dependencies is missing, it should signal that through its readiness probe, so Kubernetes knows it, too, isn’t ready. You’ll want to do this not only because it prevents the app from being added as a service endpoint, but also because it is used by the Deployment controller when performing a rolling update, thereby preventing a rollout of a bad version.

## 17.2.4. Adding lifecycle hooks

Pods also allow you to define two lifecycle hooks:

 - Post-start hooks
 - Pre-stop hooks

These lifecycle hooks are specified per containers, unlike init containers, which apply to the whole pod.

Lifecycle hooks are similar to liveness and readiness probes in that they can either
 - Execute a command inside a container
 - Perform an HTTP GET request against a URL

***

But even though the hook runs asynchronously, it does affect the container in two ways. Until the (Post-start) hook completes, the container will stay in the `Waiting` state with the reason `ContainerCreating`. Because of this, the pod’s status will be `Pending` instead of `Running`. If the hook fails to run or returns a non-zero exit code, the main container will be killed.

***

Sadly, if the process started by the hook logs to the standard output, you can’t see the output anywhere. This makes debugging lifecycle hooks painful. If the hook fails, you’ll only see a `FailedPostStartHook` warning among the pod’s events.

***

A pre-stop hook is executed immediately before a container is terminated. When a container needs to be terminated, the Kubelet will run the pre-stop hook, if configured, and only then send a `SITERM` to the process

***

(When configuring the hook) The `host` field defaults to the pod IP. Be sure not to set it to localhost, because it would refer to the node, not the pod

***

In contrast to the post-start hook, the container will be killed regardless of the pre-stop hook result

***

If the pre-stop hook fails, you’ll see a `FailedPreStopHook` warning event among the pod’s events, but because the pod will be deleted soon, you may not even notice that the pre-stop hook failed to run properly.

***

Many developers make the mistake of defining a pre-stop hook solely to send a `SIGTERM` signal to their apps in the pre-stop hook. They do this because they don’t see their application receive the `SIGTERM` signal sent by the Kubelet. The reason why the signal isn’t received by the application isn’t because Kubernetes isn’t sending it, but because the signal isn’t being passed to the app process inside the container itself. If your container image is configured to run a shell, which in turn runs the app process, the signal may be eaten up by the shell itself, instead of being passed down to the child process.

***

you could configure the container image to not run a shell at all and instead run the application binary directly. You do this by using the exec form of ENTRYPOINT or CMD in the Dockerfile: ENTRYPOINT ["/mybinary"] instead of ENTRYPOINT /mybinary. 

***

## 17.2.5. Understanding pod shutdown

A pod’s shut-down is triggered by the deletion of the Pod object through the API server. Upon receiving an HTTP DELETE request, the API server doesn’t delete the object yet, but only sets a `deletionTimestamp` field in it.

***

Once the Kubelet notices the pod needs to be terminated, it starts terminating each of the pod’s containers. It gives each container time to shut down gracefully, but the time is limited. That time is called the termination grace period and is configurable per pod. The timer starts as soon as the termination process starts. Then the following sequence of events is performed:

 - Run the pre-stop hook, if one is configured, and wait for it to finish.
 - Send the SIGTERM signal to the main process of the container. 
 - Wait until the container shuts down cleanly or until the termination grace period runs out.
 - Forcibly kill the process with SIGKILL, if it hasn’t terminated gracefully yet. 

***

You can force the API server to delete the resource immediately, without waiting for confirmation

    $ kubectl delete po mypod --grace-period=0 --force

Be careful when using this option, especially with pods of a StatefulSet. . The StatefulSet controller takes great care to never run two instances of the same pod at the same time (two pods with the same ordinal index and name and attached to the same Persistent-Volume). By force-deleting a pod, you’ll cause the controller to create a replacement pod without waiting for the containers of the deleted pod to shut down. This may lead to app malfunction.

## 17.3. Ensuring all client requests are handled properly

## 17.3.1. Preventing broken client connections when a pod is starting up

If you don’t specify a readiness probe in your pod spec, the pod is always considered ready. It will start receiving requests almost immediately—as soon as it's added to `iptables` rules on one of the nodes and the first client pod tries to connect to the service

## 17.3.2. Preventing broken connections during pod shut-down

## 17.4. Making your apps easy to run and manage in Kubernetes

## 17.4.1. Making manageable container images

Use the `FROM scratch` directive in the Dockerfile for these images. (those which only host one executable file and don't need any dependencies)

## 17.4.2. Properly tagging your images and using imagePullPolicy wisely

## 17.4.3. Using multi-dimensional instead of single-dimensional labels

## 17.4.4. Describing each resource through annotations

## 17.4.5. Providing information on why the process terminated

The default file the process needs to write the message to is `/dev/termination-log`, but it can be changed by setting the `terminationMessagePath` field in the container definition in the pod spec.

***

If the container doesn’t write the message to any file, you can set the `terminationMessagePolicy` field to `FallbackToLogsOnError`. In that case, the last few lines of the container’s log are used as its termination message (but only when the container terminates unsuccessfully).

## 17.4.6. Handling application logs

If a container crashes and is replaced with a new one, you’ll see the new container’s log. To see the previous container’s logs, use the `--previous` option with `kubectl logs`.

***

(If the log is written to a file then) you can also copy the log file to your local machine using the command `kubectl cp foo-pod:/var/log/foo.log foo.log`

***

The FluentD agent stores each line of the log file as an entry in the ElasticSearch data store. There’s one problem with that. Log statements spanning multiple lines, such as exception stack traces in Java, appear as separate entries in the centralized logging system. To solve this problem, you can have the apps to log in json format (but then it's harder to read the logs directly)

## 17.5. Best practices for development and testing

## 17.5.1. Running apps outside of Kubernetes during development

## 17.5.2. Using Minikube in development

## 17.5.3. Versioning and auto-deploying resource manifests

## 17.5.4. Introducing Ksonnet as an alternative to writing YAML/JSON manifests

## 17.5.5. Employing Continuous Integration and Continuous Delivery (CI/CD)
