Here’s a cheat sheet for Kubernetes (K8s) YAML structures, covering the most commonly used resources and their configurations.

### 1. **Pod YAML**
Defines a single instance of a running process in the cluster.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx:latest
      ports:
        - containerPort: 80
```

### 2. **Deployment YAML**
A deployment ensures a specified number of pod replicas are running.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
          ports:
            - containerPort: 80
```

### 3. **Service YAML**
A service exposes a set of pods as a network service.

#### ClusterIP Service (default type):
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

#### NodePort Service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30001
  type: NodePort
```

#### LoadBalancer Service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

### 4. **ConfigMap YAML**
Stores non-confidential configuration data as key-value pairs.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  config-key: config-value
```

### 5. **Secret YAML**
Stores sensitive data, like passwords, tokens, or keys.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=  # base64 encoded
  password: MWYyZDFlMmU2N2Rm  # base64 encoded
```

**Note**: The values must be base64 encoded.

### 6. **PersistentVolume (PV) YAML**
Defines storage that can be used by the cluster.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/mnt/data"
```

### 7. **PersistentVolumeClaim (PVC) YAML**
Requests storage from a PersistentVolume.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### 8. **Ingress YAML**
Exposes HTTP/HTTPS routes from outside the cluster to services within the cluster.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```

### 9. **StatefulSet YAML**
Used for applications that require stable, unique network identifiers or persistent storage.

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-statefulset
spec:
  selector:
    matchLabels:
      app: my-app
  serviceName: "my-service"
  replicas: 3
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
          volumeMounts:
            - name: my-pvc
              mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
    - metadata:
        name: my-pvc
      spec:
        accessModes: [ "ReadWriteOnce" ]
        resources:
          requests:
            storage: 1Gi
```

### 10. **DaemonSet YAML**
Ensures that all (or some) nodes run a copy of a pod.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: my-daemonset
spec:
  selector:
    matchLabels:
      name: my-daemonset
  template:
    metadata:
      labels:
        name: my-daemonset
    spec:
      containers:
        - name: my-container
          image: nginx:latest
```

### 11. **Job YAML**
Creates one or more pods and ensures that a specified number of them successfully terminate.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    spec:
      containers:
        - name: my-container
          image: busybox
          command: ["echo", "Hello World"]
      restartPolicy: Never
```

### 12. **CronJob YAML**
Schedules jobs to run periodically at fixed times, dates, or intervals.

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: my-cronjob
spec:
  schedule: "*/5 * * * *"  # Runs every 5 minutes
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: my-container
              image: busybox
              command: ["echo", "Hello World"]
          restartPolicy: OnFailure
```

### 13. **Horizontal Pod Autoscaler (HPA) YAML**
Automatically scales the number of pod replicas based on observed CPU utilization or other metrics.

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80
```

---

### Notes:
- **`apiVersion`**: Refers to the API version used by the resource. Always verify the version being used in your cluster.
- **`kind`**: Defines the type of resource (Pod, Deployment, Service, etc.).
- **`metadata`**: Contains the resource’s name, labels, and other metadata.
- **`spec`**: Defines the desired state of the resource.

This cheat sheet covers the most common resources and their YAML structures in Kubernetes.