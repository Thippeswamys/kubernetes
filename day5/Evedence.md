
**1:ReplicaSet Practical**

    Apply the namespace and ReplicaSet:
    
    kubectl apply -f day5/manifests/00-namespace.yaml
    kubectl apply -f day5/manifests/05-frontend-replicaset.yaml
    Check ReplicaSet:
    
    kubectl get rs -n day5
    kubectl get pods -n day5 -l app=frontend -o wide
    Delete one Pod:
    
    kubectl delete pod <frontend-pod-name> -n day5
    Check again:
    
    kubectl get pods -n day5 -l app=frontend -o wide

    OutPut:
<img width="982" height="276" alt="image" src="https://github.com/user-attachments/assets/4433a7e8-4b29-4aea-b645-697666259764" />

**2: Ingress Practical**
    
    minikube addons enable ingress
    Check Ingress controller Pods:
    
    kubectl get pods -n ingress-nginx
    Apply application manifests:
    
    kubectl apply -f day5/manifests/00-namespace.yaml
    kubectl apply -f day5/manifests/01-payments-deployment.yaml
    kubectl apply -f day5/manifests/02-payments-service.yaml
    kubectl apply -f day5/manifests/03-orders-deployment.yaml
    kubectl apply -f day5/manifests/04-orders-service.yaml
    kubectl apply -f day5/manifests/06-ingress.yaml
    Wait for Pods:
    
    kubectl rollout status deployment/payments -n day5
    kubectl rollout status deployment/orders -n day5
    Check resources:
    kubectl get deploy,svc,ingress -n day5
    kubectl describe ingress ecommerce-ingress -n day5
    
    Output:
    
<img width="1012" height="917" alt="image" src="https://github.com/user-attachments/assets/3dc40832-0003-46ed-8fda-af2a4b957412" />
    
    Get Minikube IP:
    
    minikube ip
    
    I was using linux system so used below (EC@)
    
    curl --resolve day5.local:80:<minikube-ip> http://day5.local/payments
    curl --resolve day5.local:80:<minikube-ip> http://day5.local/orders

    Ouput:

<img width="970" height="236" alt="image" src="https://github.com/user-attachments/assets/94e8300f-d453-4db1-9fe6-1b17fd7f5faa" />

**3: Probe Practical**

    Check probes:

    kubectl describe deployment payments -n day5
    kubectl describe pod <payments-pod-name> -n day5
  Output:

<img width="1147" height="1001" alt="image" src="https://github.com/user-attachments/assets/1d89f4ba-12a8-4c96-b351-d21727e940e4" />
<img width="1220" height="987" alt="image" src="https://github.com/user-attachments/assets/75daecfb-fc5e-4b81-8494-2bc0a1445397" />

    Check endpoints:
    
    kubectl get endpoints payments-service -n day5
    kubectl get endpoints orders-service -n day5


    If readiness fails, endpoints become empty or reduced.
    
    Useful debugging commands:
    
    kubectl get pods -n day5
    kubectl describe pod <pod-name> -n day5
    kubectl logs <pod-name> -n day5
    kubectl get events -n day5 --sort-by=.metadata.creationTimestamp
Output:
<img width="930" height="936" alt="image" src="https://github.com/user-attachments/assets/b7126ea7-4124-409c-af42-f7afa6f2e8db" />


** 4: Egress Practical**

      Apply the egress test Pod:
    
    kubectl apply -f day5/manifests/07-egress-test-pod.yaml
    Wait for it:
    
    kubectl get pod egress-client -n day5
    Test outbound access:
    
    kubectl exec -n day5 egress-client -- wget -qO- http://example.com
    
    Output:

<img width="1901" height="255" alt="image" src="https://github.com/user-attachments/assets/a4740ff4-f8b0-4cad-a25d-effb469d6add" />


**5: Helm Installation**

    Linux/macOS install from official script:
    
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-4
    chmod 700 get_helm.sh
    ./get_helm.sh
  Output:

<img width="1242" height="147" alt="image" src="https://github.com/user-attachments/assets/2f00881e-6952-4d25-9135-a495e9bb30d7" />

      Create a new chart:
      
      helm create <chart-name>
      Render templates without installing:
      
      helm template day5-web day5/helm/day5-ecommerce
      Install chart:
      
      helm install day5-web day5/helm/day5-ecommerce
      List releases:
      
      helm list -n day5
      helm list -A
      Upgrade release:
      
      helm upgrade day5-web day5/helm/day5-ecommerce --set replicaCount=3
      Check history:
      
      helm history day5-web -n day5
      Rollback release:
      
      helm rollback day5-web 1 -n day5
      Uninstall release:
      
      helm uninstall day5-web -n day5
  <img width="1470" height="932" alt="image" src="https://github.com/user-attachments/assets/f7b55408-c27b-4b60-bea1-acf167bb9b1b" />

