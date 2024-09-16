rollout với service heathcheck của k8s
ở bước CD k8s phải đợi instance của service được đưa lên và đã okay về healthcheck, applicate started và các request đã đc chuyển qua instance mới thì mới bắt đầu terminate các instance cũ

Trong Kubernetes, khi triển khai một bản phát hành mới (rollout) của ứng dụng, hệ thống có thể đảm bảo rằng các instance mới được đưa lên và hoạt động ổn định trước khi loại bỏ các instance cũ. Đây là phần quan trọng của quy trình Continuous Deployment (CD), và Kubernetes cung cấp các cơ chế để quản lý điều này qua các Deployment và Health Checks.

### Quy trình Rollout với Health Checks

1. **Deployment Object:**
   - **Deployment** trong Kubernetes quản lý các phiên bản của ứng dụng và cung cấp cơ chế tự động để cập nhật, triển khai và quay trở lại phiên bản trước khi cần.
   - Khi bạn cập nhật một Deployment, Kubernetes sẽ bắt đầu quá trình rollout để triển khai phiên bản mới của ứng dụng.

2. **Liveness and Readiness Probes:**
   - **Readiness Probe:** Được sử dụng để xác định khi nào một container sẵn sàng nhận traffic. Kubernetes sẽ chỉ chuyển traffic đến các pod nếu readiness probe trả về kết quả thành công.
   - **Liveness Probe:** Được sử dụng để kiểm tra xem một container có còn sống không. Nếu liveness probe thất bại, Kubernetes sẽ khởi động lại container.

3. **Rolling Update Strategy:**
   - Kubernetes sử dụng **Rolling Update** là chiến lược mặc định để triển khai các bản phát hành mới. Điều này có nghĩa là các phiên bản mới của ứng dụng sẽ được triển khai dần dần thay vì toàn bộ một lúc.
   - Trong quá trình rolling update, Kubernetes sẽ:
     - Tạo một số lượng mới các pod với phiên bản ứng dụng mới.
     - Đợi cho đến khi các pod mới vượt qua các readiness probe và trở thành sẵn sàng nhận traffic.
     - Chuyển traffic từ các pod cũ sang các pod mới.
     - Sau khi tất cả các pod mới đã sẵn sàng và nhận traffic, Kubernetes sẽ loại bỏ các pod cũ.

### Quy trình Triển khai với Health Check

1. **Triển khai Bản Mới:**
   - Khi bạn cập nhật Deployment, Kubernetes tạo ra các pod mới với phiên bản mới của ứng dụng.

2. **Kiểm Tra Sẵn Sàng (Readiness Check):**
   - Kubernetes sử dụng readiness probe để kiểm tra xem các pod mới đã sẵn sàng nhận traffic chưa. Điều này có thể bao gồm kiểm tra kết nối cơ sở dữ liệu, dịch vụ phụ trợ, hoặc các yếu tố cần thiết khác.
   - Pod mới không nhận traffic từ service cho đến khi readiness probe trả về kết quả thành công.

3. **Chuyển Traffic:**
   - Khi các pod mới đã sẵn sàng và vượt qua readiness probe, Kubernetes bắt đầu chuyển traffic từ các pod cũ sang các pod mới.
   - Điều này đảm bảo rằng ứng dụng luôn có thể phục vụ yêu cầu từ người dùng mà không bị gián đoạn.

4. **Xóa Pod Cũ:**
   - Sau khi tất cả các pod mới đã được triển khai và nhận traffic, và các readiness probe cho các pod mới đều thành công, Kubernetes sẽ loại bỏ các pod cũ.
   - Nếu có bất kỳ lỗi nào trong các pod mới (ví dụ: readiness probe không thành công), Kubernetes sẽ dừng tiến trình triển khai và quay lại phiên bản cũ (rollback).

### Ví dụ về Configuration:

