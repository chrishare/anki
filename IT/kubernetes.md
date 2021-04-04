## Steps to containerize application code

Per https://www.digitalocean.com/community/tutorials/modernizing-applications-for-kubernetes

%

1. Remove hardcoded config and secrets
```
DB_HOST = os.environ.get('APP_DB_HOST')
```
and use configmaps and secrets in Kubernetes

2. Become stateless

Don't persist data locally, since the local container could die at any time. Session affinity for in-memory session details is possible but undesireable.

3. Implement healthchecks 

e.g.
```
@app.route('/health')
def return_ok():
    return 'Ok!', 200
```
4. Implement metrics for monitoring

e.g. Implement /metrics for Promethius to scrape

5. Build admin functions as APIs

e.g. Clearing caches is an /api function

6. Explicitly declare dependencies

This allows easier reasoning about how dependencies impact the project, and how to manage them.

7. Implement build pipelines

Builds should be repeatable, and do reasonable testing.

## 12 Factors for Modern Kubernetes Applications

%

Per https://www.digitalocean.com/community/tutorials/architecting-applications-for-kubernetes

A quick summary of the Twelve Factors are:

1. Codebase: Manage all code in version control systems (like Git or Mercurial). The codebase comprehensively dictates what is deployed.
2. Dependencies: Dependencies should be managed entirely and explicitly by the codebase, either vendored (stored with the code) or version pinned in a format that a package manager can install from.
3. Config: Separate configuration parameters from the application and define them in the deployment environment instead of baking them into the application itself.
4. Backing services: Local and remote services are both abstracted as network-accessible resources with connection details set in configuration.
5. Build, release, run: The build stage of your application should be completely separate from your application release and operations processes. The build stage creates a deployment artifact from source code, the release stage combines the artifact and configuration, and the run stage executes the release.
6. Processes: Applications are implemented as processes that should not rely on storing state locally. State should be offloaded to a backing service as described in the fourth factor.
7. Port binding: Applications should natively bind to a port and listen for connections. Routing and request forwarding should be handled externally.
8. Concurrency: Applications should rely on scaling through the process model. Running multiple copies of an application concurrently, potentially across multiple servers, allows scaling without adjusting application code.
9. Disposability: Processes should be able to start quickly and stop gracefully without serious side effects.
10. Dev/prod parity: Your testing, staging, and production environments should match closely and be kept in sync. Differences between environments are opportunities for incompatibilities and untested configurations to appear.
11. Logs: Applications should stream logs to standard output so external services can decide how to best handle them.
12. Admin processes: One-off administration processes should be run against specific releases and shipped with the main process code.

## Kubernetes Overview

Overview

%

Kubernetes, at its basic level, is a system for running and coordinating containerized applications across a cluster of machines. It is a platform designed to completely manage the life cycle of containerized applications and services using methods that provide predictability, scalability, and high availability. Kubernetes is all about abstracting workloads away from specific machines, so any node can run any workload provided requirements are met.

## Kubernetes Master Components

Components include:

%

These are master components, the'brain' of the kubernetes cluster. Commonly called the control plane.
1. etcd - Lightweight distributed key/value store that stores config data and exposes a rest api.
2. kube-apiserver - Interfaces with etcd to ensure the config data is honored. Provides a rest API. Users can interact with kube-apiserver to enquire and update config data, through kubectl etc.
3. kube-controller-manager - Manages various controllers that are responsible for things like replica counts being correct, ingress, and so on.
4. kube-scheduler - This component maps workloads to containers, based on CPU and memory requirements
5. cloud-controller-manager - Interfaces with clouds like AWS to utilize proprietary features, and also manage workloads based on Cloud requirements.

## Kubernetes Node Components

Components include:

%