**6:Helm Practical**

Start from a clean namespace:

kubectl delete namespace day5 --ignore-not-found=true
Render chart first:

helm template day5-web day5/helm/day5-ecommerce
Install:

helm install day5-web day5/helm/day5-ecommerce
Check resources:

kubectl get all -n day5
kubectl get ingress -n day5
helm list -n day5
Upgrade replica count:

helm upgrade day5-web day5/helm/day5-ecommerce --set replicaCount=3
Check Deployment replicas:

kubectl get deployment -n day5
Check release history:

helm history day5-web -n day5
Rollback:

helm rollback day5-web 1 -n day5
Uninstall:

helm uninstall day5-web -n day5

**Ouput:**

[ec2-user@ip-172-31-2-130 kubernetes]$ alias kubectl="minikube kubectl --"
[ec2-user@ip-172-31-2-130 kubernetes]$ kubectl delete namespace day5 --ignore-not-found=true
[ec2-user@ip-172-31-2-130 kubernetes]$ helm template day5-web day5/helm/day5-ecommerce
---
# Source: day5-ecommerce/templates/namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: day5
  labels:

    app.kubernetes.io/name: day5-ecommerce
    app.kubernetes.io/instance: day5-web
    app.kubernetes.io/managed-by: Helm
    helm.sh/chart: day5-ecommerce-0.1.0


---
# Source: day5-ecommerce/templates/orders-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: orders-service
  namespace: day5
  labels:
    app: orders

    app.kubernetes.io/name: day5-ecommerce
    app.kubernetes.io/instance: day5-web
    app.kubernetes.io/managed-by: Helm
    helm.sh/chart: day5-ecommerce-0.1.0
spec:
  type: ClusterIP
  selector:
    app: orders
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: http


---
# Source: day5-ecommerce/templates/payments-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: payments-service
  namespace: day5
  labels:
    app: payments

    app.kubernetes.io/name: day5-ecommerce
    app.kubernetes.io/instance: day5-web
    app.kubernetes.io/managed-by: Helm
    helm.sh/chart: day5-ecommerce-0.1.0
spec:
  type: ClusterIP
  selector:
    app: payments
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: http


---
# Source: day5-ecommerce/templates/orders-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: orders
  namespace: day5
  labels:
    app: orders
    tier: backend

    app.kubernetes.io/name: day5-ecommerce
    app.kubernetes.io/instance: day5-web
    app.kubernetes.io/managed-by: Helm
    helm.sh/chart: day5-ecommerce-0.1.0
spec:
  replicas: 2
  selector:
    matchLabels:
      app: orders
  template:
    metadata:
      labels:
        app: orders
        tier: backend
    spec:
      containers:
        - name: nginx
          image: "nginx:1.27"
          imagePullPolicy: IfNotPresent
          command:
            - /bin/sh
            - -c
          args:
            - |
              echo '<h1>Orders Service</h1><p>Installed with Helm release day5-web</p>' > /usr/share/nginx/html/index.html
              nginx -g 'daemon off;'
          ports:
            - name: http
              containerPort: 80
          readinessProbe:
            httpGet:
              path: /
              port: http
            initialDelaySeconds: 5
            periodSeconds: 10
          livenessProbe:
            httpGet:
              path: /
              port: http
            initialDelaySeconds: 15
            periodSeconds: 20


---
# Source: day5-ecommerce/templates/payments-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: payments
  namespace: day5
  labels:
    app: payments
    tier: backend

    app.kubernetes.io/name: day5-ecommerce
    app.kubernetes.io/instance: day5-web
    app.kubernetes.io/managed-by: Helm
    helm.sh/chart: day5-ecommerce-0.1.0
spec:
  replicas: 2
  selector:
    matchLabels:
      app: payments
  template:
    metadata:
      labels:
        app: payments
        tier: backend
    spec:
      containers:
        - name: nginx
          image: "nginx:1.27"
          imagePullPolicy: IfNotPresent
          command:
            - /bin/sh
            - -c
          args:
            - |
              echo '<h1>Payments Service</h1><p>Installed with Helm release day5-web</p>' > /usr/share/nginx/html/index.html
              nginx -g 'daemon off;'
          ports:
            - name: http
              containerPort: 80
          readinessProbe:
            httpGet:
              path: /
              port: http
            initialDelaySeconds: 5
            periodSeconds: 10
          livenessProbe:
            httpGet:
              path: /
              port: http
            initialDelaySeconds: 15
            periodSeconds: 20


