## 5.1. Introducing services

## 5.1.1. Creating services

If you want all requests made by a certain client to be redirected to the same pod every time, you can set the service’s `s??????A???????` property to `ClientIP`

## 5.1.2. Discovering services

Pods behind this service may come and go, their IPs may change, their number can go up or down, but they’ll always be accessible through the service’s single and constant ???.

***

Connecting to a service can be even simpler than that. You can omit the `svc.cluster.local` suffix and even the ???, when ???

## 5.2. Connecting to services living outside the cluster

## 5.2.1. Introducing service endpoints

Services don’t link to pods directly. Instead, a resource sits in between—the ??? resource.

***

Although the ??? is defined in the service spec, it’s not used directly when redirecting incoming connections. Instead, it is used to build a list of IPs and ports, which is then stored in the Endpoints resource.

## 5.2.2. Manually configuring service endpoints

## 5.2.3. Creating an alias for an external service

To create a service that serves as an alias for an external service, you create a Service resource with the type field set to `E???????N???`.

## 5.3. Exposing services to external clients

You have a few ways to make a service accessible externally: ??? (name at least 2)

## 5.3.1. Using a NodePort service

## 5.3.2. Exposing a service through an external load balancer

## 5.3.3. Understanding the peculiarities of external connections

When an external client connects to a service through the node port (this also includes cases when it goes through the load balancer first), the randomly chosen pod may or may not be ???

## 5.4. Exposing services externally through an Ingress resource

When a client sends an HTTP request to the Ingress, the ??? and ??? determine which service the request is forwarded to

## 5.4.1. Creating an Ingress resource

## 5.4.2. Accessing the service through the Ingress

the Ingress controller didn’t forward the request to the service. It only used it ??? (do what?). Most, if not all, controllers work like this.

## 5.4.3. Exposing multiple services through the same Ingress

## 5.4.4. Configuring Ingress to handle TLS traffic

## 5.5. Signaling when a pod is ready to accept connections

## 5.5.1. Introducing readiness probes

Unlike liveness probes, if a container fails the readiness check, it won’t ???

## 5.5.2. Adding a readiness probe to a pod

## 5.5.3. Understanding what real-world readiness probes should do

When a pod is being shut down, the app running in it usually stops accepting connections as soon as it receives the termination signal. Because of this, you might think you need to make your readiness probe start failing as soon as the shutdown procedure is initiated, ensuring the pod is removed from all services it’s part of. But that’s not necessary, because ???.

## 5.6. Using a headless service for discovering individual pods

## 5.6.1. Creating a headless service

## 5.6.2. Discovering pods through DNS

A headless services still provides ??? across pods, but through the DNS round-robin mechanism instead of through the service proxy.

## 5.6.3. Discovering all pods—even those that aren’t ready

## 5.7. Troubleshooting services
When you’re unable to access your pods through the service, you should start by going through the following list:
- First, make sure you’re connecting to the service’s cluster IP ???, not ???.
- Don’t bother ??? to figure out if the service is accessible.
- If you’ve defined a ???, make sure it’s succeeding; otherwise the pod won’t be part of the service.
- To confirm that a pod is part of the service, examine the corresponding Endpoints object with `kubectl ???`
- If you’re trying to access the service through its FQDN or a part of it (for example, myservice.mynamespace.svc.cluster.local or myservice.mynamespace) and it doesn’t work, see if you can access it using its ??? instead of the FQDN.  
- Check whether you’re connecting to the ??? exposed by the service and not the ???.
- Try connecting to the pod IP directly to confirm your pod is accepting connections on the correct port.
- If you can’t even access your app through the pod’s IP, make sure your app isn’t only binding to ???.