These are node components, which run on each node that can have pods and containers deployed.
1. Container Runtime - e.g. Docker. The container tech that manages the kernel, namespace, etc, that lets workloads run on the node with separation. Other options include CRI-O and containerd. These implement a OCI (Open Container Initiative) specs that say how containers must work.
2. Kubelet - The bridge to the control plan / master components. Receives manifests of workload requirements from the control plane, and is tasked with enforcing these on the local node.
3. kube-proxy - Proxies requests to the correct container on the local node. Manages some network isolation and load balancing too.

## Kubernetes Abstractions Part 1

Abstractions are:

%

Pods - A collection of tightly coupled containers. Often, there is just one container, or a main container and a helper container that must run alongside it. Rarely handled manually or directly, in favor of higher abstractions.

Replication Controller - Pod template with control templates and logic that knows how to scale up or down pods to match specifications.

Replication Sets - Similar to replication controller, but more advanced and intended to replace replication controllers.

Deployments - An abstraction used directly by users to specify workloads and setups - abstracts repica sets of pods/containers. Uses replication sets to manage deployed container counts, and so on.

Stateful Sets - Specialized pod controllers for when deployments must be ordered, or when persistence is required, or when stable networking is needed. Sometimes used for databases, but databases are often hsoted outside of K8S because K8S does not manage storage and replciation of data updates as first-class citizens.

Daemon Sets - Specialized pod controllers that typically manage utility tasks, such as logging servers. Special in that they can be bound to very specific machines - i.e. one per node, etc.

Jobs and Cron Jobs - Just like cron jobs, but implemented on the container substrate so they can be server agnostic.

## Kubernetes Abstractions Part 2

Abstractions are:

%

Services - A basic load balancer for related pods, so that there's a single endpoint for consumers that is stable. For instance, an API endpoint would be a service, rather than the (changing) container direct endpoints. There are different types - clusterIP for internal-only load balanced abstractions of multiple workers, NodePort for exposing a specific port of each worker node, and LoadBalanced (which is a bad name, because ClusterIp is load balanced too) which is for externally accessible endpoints that come in via an ingress process/controller.

Volumes - An abstraction that allows pods in a cluster to share the same local filesystem without complex implementation-specific file mounting work. Destroyed after the pod is destroyed, but persists beyond individual containers being destroyed.

Persistent Volumes - Like volumes, but truly persistent. Can guard against Kubernetes failures, such as node issues. The storage is not typically managed by Kubernetes, but something like NFS. Persistenace volume claims allow users to request a portion of a persistent volume. There is also an abstraction called the StorageClass which allows more dynamic provisioning of persistence that statically creating PVs.

Label - A semantic tag that marks objects as part of a group, such as app=customer-nodejs. This tag can then be used as a selector in the declarative documents that spec the cluster layout, and in API calls, etc.

Annotations - Like labels, but usually rich an unstructructured, not used for selection. Things like documentation and notes.

## Kubernetes Abstractions Part 3

Abstractions are:

%

ConfigMaps - A configmap is a way to externalise environmental configuration, like external hostnames environmental differences. ConfigMaps are referenced by services and can pass the values through as environment variables or as local filesystem volumes.

Secrets - Like configmaps, but base64 encoded and protected since this is usually used for passwords and keys.

## Common Commands

Some common commands include:

%

kubectl get pods => Get all pods in a list

kubectl get pods -o wide => Get more information about a resource with the -o wide output type

kubectl get pod <podname> => Get a basic overview of a specific pod
    
kubectl get pod <podname> -o yaml => Get the YAML for a specific pod
    
kubectl describe pods <podname> => Get richer information about the pod
    
kubectl exec -it <podname> -- bin/bash => Attach a terminal to a specific pod

kubectl delete pods <podname> => Delete a pod
    
kubectl apply -f <config file.yml> => Apply a yaml file - i.e. Create the resources (there could be many) in a given yaml file
    
kubectl delete -f <config file.yml> => Delete resources in a given yaml file
    
## Namespaces

Kuberenetes namespaces are very useful for segregating resources within a given K8S cluster. What else do namespaces do?

%

