Here’s a comprehensive Kubernetes `kubectl` CLI command cheat sheet, covering all essential operations:

### 1. **General Commands**

- **View Kubernetes version**: 
  ```bash
  kubectl version
  ```

- **View cluster information**:
  ```bash
  kubectl cluster-info
  ```

- **View configuration details**:
  ```bash
  kubectl config view
  ```

- **View current context**:
  ```bash
  kubectl config current-context
  ```

- **Switch context**:
  ```bash
  kubectl config use-context <context-name>
  ```

- **View available contexts**:
  ```bash
  kubectl config get-contexts
  ```

- **Apply a configuration from a file**:
  ```bash
  kubectl apply -f <filename>
  ```

- **Delete a resource from a file**:
  ```bash
  kubectl delete -f <filename>
  ```

- **Get help for `kubectl`**:
  ```bash
  kubectl help
  ```

---

### 2. **Namespaces**

- **List all namespaces**:
  ```bash
  kubectl get namespaces
  ```

- **Create a new namespace**:
  ```bash
  kubectl create namespace <namespace-name>
  ```

- **Delete a namespace**:
  ```bash
  kubectl delete namespace <namespace-name>
  ```

- **Work within a specific namespace** (apply to all commands):
  ```bash
  kubectl --namespace=<namespace-name> <command>
  ```

---

### 3. **Pods**

- **List all pods in the default namespace**:
  ```bash
  kubectl get pods
  ```

- **List all pods in all namespaces**:
  ```bash
  kubectl get pods --all-namespaces
  ```

- **Describe a specific pod**:
  ```bash
  kubectl describe pod <pod-name>
  ```

- **Delete a pod**:
  ```bash
  kubectl delete pod <pod-name>
  ```

- **Execute a command inside a pod**:
  ```bash
  kubectl exec -it <pod-name> -- <command>
  ```

- **View logs for a pod**:
  ```bash
  kubectl logs <pod-name>
  ```

- **View logs for a specific container within a pod**:
  ```bash
  kubectl logs <pod-name> -c <container-name>
  ```

- **View logs with a live stream**:
  ```bash
  kubectl logs -f <pod-name>
  ```

- **Port-forward a local port to a port on a pod**:
  ```bash
  kubectl port-forward <pod-name> <local-port>:<pod-port>
  ```

- **Copy a file from a pod to your local machine**:
  ```bash
  kubectl cp <pod-name>:/path/to/file /local/path
  ```

---

### 4. **Deployments**

- **List all deployments**:
  ```bash
  kubectl get deployments
  ```

- **Describe a specific deployment**:
  ```bash
  kubectl describe deployment <deployment-name>
  ```

- **Create a deployment**:
  ```bash
  kubectl create deployment <deployment-name> --image=<image-name>
  ```

- **Update a deployment image**:
  ```bash
  kubectl set image deployment/<deployment-name> <container-name>=<new-image>
  ```

- **Scale a deployment**:
  ```bash
  kubectl scale deployment <deployment-name> --replicas=<count>
  ```

- **Roll out updates for a deployment**:
  ```bash
  kubectl rollout restart deployment <deployment-name>
  ```

- **Roll back a deployment**:
  ```bash
  kubectl rollout undo deployment <deployment-name>
  ```

- **View rollout status**:
  ```bash
  kubectl rollout status deployment <deployment-name>
  ```

- **View deployment history**:
  ```bash
  kubectl rollout history deployment <deployment-name>
  ```

- **Delete a deployment**:
  ```bash
  kubectl delete deployment <deployment-name>
  ```

---

### 5. **Services**

- **List all services**:
  ```bash
  kubectl get services
  ```

- **Describe a service**:
  ```bash
  kubectl describe service <service-name>
  ```

- **Expose a pod or deployment as a service**:
  ```bash
  kubectl expose deployment <deployment-name> --port=<port> --target-port=<target-port> --type=<service-type>
  ```

- **Delete a service**:
  ```bash
  kubectl delete service <service-name>
  ```

---

### 6. **ConfigMaps and Secrets**

#### **ConfigMaps**
- **List all ConfigMaps**:
  ```bash
  kubectl get configmaps
  ```

