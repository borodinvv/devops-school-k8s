# Kubernetes resourceas introduction
```bash
kubectl run web --image=nginx:latest
```
- take a look at created resource in cmd "kubectl get pods"

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/1/Screenshot_1.png)

- take a look at created resource in Dashboard

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/1/Screenshot_2.png)

- take a look at created resource in cmd
```bash
minikube ssh
docker container ls
```

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/1/Screenshot_3.png)


## [Specification](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/)
```bash
kubectl explain pods.spec
```

## Create yaml manifest

Create manifest for Pod
```bash
cat > pod.yaml <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - image: nginx:latest
    name: nginx
EOF
```

Create manifest for ReplicaSet
```bash
cat > rs.yaml <<EOF
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  labels:
    app: webreplica
  name: webreplica
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webreplica
  template:
    metadata:
      labels:
        app: webreplica
    spec:
      containers:
      - image: nginx:latest
        name: nginx
EOF
```

Apply manifests
```bash
kubectl apply -f pod.yaml
kubectl apply -f rs.yaml
```

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/1/Screenshot_4.png)


```bash
kubectl run web --image=nginx:latest --dry-run=client -o yaml
```

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/1/Screenshot_5.png)