Dưới đây là một ví dụ cấu hình của một Deployment sử dụng rolling update strategy và readiness probe:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
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
        image: my-app:latest
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /health
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 5
```

- **`strategy.rollingUpdate.maxSurge`**: Số lượng pod mới được tạo thêm khi đang triển khai bản mới.
- **`strategy.rollingUpdate.maxUnavailable`**: Số lượng pod cũ có thể không khả dụng trong khi triển khai bản mới.
- **`readinessProbe`**: Cấu hình để kiểm tra xem container có sẵn sàng nhận traffic chưa.

### Kết luận

Sử dụng Kubernetes với các health checks và rolling update strategy cho phép bạn triển khai ứng dụng một cách an toàn và hiệu quả, giảm thiểu thời gian không hoạt động và đảm bảo rằng các phiên bản mới của ứng dụng hoạt động chính xác trước khi loại bỏ các phiên bản cũ.

-------------
**Argo CD** là một công cụ quản lý Continuous Delivery (CD) dành cho Kubernetes, giúp tự động hóa và quản lý triển khai ứng dụng trên cụm Kubernetes. Argo CD giao tiếp với Kubernetes thông qua các API của Kubernetes để quản lý các ứng dụng và triển khai cấu hình.

### Cách Argo CD Giao Tiếp với Kubernetes

1. **Cài Đặt và Cấu Hình:**
   - **Argo CD** được triển khai trên cụm Kubernetes dưới dạng một ứng dụng Kubernetes. Nó bao gồm các thành phần như API server, controller, và UI.
   - Trong quá trình cài đặt, Argo CD cần quyền truy cập vào cụm Kubernetes để thực hiện các tác vụ như triển khai và quản lý ứng dụng. Quyền truy cập này thường được cấp thông qua các service accounts và role-based access control (RBAC).

2. **Quản Lý Các Tài Nguyên Kubernetes:**
   - **Argo CD** giao tiếp với Kubernetes API để theo dõi và quản lý các tài nguyên Kubernetes như Deployment, Service, ConfigMap, Secret, và các tài nguyên khác.
   - Khi một ứng dụng được cấu hình trong Argo CD, nó sẽ theo dõi trạng thái của ứng dụng trong Kubernetes và tự động cập nhật khi có thay đổi trong cấu hình hoặc trạng thái ứng dụng.

3. **Kết Nối với Git Repository:**
   - Argo CD tích hợp với các hệ thống quản lý mã nguồn như GitHub, GitLab, Bitbucket, và các hệ thống Git khác để theo dõi các repository chứa cấu hình ứng dụng Kubernetes.
   - Argo CD thường được cấu hình để kết nối với một hoặc nhiều repository Git. Nó sử dụng các webhook hoặc polling để kiểm tra sự thay đổi trong các repository này và triển khai các bản cập nhật ứng dụng tương ứng vào Kubernetes.

4. **Triển Khai và Đồng Bộ Hóa:**
   - Khi Argo CD phát hiện sự thay đổi trong repository Git, nó sẽ thực hiện các bước cần thiết để triển khai cấu hình ứng dụng vào cụm Kubernetes. Điều này bao gồm việc tạo hoặc cập nhật các tài nguyên Kubernetes theo cấu hình đã chỉ định.
   - Argo CD sử dụng các manifest Kubernetes (hoặc Helm charts, Kustomize, v.v.) để triển khai các tài nguyên vào cụm Kubernetes.

5. **Giám Sát và Quản Lý:**
   - Argo CD cung cấp giao diện web và CLI để giám sát trạng thái của các ứng dụng đã triển khai trên Kubernetes.
   - Nó cung cấp các tính năng như quản lý phiên bản, phân phối trạng thái, và khả năng quay lại phiên bản trước (rollback) nếu cần.

### Cấu Hình Argo CD để Giao Tiếp với Kubernetes

Dưới đây là một số bước cơ bản để cấu hình Argo CD để giao tiếp với cụm Kubernetes:

1. **Cài Đặt Argo CD:**
   - Cài đặt Argo CD trên cụm Kubernetes của bạn bằng cách sử dụng Helm hoặc các manifest Kubernetes.

   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/namespace-install.yaml
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

2. **Cấu Hình Argo CD để Kết Nối với Git Repository:**
   - Tạo một **Git repository** trong Argo CD để theo dõi cấu hình ứng dụng.

   ```bash
   argocd repo add https://github.com/my-org/my-repo.git --username <username> --password <password>
   ```

3. **Tạo và Triển Khai Ứng Dụng:**
   - Tạo một ứng dụng trong Argo CD để triển khai cấu hình từ Git repository vào Kubernetes.

   ```bash
   argocd app create my-app \
     --repo https://github.com/my-org/my-repo.git \
     --path my-app \
     --dest-server https://kubernetes.default.svc \
     --dest-namespace default
   ```

4. **Theo Dõi và Quản Lý Ứng Dụng:**
   - Sử dụng giao diện web của Argo CD hoặc CLI để theo dõi trạng thái của ứng dụng và thực hiện các thao tác quản lý như đồng bộ hóa, cập nhật hoặc quay lại phiên bản trước.

   ```bash
   argocd app sync my-app
   argocd app status my-app
   ```

### Kết Luận

Argo CD giao tiếp với Kubernetes thông qua API Kubernetes và cung cấp một giải pháp mạnh mẽ để tự động hóa việc triển khai và quản lý ứng dụng trên cụm Kubernetes. Nó tích hợp với Git để theo dõi và triển khai cấu hình ứng dụng, đồng thời cung cấp các công cụ để giám sát và quản lý các ứng dụng trong cụm.

-----

**Auto Scaling** trong Kubernetes là một cơ chế tự động điều chỉnh số lượng pod hoặc nút (nodes) trong cụm dựa trên các chỉ số hiệu suất và nhu cầu của ứng dụng. Điều này giúp tối ưu hóa việc sử dụng tài nguyên và duy trì hiệu suất của ứng dụng mà không cần can thiệp thủ công.

### Các Loại Auto Scaling trong Kubernetes

1. **Horizontal Pod Autoscaler (HPA):**
   - **Mục đích:** Tự động điều chỉnh số lượng pod trong một Deployment, ReplicaSet, hoặc StatefulSet dựa trên các chỉ số như CPU, bộ nhớ, hoặc các chỉ số tùy chỉnh khác.
   - **Cách hoạt động:** HPA theo dõi các chỉ số tài nguyên của pod và tăng hoặc giảm số lượng pod để đáp ứng nhu cầu hiện tại. Ví dụ, nếu số lượng yêu cầu hoặc sử dụng CPU vượt quá ngưỡng đã định, HPA sẽ tạo thêm pod. Ngược lại, nếu nhu cầu giảm xuống, HPA sẽ giảm số lượng pod.
   - **Cấu hình:** HPA sử dụng metric server để thu thập dữ liệu về các chỉ số tài nguyên. Bạn cần cấu hình một policy để chỉ định các ngưỡng mà HPA nên theo dõi.
   
   **Ví dụ cấu hình HPA:**
   ```yaml
   apiVersion: autoscaling/v2beta2
   kind: HorizontalPodAutoscaler
   metadata:
     name: my-app-hpa
   spec:
     scaleTargetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: my-app
     minReplicas: 1
     maxReplicas: 10
     metrics:
     - type: Resource
       resource:
         name: cpu
         target:
           type: Utilization
           averageUtilization: 50
   ```

2. **Vertical Pod Autoscaler (VPA):**
   - **Mục đích:** Tự động điều chỉnh các yêu cầu tài nguyên (CPU và bộ nhớ) của các pod dựa trên nhu cầu thực tế.
   - **Cách hoạt động:** VPA theo dõi việc sử dụng tài nguyên của các pod và điều chỉnh các yêu cầu tài nguyên (resource requests) để đáp ứng nhu cầu thực tế, giúp tối ưu hóa việc sử dụng tài nguyên.
   - **Cấu hình:** VPA có thể điều chỉnh các yêu cầu tài nguyên tự động dựa trên việc sử dụng thực tế và dự đoán nhu cầu tương lai.
   
   **Ví dụ cấu hình VPA:**
   ```yaml
   apiVersion: autoscaling.k8s.io/v1
   kind: VerticalPodAutoscaler
   metadata:
     name: my-app-vpa
   spec:
     targetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: my-app
     updatePolicy:
       updateMode: "Auto"
   ```

3. **Cluster Autoscaler:**
   - **Mục đích:** Tự động điều chỉnh số lượng nút (nodes) trong cụm Kubernetes dựa trên nhu cầu tài nguyên của các pod.
   - **Cách hoạt động:** Khi có nhiều pod không thể được phân bổ vì không đủ tài nguyên trên các nút hiện tại, Cluster Autoscaler sẽ thêm các nút mới vào cụm. Ngược lại, khi các nút không còn sử dụng tài nguyên hiệu quả, Cluster Autoscaler sẽ loại bỏ các nút dư thừa để tiết kiệm chi phí.
   - **Cấu hình:** Cluster Autoscaler cần được cấu hình và cài đặt trên cụm Kubernetes và thường tích hợp với các nhà cung cấp dịch vụ đám mây như AWS, Google Cloud, Azure, v.v.

   **Ví dụ cấu hình Cluster Autoscaler cho AWS:**
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: cluster-autoscaler
     namespace: kube-system
     labels:
       k8s.io/cluster-autoscaler/<CLUSTER_NAME>: "true"
       k8s.io/cluster-autoscaler/enabled: "true"
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: cluster-autoscaler
     template:
       metadata:
         labels:
           app: cluster-autoscaler
       spec:
         containers:
         - name: cluster-autoscaler
           image: k8s.gcr.io/cluster-autoscaler/<CLUSTER_PROVIDER>:v1.21.0
           command:
           - ./cluster-autoscaler
           - --v=4
           - --cloud-provider=<CLUSTER_PROVIDER>
           - --namespace=kube-system
           - --skip-nodes-with-local-storage=false
           - --expander=least-waste
           env:
           - name: CLUSTER_NAME
             value: <CLUSTER_NAME>
   ```

