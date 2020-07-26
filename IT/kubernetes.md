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