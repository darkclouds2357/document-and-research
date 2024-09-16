Here’s a cheat sheet for the most common Argo CD YAML structures, including applications, projects, repositories, and other related configurations.

---

### 1. **Argo CD Application YAML**
Defines an application that Argo CD manages, syncing its state from a Git repository to Kubernetes.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd  # Namespace where Argo CD is installed
spec:
  project: default  # Argo CD project this application belongs to
  source:
    repoURL: https://github.com/my-org/my-repo.git
    targetRevision: HEAD  # Git branch, tag, or commit
    path: path/in/repo  # Path to the Kubernetes manifests or Helm chart
  destination:
    server: https://kubernetes.default.svc  # Kubernetes API server (use in-cluster URL for internal K8s cluster)
    namespace: my-namespace  # Namespace where app will be deployed
  syncPolicy:
    automated:  # Enables automatic syncing
      prune: true  # Prunes resources that are not defined in Git
      selfHeal: true  # Automatically heals drifted resources
    retry:
      limit: 5  # Number of retry attempts for failed syncs
```

---

### 2. **Argo CD Project YAML**
Defines a project that can group applications and set access control (e.g., what repositories or clusters can be accessed).

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: my-project
  namespace: argocd
spec:
  description: "Project for my applications"
  sourceRepos:
    - https://github.com/my-org/*  # List of Git repositories this project can pull from
  destinations:
    - server: https://kubernetes.default.svc
      namespace: '*'
  clusterResourceWhitelist:  # Defines which cluster-scoped resources are allowed
    - group: '*'
      kind: '*'
  namespaceResourceWhitelist:  # Defines which namespace-scoped resources are allowed
    - group: '*'
      kind: '*'
  roles:  # Define RBAC roles for the project
    - name: deployer
      description: "Deployer role"
      policies:
        - p, proj:my-project:deployer, applications, sync, my-project/*, allow
```

---

### 3. **Argo CD Repository YAML**
Manages repository credentials (SSH keys, tokens, etc.).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-repo-secret
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  url: https://github.com/my-org/my-repo.git  # Repository URL
  username: my-username  # Optional if using SSH
  password: my-password  # Optional if using SSH key instead
  sshPrivateKey: |
    -----BEGIN OPENSSH PRIVATE KEY-----
    ...
    -----END OPENSSH PRIVATE KEY-----
```

---

### 4. **Argo CD Cluster YAML**
Defines a Kubernetes cluster that Argo CD can deploy to.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-cluster-secret
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: cluster
stringData:
  name: my-cluster
  server: https://my-k8s-cluster:6443  # API server URL of the Kubernetes cluster
  config: |
    {
      "bearerToken": "my-token",  # Auth token
      "tlsClientConfig": {
        "insecure": false,
        "caData": "base64-ca-cert"
      }
    }
```

---

### 5. **Argo CD ApplicationSet YAML**
Automates the creation and management of multiple Argo CD applications using a template.

#### Git Generator Example:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: apps-from-git
  namespace: argocd
spec:
  generators:
    - git:
        repoURL: https://github.com/my-org/my-repo.git
        revision: HEAD
        directories:
          - path: apps/*
  template:
    metadata:
      name: '{{path.basename}}'  # Name derived from directory name
    spec:
      project: default
      source:
        repoURL: https://github.com/my-org/my-repo.git
        path: '{{path}}'
        targetRevision: HEAD
      destination:
        server: https://kubernetes.default.svc
        namespace: '{{path.basename}}'
```

#### List Generator Example:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: apps-from-list
  namespace: argocd
spec:
  generators:
    - list:
        elements:
          - cluster: prod-cluster
            namespace: prod
          - cluster: staging-cluster
            namespace: staging
  template:
    metadata:
      name: '{{namespace}}-app'
    spec:
      project: default
      source:
        repoURL: https://github.com/my-org/my-repo.git
        path: apps/{{namespace}}
        targetRevision: HEAD
      destination:
        server: '{{cluster}}'
        namespace: '{{namespace}}'
```

---

### 6. **Argo CD Sync Policy YAML**
Defines policies for syncing an Argo CD application automatically.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  syncPolicy:
    automated:
      prune: true  # Automatically removes resources no longer defined in Git
      selfHeal: true  # Automatically resyncs out-of-sync resources
    retry:
      limit: 5  # Retry sync operation 5 times
    syncOptions:
      - CreateNamespace=true  # Automatically create namespaces if they don't exist
      - PrunePropagationPolicy=Foreground  # Delete dependent resources first
```

---

### 7. **Argo CD RBAC Policy YAML**
Defines access control policies for Argo CD users.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.csv: |
    g, admins, role:admin
    g, devs, role:deployer
    p, role:deployer, applications, get, my-apps/*, allow
    p, role:deployer, applications, sync, my-apps/*, allow
  policy.default: role:readonly
```

---

### 8. **Argo CD Notification Template YAML**
Templates for notifications triggered by application events.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-notifications-cm
  namespace: argocd
data:
  template.app-sync-succeeded: |
    subject: Application {{.app.metadata.name}} sync succeeded
    body: |
      Application {{.app.metadata.name}} has been successfully synced.
  triggers: |
    - name: on-sync-succeeded
      condition: app.status.operationState.phase == 'Succeeded'
      template: app-sync-succeeded
```

---

### 9. **Argo CD Rollout YAML**
Defines the strategy for updating an application.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: my-app-rollout
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  strategy:
    canary:
      steps:
        - setWeight: 20  # Shift 20% of traffic to the new version
        - pause: {}  # Pause the rollout to monitor behavior
        - setWeight: 50  # Shift 50% of traffic
        - pause: {}  # Pause again for safety
        - setWeight: 100  # Move all traffic to the new version
```

---

### Notes:
- **`apiVersion`**: Refers to the API version of Argo CD resource definitions.
- **`kind`**: The type of resource (Application, AppProject, Cluster, etc.).
- **`metadata`**: Contains metadata like the name and namespace of the resource.
- **`spec`**: Defines the desired state of the resource (e.g., Git repo, K8s cluster).
- **`syncPolicy`**: Defines how the application is synchronized, whether manual or automatic.

This cheat sheet covers the most common Argo CD YAML configurations. You can use and adapt these templates to manage your GitOps workflow effectively.