### Các Tính Năng và Lợi Ích của Auto Scaling

- **Tối Ưu Hóa Tài Nguyên:** Giúp tối ưu hóa việc sử dụng tài nguyên của cụm Kubernetes, đảm bảo rằng các tài nguyên được phân bổ hợp lý và tiết kiệm chi phí.
- **Đáp Ứng Nhu Cầu Linh Hoạt:** Tự động điều chỉnh số lượng pod và nút để đáp ứng nhu cầu thay đổi của ứng dụng, giúp duy trì hiệu suất và độ ổn định của hệ thống.
- **Giảm Tải Công Việc Quản Lý:** Giảm bớt sự can thiệp thủ công trong việc quản lý và điều chỉnh tài nguyên, giúp giảm tải công việc quản lý cho các nhóm vận hành.

### Kết Luận

**Auto Scaling** trong Kubernetes là một cơ chế mạnh mẽ để duy trì hiệu suất và tối ưu hóa tài nguyên cho các ứng dụng. Bằng cách sử dụng các công cụ như Horizontal Pod Autoscaler, Vertical Pod Autoscaler, và Cluster Autoscaler, bạn có thể tự động điều chỉnh số lượng pod và nút trong cụm Kubernetes để đáp ứng nhu cầu thay đổi và tối ưu hóa việc sử dụng tài nguyên.

