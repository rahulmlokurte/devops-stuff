# Kubernetes Operator

Kubernetes manages whole lifecycle of a Stateless applications. Kubernetes uses the control loop to make sure that the desired and actual state are matching. We can easily scale the application instances. If one instance dies, kubernetes will create a new instance using the control loop.

For stateful applications, we need an human intervention. The databases like Mysql, Postgresql are the stateful apps. If we have multiple instances of those, if one goes does, we cannot replace it with other. There might be a order, in which we stop the instances and recreate an instance.

To automate the stateful applications, kubernetes provides an CRD API which is Custom Resource Definition API. It is used to extend the capabilities of kubernetes to handle the stateful applications. We can create custom K8 components.

Operators can be build using CRD and control loop mechanism. The operator will be written by experts in the specific applications. So, this means, there are already operators built by the community. For more information, visit the website [OperatorHub - The registry for Kubernetes Operator](https://operatorhub.io/)

We can also create our own operator using [Operator SDK](https://sdk.operatorframework.io/)

## Why we cannot use deployment for stateful applications

1. Deployment creates a pods which shares the volumes. When we scale up, deployment api will create pods which random hash which is difficult to track the states of applications.

2. Pods do not have own DNS names, so they are not addressable.

So, we use the statefulset for stateful applications and distributed systems instead of deployments.

## What is the change in statefulset

1. Pods get there own DNS name. So are addressable and cluster can easily communicate with the pod

2. If we scale up the pods, it will be given a name [podname]-[ordinal].

3. If pods is destroyed, it will be recreated using same DNS name.

4. Pods get their own volume.

5. When pods are scaled down, the highest ordinal pod will be destroyed first and it does not delete the attached persistant volume to the pod.
