## 9.2.2. Performing a rolling update with kubectl


Pushing updates to the same image tag

You need to be aware that the default `imagePullPolicy` depends on the image tag. If a container refers to the latest tag (either explicitly or by not specifying the tag at all), `imagePullPolicy` defaults to Always, but if the container refers to any other tag, the policy defaults to IfNotPresent.

***

[To perform rolling update on a ReplicationController] All you need to do is tell it which ReplicationController to replace, give a name for the new ReplicationController, and specify the new image. The following listing shows the full command for performing the rolling update.
- `kubectl rolling-update kubia-v1 kubia-v2 --image=luksa/kubia:v2`

***

kubectl created this ReplicationController by copying the kubia-v1 controller and changing the image in its pod template. If you look closely at the controller’s label selector, you’ll notice it has also a `deployment` label

    Selector:   app=kubia,deployment=757d16a0f02f6a5c387f2b5edb62b155

## 9.2.3. Understanding why kubectl rolling-update is now obsolete

What’s so wrong with this process that a better one had to be introduced?

Well, for starters, I, for one, don’t like Kubernetes modifying objects I’ve created. Okay, it’s perfectly fine for the scheduler to assign a node to my pods after I create them, but Kubernetes modifying the labels of my pods and the label selectors of my ReplicationControllers is something that I don’t expect and could cause me to go around the office yelling at my colleagues, “Who’s been messing with my controllers!?!?”

But even more importantly, if you’ve paid close attention to the words I’ve used, you probably noticed that all this time I said explicitly that rolling update is done client-side (by kubectl) so in case of network problems you can end in inconsistent state.

## 9.3. Using Deployments for updating apps declaratively

Using a Deployment instead of the lower-level constructs makes updating an app much easier, because you define the End-state and  let Kubernetes decide how to reach it

## 9.3.1. Creating a Deployment

```
apiVersion: apps/v1beta1          
kind: Deployment                  
metadata:
  name: kubia                     
spec:
  replicas: 3
  template:
    metadata:
      name: kubia
      labels:
        app: kubia
    spec:
      containers:
      - image: luksa/kubia:v1
        name: nodejs
```

***

`kubectl create -f kubia-deployment-v1.yaml --record`

Be sure to include the --record command-line option when creating it. This adds the deployment to the revision history

***

You can use the usual `kubectl get deployment` and the `kubectl describe deployment` commands to see details of the Deployment, but let me point you to an additional command, which is made specifically for checking a Deployment’s status:

`kubectl rollout status deployment kubia`

***

Take note of the names of these pods (created via Deployment). Earlier, when you used a ReplicationController to create pods, their names were composed of the name of the controller plus a randomly generated string (for example, kubia-v1-m33mv). The three pods created by the Deployment include an additional numeric value in the middle of their names. What is that exactly?

The number corresponds to the hash of the Deployment and ReplicaSet

## 9.3.2. Updating a Deployment

Similar to scaling a ReplicationController or ReplicaSet up or down, all you need to do is reference a new image tag in the Deployment’s pod template and leave it to Kubernetes to transform your system so it matches the new desired state.

How this new state should be achieved is governed by the deployment strategy configured on the Deployment itself. The default strategy is to perform a rolling update (the strategy is called `RollingUpdate`). The alternative is the `Recreate` strategy, which removes all old pods and then creates new ones

## 9.3.4. Controlling the rate of the rollout

Two properties affect how many pods are replaced at once during a Deployment’s rolling update. They are `maxSurge` and `maxUnavailable` and can be set as part of the `rollingUpdate` sub-property of the Deployment’s `strategy` attribute,


## 9.3.5. Pausing the rollout process

## 9.3.6. Blocking rollouts of bad versions

The `minReadySeconds` property specifies how long the new pod should return "success" for all readiness probes of its containers.

***

Because the rollout will never continue, the only thing to do now is abort the rollout by undoing it:

`kubectl rollout undo deployment kubia`