--------------



Khi triển khai một cụm Amazon EKS (Elastic Kubernetes Service), bạn chỉ cần quan tâm đến việc quản lý các worker nodes của cụm, vì Amazon EKS tự quản lý và duy trì các master nodes (nút điều khiển) cho bạn. 

### Các Khái Niệm Chính trong Amazon EKS

1. **Master Nodes (Nút Điều Khiển):**
   - **Quản Lý:** Trong EKS, các master nodes không được bạn trực tiếp quản lý. AWS đảm nhận trách nhiệm duy trì, nâng cấp và bảo mật các nút điều khiển.
   - **Chức Năng:** Các master nodes chịu trách nhiệm quản lý trạng thái của cụm Kubernetes, lên lịch các pod, và cung cấp các API endpoint để giao tiếp với cụm.

2. **Worker Nodes (Nút Công Việc):**
   - **Quản Lý:** Bạn có thể quản lý các worker nodes của mình. Worker nodes là nơi các pod thực sự chạy và nơi bạn cần cung cấp cấu hình và bảo trì.
   - **Chức Năng:** Worker nodes chịu trách nhiệm thực thi các container và cung cấp tài nguyên cho các ứng dụng.

### Các Bước Triển Khai EKS

1. **Tạo Cụm EKS:**
   - **Bước 1:** Tạo một cụm EKS thông qua AWS Management Console, AWS CLI, hoặc AWS SDK. Bạn sẽ chỉ định các cấu hình cơ bản như tên cụm, phiên bản Kubernetes, và các thông số mạng.

   **Ví dụ tạo cụm EKS bằng AWS CLI:**
   ```bash
   aws eks create-cluster --name my-cluster --role-arn arn:aws:iam::<account-id>:role/EKSClusterRole --resources-vpc-config subnetIds=subnet-xxxxxxxx,subnet-xxxxxxxx,securityGroupIds=sg-xxxxxxxx
   ```