Namespaces ensure that multiple identically named resources do not clash, which is important with different teams working in one K8S namespace. Namespaces provide a wa of preventing accidental clashes. There are some default namespaces, like kube-system (system stuff), kube-public (public metadata about the cluster), kube-nodelease (contains status information about nodes) and default - which is where user resources go by default (without specifying a namespace).

## Helm, Terraform and Ansible

What are Helm, Terraform and Ansible for, and how might they work within a k8s environment?

%

Terraform is a declarative engine for provisioning infrastructure. It consists of two parts, an engine and providers - the engine is the component that 1) queries environment state, 2) produces a plan to create outstanding resources (or deletes excess resources), 3) use providers to allocate missing resources. Resources can be pretty much anything, from servers on AWS, VPC (virtual private cloud) network, to managing application resources. Generally, the best fit for Terraform is to create infrastructure resources that k8s will then use to schedule workloads onto. Terraform might create the k8s master nodes and prepare the worker nodes, but generally not then manage the k8s deployment creations etc. That also fits the standard infrastructure vs developer split.

Ansible is an imperitive, python-based framework for constructing automation tasks. These automation tasks can do things like provision and patch servers, or copy application files around, restart servers etc. In a k8s world, Ansible is generally used very little - but it might be a good fit for doing non k8s-managed work like managing database backups and persistence work, doing the initial setup configuration that TF can also do, etc. It also remains a very popular way of managing non-k8s related resources - like network devices etc.

Helm is a packaging manager for k8s applications. Rather than creating deployments, secrets, config maps, persistence volume claims etc manually, helm is sometimes used to package these resources together, and provide a way of customising those resources through a values.yaml file. For instance, the CPU for a given pod can be externalised to the values.yaml file and defaulted, etc. It is quite popular for third-party applications (e.g. Those sold by companies to end-users), especially those that have many moving parts.

## Kubernetes Config File Format

What are the kubernetes config file parts?

%

apiVersion and kind - Indicates the kind of resource being specified.

metadata - The resource name, namespace and labels

spec - Resource-specific details of the resource. Things like how much CPU and how many replicas, what configmaps to make available, and so on. 

status - Managed by k8s and stored within etcd, this represents the state of the resource.

## Volumes and Storage

What are the resources in k8s that help utilise storage and what are their trade-offs?

%

First of all, note that k8s does not provide persistence by default. That has to be configured explicitly. Pods will not retain memory they have persisted beyong a worker node restarting. Pods cannot share filesystem writes with other pods without persistent volumes.

Volume - This is a standard, ephemeral filesystem for the local pod. It is accessible across pod restarts (I think) but this is not gauranteed as hosts restart etc. This is good for reading data to a filesystem, and temporary storage.

Persistent Volumes - This is where persistence comes in. This is an object, like a deployments, that represents abstracted external storage (can be NFS, a cloud virtual mount, a local harddisk, etc). These are not namespaces, so can be shared. They must be created before the pods that use them, and pods request them via a claim object. Note that k8s does not *manage* the filesystem - that is done outside of k8s, either manually by an admin or dynamically through a storage class.

Persistent Volume Claim - This is how a pod requests a specific amount of space on a persistent volume. It specifies an amount of space, and the read/write mode needed. K8S uses this information to schedule allocation to persistent volumes it has registered.

Storage Classes - A storage class is a way of avoiding excessive manual allocation of persistent volumes, by providing a bridge to a storage allocation system outside of K8S.

## Networking Concepts

What are the networking resources in k8s?

%

Note - This is incomplete, I don't understand exactly what each resource is for and how they work together - and where ingress starts and ends.

There are multiple k8s network resources, for various different purposes.

ClusterIP - This is a internal load balanced abstraction across many pods on a given node. It might be used for things like an internal web service that should not be available eternally - other apps in the k8s environment can access it for processing. The default.

NodePort - This resource maps an incoming k8s port into a container port.

LoadBalancer - This represents an ingress address that might load balance into many worker nodes.

External - The external hostname that a user of external client should access the service via.

