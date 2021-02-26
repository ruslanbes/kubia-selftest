## 4.1. Keeping pods healthy

## 4.1.1. Introducing liveness probes

Kubernetes can probe a container using one of the three mechanisms:
- ???
- ???
- ???

## 4.1.2. Creating an HTTP-based liveness probe

## 4.1.3. Seeing a liveness probe in action

## 4.1.4. Configuring additional properties of the liveness probe

## 4.1.5. Creating effective liveness probes

## 4.2. Introducing ReplicationControllers

## 4.2.1. The operation of a ReplicationController

A ReplicationController’s job is to make sure that ??? always matches its label selector

***

A ReplicationController has three essential parts (also shown in figure 4.3):

- A ???, which determines what pods are in the ReplicationController’s scope
- A ???, which specifies the desired number of pods that should be running
- A ???, which is used when creating new pod replicas

## 4.2.2. Creating a ReplicationController

The pod labels in the template must obviously match the label selector of the ReplicationController; otherwise the controller would create new pods indefinitely, because spinning up a new pod wouldn’t bring the actual replica count any closer to the desired number of replicas. To prevent such scenarios, ??? (who does what?).

## 4.2.3. Seeing the ReplicationController in action

## 4.2.4. Moving pods in and out of the scope of a ReplicationController

What do you think would happen if instead of changing the labels of a pod, you modified the Replication-Controller’s label selector? ???

## 4.2.5. Changing the pod template

## 4.2.6. Horizontally scaling pods

## 4.2.7. Deleting a ReplicationController

When you delete a ReplicationController through `kubectl delete`, ??? (what happens with its pods?)

***

When deleting a ReplicationController with `kubectl delete`, you can ??? (do what?) by passing the `--cascade=false` option to the command.

## 4.3. Using ReplicaSets instead of ReplicationControllers

## 4.3.1. Comparing a ReplicaSet to a ReplicationController

## 4.3.2. Defining a ReplicaSet

## 4.3.3. Creating and examining a ReplicaSet

## 4.3.4. Using the ReplicaSet’s more expressive label selectors

If you specify (in ReplicaSet's yaml) multiple expressions, all those expressions must evaluate to true for the selector to match a pod. If you specify both `m????L?????` and `m???????????????`, all the labels must match and all the expressions must evaluate to true for the pod to match the selector.

## 4.3.5. Wrapping up ReplicaSets

## 4.4. Running exactly one pod on each node with DaemonSets

## 4.4.1. Using a DaemonSet to run a pod on every node

## 4.4.2. Using a DaemonSet to run pods only on certain nodes

Later in the book, you’ll learn that nodes can be made unschedulable, preventing pods from being deployed to them. A DaemonSet will deploy pods even to such nodes, ??? (why?)

## 4.5. Running pods that perform a single completable task

## 4.5.1. Introducing the Job resource

## 4.5.2. Defining a Job resource

In a pod’s specification, you can specify what Kubernetes should do when the processes running in the container finish. This is done through the `r??????P?????` pod spec property, which defaults to `???`

## 4.5.3. Seeing a Job run a pod

## 4.5.4. Running multiple pod instances in a Job

If you need a Job to run more than once, you set `???` to how many times you want the Job’s pod to run

***

By setting `???` to 2, the Job creates two pods and runs them in parallel

## 4.5.5. Limiting the time allowed for a Job pod to complete

## 4.6. Scheduling Jobs to run periodically or once in the future

## 4.6.1. Creating a CronJob

## 4.6.2. Understanding how scheduled jobs are run