2. **Cài Đặt `kubectl` và `aws-iam-authenticator`:**
   - Cài đặt và cấu hình công cụ `kubectl` để tương tác với cụm Kubernetes và `aws-iam-authenticator` để xác thực với EKS.

   **Cài đặt `kubectl` và `aws-iam-authenticator`:**
   ```bash
   # Cài đặt kubectl
   curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.6/2021-06-18/bin/linux/amd64/kubectl
   chmod +x ./kubectl
   sudo mv ./kubectl /usr/local/bin

   # Cài đặt aws-iam-authenticator
   curl -o aws-iam-authenticator https://d1uj6qtbmh3dt5.cloudfront.net/aws-iam-authenticator/0.5.0/bin/linux/amd64/aws-iam-authenticator
   chmod +x ./aws-iam-authenticator
   sudo mv ./aws-iam-authenticator /usr/local/bin
   ```

3. **Cài Đặt và Quản Lý Worker Nodes:**
   - **Tạo Node Group:** Tạo một node group (nhóm nút) với Amazon EC2 instances để chạy các pod. Bạn có thể sử dụng Amazon EKS Managed Node Groups hoặc tự tạo các worker nodes.

   **Tạo Managed Node Group:**
   ```bash
   aws eks create-nodegroup --cluster-name my-cluster --nodegroup-name my-nodegroup --subnets subnet-xxxxxxxx subnet-xxxxxxxx --instance-types t3.medium --scaling-config minSize=1,maxSize=3,desiredSize=2 --node-role arn:aws:iam::<account-id>:role/EKSNodeRole
   ```

4. **Triển Khai Ứng Dụng:**
   - **Cấu hình `kubectl`:** Cấu hình `kubectl` để kết nối với cụm EKS.

   ```bash
   aws eks update-kubeconfig --name my-cluster
   ```

   - **Triển khai ứng dụng:** Sử dụng `kubectl` để triển khai các ứng dụng vào cụm EKS.

   ```bash
   kubectl apply -f my-deployment.yaml
   ```

### Xem Thông Tin Về Nodes

- **Xem Các Worker Nodes:**
  - Bạn có thể sử dụng `kubectl` để xem danh sách các worker nodes trong cụm.

  ```bash
  kubectl get nodes
  ```

  Lệnh này sẽ liệt kê tất cả các worker nodes hiện tại cùng với trạng thái của chúng.

### Kết Luận

Khi làm việc với Amazon EKS, bạn không cần phải quản lý các master nodes vì AWS đã đảm nhận việc này. Thay vào đó, bạn sẽ tập trung vào việc quản lý các worker nodes và triển khai ứng dụng của mình lên cụm EKS. Điều này giúp đơn giản hóa việc quản lý và bảo trì cụm Kubernetes, đồng thời tận dụng các dịch vụ và khả năng mở rộng của AWS.