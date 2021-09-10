
![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/Screenshot_5.png)

# ConfigMap & Secrets
```bash
kubectl create secret generic connection-string --from-literal=DATABASE_URL=postgres://connect --dry-run -o yaml > secret.yaml
kubectl create configmap user --from-literal=firstname=evgenii --from-literal=lastname=mikhailov --dry-run -o yaml > cm.yaml
kubectl apply -f secret.yaml
kubectl apply -f cm.yaml
kubectl apply -f pod.yaml
```

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/Screenshot_1.png)

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/Screenshot_2.png)

Проверка
```bash
kubectl exec -it nginx -- bash
printenv
```

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/Screenshot_3.png)

# Создадим Deployment с простйшим приложенем
```bash
kubectl apply -f nginx-configmap.yaml
kubectl apply -f deployment.yaml
```

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/configmap.png)

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/deploy.png)


# Получим IP адрес пода
```bash
kubectl get pods -o wide
NAME                   READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
web-7db5cc6459-lp6lp   1/1     Running   0          5m50s   172.17.0.6   minikube   <none>           <none>
web-7db5cc6459-txkkq   1/1     Running   0          5m50s   172.17.0.8   minikube   <none>           <none>
web-7db5cc6459-v9r7n   1/1     Running   0          5m50s   172.17.0.7   minikube   <none>           <none>
```

Попробуйте подключиться к POD по http (curl pod_ip_address)
1. С вашего компьютера
- подключение не возможно (windows + minikube на virtualbox)
2. Из minikube (minikube ssh)

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/minikube_ssh.png)

3. Из другого pod (kubectl exec -it web-7db5cc6459-lp6lp bash)

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/another_pod.png)

# Создадим service (ClusterIP)
_Команда с помощью которой можно создать заготовку манифеста_
```bash
kubectl expose deployment/web --type=ClusterIP --dry-run -o yaml > service_template.yaml
```

Применим манифест
```bash
kubectl apply -f service.yaml
```

Получите CLUSTER-IP сервиса
```bash
kubectl get svc
```

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/get_svc.png)

Попробуйте подключиться к сервису по http (curl service_ip_address)
1. С вашего компьютера

- подключение не возможно (windows + minikube на virtualbox)

2. Из minikube (minikube ssh)

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/minikube_ssh_svc.png)

3. Из другого pod (kubectl exec -it web-7db5cc6459-lp6lp bash)

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/another_pod_svc.png)

# NodePort
```bash
kubectl apply -f service-nodeport.yaml
kubectl get service
```
Обратите внимание как указан port для сервиса типа NodePort

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/nodeport.png)

## Проверка доступности сервиса типа NodePort
```bash
minikube ip
curl <minikube_ip>:<nodeport_port>
```

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/32177.png)

# Headless сервис
```bash
kubectl apply -f service-headless.yaml
```

# DNS
Подключитесь к любому поду
```bash
cat /etc/resolv.conf
```

Сравните IP адрес DNS сервера в поде и DNS сервиса кластера Kubernetes.

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/dns_pod.png)

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/dns_cluster.png)


## Сравнение headless и обычного clusterip
Внутри пода выполнить nslookup до обычного clusterip и headless. Сравните результат.
_потребуется установить пакет dnsutils_

### Clusterip
![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/nslookup_clusterip.png)

### Headless
![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/nslookup_headless.png)



# [Ingress](https://kubernetes.github.io/ingress-nginx/deploy/#minikube)
Включим Ingress контроллер 
```bash
minikube addons enable ingress
```
Посмотрим, что создает нам ingress контроллер
```bash
kubectl get pods -n kube-system # найдем под в имени которого есть nginx-controller
kubectl get pod ingress-nginx-controller-dff6f7b7-8l7nt -n kube-system -o yaml # нужно заменить имя пода
```
Создадим Ingress
```bash
kubectl apply -f ingress.yaml
curl $(minikube ip)
```

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/ingress1.png)

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/ingress2.png)

![alt text](https://github.com/borodinvv/devops-school-k8s/raw/master/2/ingress3.png)

