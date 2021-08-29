# Basics of Kubernetes and Kind By Example

kubectl - A kubernetes command line tool that allows us to run commands against Kubernetes clusters. We can deploy applications, inspect and monitor the cluster resources and view logs using kubectl.

kind - It is a tool which lets us to run kubernetes on our local computer. The prerequisite for this tool is to have docker installed in our local computer.

## Install Kubectl

1. [macOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)

2. [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)

## Install Kind

### macOS

```sh
$brew install kind
```

### Windows

```sh
$curl.exe -Lo kind-windows-amd64.exe https://kind.sigs.k8s.io/dl/v0.11.1/kind-windows-amd64
$Move-Item .\kind-windows-amd64.exe c:\some-dir-in-your-PATH\kind.exe
```

## Create a Kubernetes cluster
Once kind is installed, create a kubernetes cluster using command
```sh
$kind create cluster --name learning-kubernetes-dev
```

When the cluster is created, the cluster configuration file is stored at the location `${home}/.kube/config`

We can create multiple kubernetes clusters. Let us define one more cluster for **production**
```sh
$kind create cluster --name learning-kubernetes-prod
```

To verify, if cluster has been created, issue the below command.

```sh
$kind get clusters

learning-kubernetes-dev
learning-kubernetes-prod
```

To interact with a particular cluster, we need to specify the `kind-<cluster-name>` as a context in kubectl as shown below.

```sh
➜  $kubectl cluster-info --context kind-learning-kubernetes-dev
Kubernetes control plane is running at https://127.0.0.1:52083
CoreDNS is running at https://127.0.0.1:52083/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

➜  $kubectl cluster-info --context kind-learning-kubernetes-prod
Kubernetes control plane is running at https://127.0.0.1:52449
CoreDNS is running at https://127.0.0.1:52449/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

To verify which context is currently used, we can issue below command.
```sh
$kubectl config get-contexts

CURRENT   NAME                            CLUSTER                         AUTHINFO                        NAMESPACE
          kind-learning-kubernetes-dev    kind-learning-kubernetes-dev    kind-learning-kubernetes-dev
*         kind-learning-kubernetes-prod   kind-learning-kubernetes-prod   kind-learning-kubernetes-prod
```
The * indicates the current cluster. So the current cluster context is **kind-learning-kubernetes-prod**

To switch between the clusters, we can issue below command.
```sh
$kubectl config use-context kind-learning-kubernetes-dev

Switched to context "kind-learning-kubernetes-dev"
```
Let us verify again, this time, the current context is **kind-learning-kubernetes-dev**

```sh
$kubectl config get-contexts
CURRENT   NAME                            CLUSTER                         AUTHINFO                        NAMESPACE
*         kind-learning-kubernetes-dev    kind-learning-kubernetes-dev    kind-learning-kubernetes-dev
          kind-learning-kubernetes-prod   kind-learning-kubernetes-prod   kind-learning-kubernetes-prod
```

## Namespace

The virtual clusters which is created within the physical cluster are called **namespaces**. If we have multiple teams/projects, we can use the same cluster, but for each team/project, we can define the namespaces.

If we do not define any namespaces, then we get a `default` namespace. We can use the below command to see the namespaces.

```sh
kubectl get namespaces

NAME                 STATUS   AGE
default              Active   23m
```

### Create a namespace

We can define multiple namespaces in a kubernetes clusters. In our **learning-kubernetes-dev**, we can have two namespaces. One is used by team-1 and other is used by team-2.

Lets first switch the context to create a namespace in **learning-kubernetes-dev** cluster
```sh
$kubectl config use-context kind-learning-kubernetes-dev
```
create a file called team1-namespace.json with the below content.
```json
{
  "apiVersion": "v1",
  "kind": "Namespace",
  "metadata": {
    "name": "team-1",
    "labels": {
      "name": "team-1"
    }
  }
}
```

Use the below command to create a namespace.

```sh
$kubectl create -f team1-namespace.json

namespace/team-1 created
```

create a file called team2-namespace.json with the below content.
```json
{
  "apiVersion": "v1",
  "kind": "Namespace",
  "metadata": {
    "name": "team-2",
    "labels": {
      "name": "team-2"
    }
  }
}
```
Use the below command to create a namespace.

```sh
$kubectl create -f team2-namespace.json

namespace/team-2 created
```

Use the below command to verify the namespaces.

```sh
$kubectl get namespace

NAME                 STATUS   AGE
default              Active   44m
team-1               Active   2m13s
team-2               Active   18s
```

### Create a Pod in a Namespace

A Kubernetes namespace provides the scope for Pods, Services, and Deployments in the cluster.

let's create a simple Deployment and Pods for both Team-1 and Team-2 namespace. Team-1 need NGINX server of 1 instance and Team-2 needs 2 instances.

```sh
$kubectl create deployment nginx-team-1 --image nginx -n team-1 --replicas=1

deployment.apps/nginx-team-1 created
```

```sh
$kubectl create deployment nginx-team-2 --image nginx -n team-2 --replicas=2
deployment.apps/nginx-team-2 created
```

Lets verify the number of pods created for team-1 and team-2

```sh
kubectl get pods -n team-1

NAME                            READY   STATUS    RESTARTS   AGE
nginx-team-1-544dd7f5bd-qk5g6   1/1     Running   0          113s
```

```sh
kubectl get pods -n team-2

NAME                            READY   STATUS    RESTARTS   AGE
nginx-team-2-55f89d7667-2wd9n   1/1     Running   0          87s
nginx-team-2-55f89d7667-8xd95   1/1     Running   0          87s
```
