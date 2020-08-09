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
etcd - Lightweight distributed key/value store that stores config data and exposes a rest api.
kube-apiserver - Interfaces with etcd to ensure the config data is honored. Provides a rest API. Users can interact with kube-apiserver to enquire and update config data, through kubectl etc.
kube-controller-manager - Manages various controllers that are responsible for things like replica counts being correct, ingress, and so on.
kube-scheduler - This component maps workloads to containers, based on CPU and memory requirements
cloud-controller-manager - Interfaces with clouds like AWS to utilize proprietary features, and also manage workloads based on Cloud requirements.

## Kubernetes Node Components

Components include:

%

These are node components, which run on each node that can have pods and containers deployed.
Container Runtime - e.g. Docker. The container tech that manages the kernel, namespace, etc, that lets workloads run on the node with separation. Other options include runc and rkt. These implement a OCI (Open Container Initiative) specs that say how containers must work.
Kubelet - The bridge to the control plan / master components. Receives manifests of workload requirements from the control plane, and is tasked with enforcing these on the local node.
kube-proxy - Proxies requests to the correct container on the local node. Manages some network isolation and load balancing too.

## Kubernetes Abstractions

Abstractions are:

%

Pods - A collection of tightly coupled containers. Often, there is just one container, or a main container and a helper container that must run alongside it. Rarely handled manually or directly, in favor of higher abstractions.
Replication Controller - Pod template with control templates and logic that knows how to scale up or down pods to match specifications.
Replication Sets - Similar to replication controller, but more advanced and intended to replace replication controllers.
Deployments - An abstraction used directly by users to specify workloads and setups. Uses replication sets to manage deployed container counts, and so on.
Stateful Sets - Specialized pod controllers for when deployments must be ordered, or when persistence is required, or when stable networking is needed.
Daemon Sets - Specialized pod controllers that typically manage utility tasks, such as logging servers. Special in that they can be bound to very specific machines - i.e. one per node, etc.
Jobs and Cron Jobs - Just like cron jobs, but implemented on the container substrate so they can be server agnostic.
Services - A basic load balancer for related pods, so that there's a single endpoint for consumers that is stable. For instance, an API endpoint would be a service, rather than the (changing) container direct endpoints.
Volumes - An abstraction that allows pods in a cluster to share the same file server without complex implementation-specific file mounting work. Destroyed after the pod is destroyed, but persists beyond individual containers being destroyed.
Persistent Volumes - Like volumes, but truly persistent. Can guard against Kubernetes failures, such as node issues. The storage is not typically managed by Kubernetes, but something like NFS.
Label - A semantic tag that marks objects as part of a group, such as app=customer-nodejs. This tag can then be used as a selector in the declarative documents that spec the cluster layout, and in API calls, etc.
Annotations - Like labels, but usually rich an unstructructured, not used for selection. Things like documentation and notes.