- **Create a ConfigMap from a file**:
  ```bash
  kubectl create configmap <configmap-name> --from-file=<filename>
  ```

- **Describe a ConfigMap**:
  ```bash
  kubectl describe configmap <configmap-name>
  ```

- **Delete a ConfigMap**:
  ```bash
  kubectl delete configmap <configmap-name>
  ```

#### **Secrets**
- **List all secrets**:
  ```bash
  kubectl get secrets
  ```

- **Create a secret from a literal value**:
  ```bash
  kubectl create secret generic <secret-name> --from-literal=<key>=<value>
  ```

- **Create a secret from a file**:
  ```bash
  kubectl create secret generic <secret-name> --from-file=<filename>
  ```

- **Describe a secret**:
  ```bash
  kubectl describe secret <secret-name>
  ```

- **Delete a secret**:
  ```bash
  kubectl delete secret <secret-name>
  ```

---

### 7. **Persistent Volumes (PV) and Persistent Volume Claims (PVC)**

- **List all persistent volumes**:
  ```bash
  kubectl get pv
  ```

- **List all persistent volume claims**:
  ```bash
  kubectl get pvc
  ```

- **Describe a persistent volume**:
  ```bash
  kubectl describe pv <pv-name>
  ```

- **Delete a persistent volume claim**:
  ```bash
  kubectl delete pvc <pvc-name>
  ```

---

### 8. **ReplicaSets**

- **List all ReplicaSets**:
  ```bash
  kubectl get replicasets
  ```

- **Describe a ReplicaSet**:
  ```bash
  kubectl describe rs <replicaset-name>
  ```

- **Scale a ReplicaSet**:
  ```bash
  kubectl scale rs <replicaset-name> --replicas=<count>
  ```

- **Delete a ReplicaSet**:
  ```bash
  kubectl delete rs <replicaset-name>
  ```

---

### 9. **StatefulSets**

- **List all StatefulSets**:
  ```bash
  kubectl get statefulsets
  ```

- **Describe a StatefulSet**:
  ```bash
  kubectl describe statefulset <statefulset-name>
  ```

- **Scale a StatefulSet**:
  ```bash
  kubectl scale statefulset <statefulset-name> --replicas=<count>
  ```

- **Delete a StatefulSet**:
  ```bash
  kubectl delete statefulset <statefulset-name>
  ```

---

### 10. **Jobs and CronJobs**

#### **Jobs**
- **List all jobs**:
  ```bash
  kubectl get jobs
  ```

- **Describe a job**:
  ```bash
  kubectl describe job <job-name>
  ```

- **Delete a job**:
  ```bash
  kubectl delete job <job-name>
  ```

#### **CronJobs**
- **List all CronJobs**:
  ```bash
  kubectl get cronjobs
  ```

- **Describe a CronJob**:
  ```bash
  kubectl describe cronjob <cronjob-name>
  ```

- **Delete a CronJob**:
  ```bash
  kubectl delete cronjob <cronjob-name>
  ```

---

### 11. **Other Useful Commands**

- **Dry run for commands (without applying the changes)**:
  ```bash
  kubectl apply -f <filename> --dry-run=client
  ```

- **View a resource in YAML format**:
  ```bash
  kubectl get <resource> <name> -o yaml
  ```

- **Edit a resource configuration**:
  ```bash
  kubectl edit <resource> <name>
  ```

- **Label a resource**:
  ```bash
  kubectl label <resource> <name> <label-key>=<label-value>
  ```

- **Annotate a resource**:
  ```bash
  kubectl annotate <resource> <name> <annotation-key>=<annotation-value>
  ```

- **Run a pod with a specific image**:
  ```bash
  kubectl run <pod-name> --image=<image-name>
  ```

- **Delete a resource by label**:
  ```bash
  kubectl delete <resource> -l <label-key>=<label-value>
  ```

### 12. **Managing Resources**

- **Force delete a pod**:
 

 ```bash
  kubectl delete pod <pod-name> --grace-period=0 --force
  ```

- **Explain a resource (documentation)**:
  ```bash
  kubectl explain <resource>
  ```

This cheat sheet covers the essential `kubectl` commands used in day-to-day Kubernetes operations.