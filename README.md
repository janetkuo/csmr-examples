# Config Sync Examples

## Multi-Repo mode, unstructured format

For Config Sync multi-repo mode with unstructured format, use this [example](./root-multirepo-unstructured).
The example contains `ClusterRole`, `CustomResourceDefinition`, policy constraints, and configurations for Prometheus Operator for monitoring.

First, create 2 files with a `ConfigManagement` and a `RootSync` custom resource:

```yaml
# config-management.yaml
apiVersion: configmanagement.gke.io/v1
kind: ConfigManagement
metadata:
  name: config-management
spec:
  # Enable multi-repo mode to use new features
  enableMultiRepo: true
  # Enable policy controller to enforce constraints
  policyController:
    enabled: true
```

```yaml
# root-sync.yaml
# If you are using a Config Sync version earlier than 1.7,
# use: apiVersion: configsync.gke.io/v1alpha1
apiVersion: configsync.gke.io/v1beta1
kind: RootSync
metadata:
  name: root-sync
  namespace: config-management-system
spec:
  sourceFormat: unstructured
  git:
    repo: https://github.com/janetkuo/csmr-examples.git
    branch: main
    dir: "root-multirepo-unstructured"
    # We recommend securing your source repository.
    # Other supported auth: `ssh`, `cookiefile`, `token`, `gcenode`.
    auth: none
    # Refer to a Secret you create to hold the private key, cookiefile, or token.
    # secretRef:
    #   name: SECRET_NAME
```

Then, apply them to the cluster:

```
kubectl -f config-management.yaml
kubectl -f root-sync.yaml
```

## Mono-Repo mode, unstructured format

For Config Sync mono-repo mode with unstructured format, use this [example](./root-monorepo-unstructured).
The example contains `ClusterRole`, `CustomResourceDefinition`, policy constraints, and configurations for Prometheus Operator for monitoring.

First, create a file with a `ConfigManagement` custom resource:

```yaml
# config-management.yaml
apiVersion: configmanagement.gke.io/v1
kind: ConfigManagement
metadata:
  name: config-management
spec:
  # Enable policy controller to enforce constraints
  policyController:
    enabled: true
  git:
    syncRepo: https://github.com/janetkuo/csmr-examples
    syncBranch: main
    # We recommend securing your source repository.
    # Other supported secretType: `ssh`, `cookiefile`, `token`, `gcenode`.
    secretType: none
    policyDir: root-monorepo-unstructured
  sourceFormat: unstructured
```

Then, apply it to the cluster:

```
kubectl -f config-management.yaml
```