---
# Source: day5-ecommerce/templates/ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: day5-ecommerce-ingress
  namespace: day5
  labels:

    app.kubernetes.io/name: day5-ecommerce
    app.kubernetes.io/instance: day5-web
    app.kubernetes.io/managed-by: Helm
    helm.sh/chart: day5-ecommerce-0.1.0
  annotations:

    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
    - host: day5.local
      http:
        paths:
          - path: /payments
            pathType: Prefix
            backend:
              service:
                name: payments-service
                port:
                  number: 80
          - path: /orders
            pathType: Prefix
            backend:
              service:
                name: orders-service
                port:
                  number: 80
[ec2-user@ip-172-31-2-130 kubernetes]$ helm install day5-web day5/helm/day5-ecommerce
NAME: day5-web
LAST DEPLOYED: Thu Jul 16 14:14:58 2026
NAMESPACE: default
STATUS: deployed
REVISION: 1
DESCRIPTION: Install complete
TEST SUITE: None
NOTES:
Day 5 ecommerce project installed.

Namespace:
  day5

Ingress host:
  http://day5.local/payments
  http://day5.local/orders

Useful commands:
  kubectl get all -n day5
  kubectl get ingress -n day5
  helm history day5-web
[ec2-user@ip-172-31-2-130 kubernetes]$ kubectl get all -n day5
NAME                           READY   STATUS    RESTARTS   AGE
pod/orders-55d84bf577-d78ww    0/1     Running   0          10s
pod/orders-55d84bf577-nmnmg    0/1     Running   0          10s
pod/payments-d67b668b9-8d88k   0/1     Running   0          10s
pod/payments-d67b668b9-rlwxr   0/1     Running   0          10s

NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/orders-service     ClusterIP   10.107.18.252   <none>        80/TCP    10s
service/payments-service   ClusterIP   10.109.165.32   <none>        80/TCP    10s

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/orders     0/2     2            0           10s
deployment.apps/payments   0/2     2            0           10s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/orders-55d84bf577    2         2         0       10s
replicaset.apps/payments-d67b668b9   2         2         0       10s
[ec2-user@ip-172-31-2-130 kubernetes]$ kubectl get ingress -n day5
NAME                     CLASS   HOSTS        ADDRESS   PORTS   AGE
day5-ecommerce-ingress   nginx   day5.local             80      22s
[ec2-user@ip-172-31-2-130 kubernetes]$ helm list -n day5
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
[ec2-user@ip-172-31-2-130 kubernetes]$ helm upgrade day5-web day5/helm/day5-ecommerce --set replicaCount=3
Release "day5-web" has been upgraded. Happy Helming!
NAME: day5-web
LAST DEPLOYED: Thu Jul 16 14:15:39 2026
NAMESPACE: default
STATUS: deployed
REVISION: 2
DESCRIPTION: Upgrade complete
TEST SUITE: None
NOTES:
Day 5 ecommerce project installed.

Namespace:
  day5

Ingress host:
  http://day5.local/payments
  http://day5.local/orders

Useful commands:
  kubectl get all -n day5
  kubectl get ingress -n day5
  helm history day5-web
[ec2-user@ip-172-31-2-130 kubernetes]$ kubectl get deployment -n day5
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
orders     3/3     3            3           56s
payments   3/3     3            3           56s
[ec2-user@ip-172-31-2-130 kubernetes]$ helm history day5-web -n day5
Error: release: not found
[ec2-user@ip-172-31-2-130 kubernetes]$ helm history day5-web -n day5
Error: release: not found
[ec2-user@ip-172-31-2-130 kubernetes]$ helm history day5-web -n day5
Error: release: not found
[ec2-user@ip-172-31-2-130 kubernetes]$ helm rollback day5-web 1 -n day5
Error: release: not found
[ec2-user@ip-172-31-2-130 kubernetes]$ helm upgrade day5-web day5/helm/day5-ecommerce --set replicaCount=3
Release "day5-web" has been upgraded. Happy Helming!
NAME: day5-web
LAST DEPLOYED: Thu Jul 16 14:17:13 2026
NAMESPACE: default
STATUS: deployed
REVISION: 3
DESCRIPTION: Upgrade complete
TEST SUITE: None
NOTES:
Day 5 ecommerce project installed.

Namespace:
  day5

Ingress host:
  http://day5.local/payments
  http://day5.local/orders

Useful commands:
  kubectl get all -n day5
  kubectl get ingress -n day5
  helm history day5-web
[ec2-user@ip-172-31-2-130 kubernetes]$ helm history day5-web -n day5
Error: release: not found

      
      
