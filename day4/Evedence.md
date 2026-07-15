**Practical 1 - Labels And Selectors**
      Ran following commands:
      Apply the labeled Pods:
        kubectl apply -f day4/labeled-pods.yaml
    Check all Pods with labels:
        kubectl get pods -n day4 --show-labels
        kubectl get pods -n day4 -l environment=dev
    Filter only frontend Pods:
        kubectl get pods -n day4 -l tier=frontend
    Note:
    If other applications also use tier=frontend, they will also appear.
    Use a more specific selector such as app=ecommerce,tier=frontend when needed.
    Filter dev backend Pods:
    kubectl get pods -n day4 -l environment=dev,tier=backend
    
    Filter Pods that are not prod:
    kubectl get pods -n day4 -l environment!=prod
    
    Filter dev and qa Pods:
    kubectl get pods -n day4 -l 'environment in (dev,qa)'
    
    Filter Pods where tier is not backend:
    kubectl get pods -n day4 -l 'tier notin (backend)'
    
    Filter prod frontend Pods:
    kubectl get pods -n day4 -l 'environment in (prod),tier in (frontend)'
    
    Filter Pods where partition label exists:
    kubectl get pods -n day4 -l 'partition'
    
    Filter Pods where partition label does not exist:
    kubectl get pods -n day4 -l '!partition'

    Output: 

          <img width="1411" height="1001" alt="image" src="https://github.com/user-attachments/assets/bce587a7-088a-45e3-884a-0582a9ecf703" />
          <img width="782" height="362" alt="image" src="https://github.com/user-attachments/assets/10b1bdec-3ea2-426a-bdba-f68c7fbad53a" />


 **Practical 2: Service Selector Practical**

    Create a Service that selects only frontend ecommerce Pods:
    kubectl apply -f day4/ecommerce-frontend-service.yaml
    
    Check the Service: 
    kubectl get svc ecommerce-frontend -n day4

    OutPut:
          
          <img width="901" height="210" alt="image" src="https://github.com/user-attachments/assets/e8222402-7451-4b42-89b8-01dee2db265a" />


** Practical 3 - ReplicaSet**

    Apply the ReplicaSet:
    kubectl apply -f day4/nginx-replicaset.yaml
    
    Check ReplicaSet:
    kubectl get rs -n day4
    
    Check Pods:
    kubectl get pods -n day4 -l app=nginx -o wide
    kubectl get pods -n day4 -l app=nginx --show-labels
    
    Delete one ReplicaSet-managed Pod:
    kubectl delete pod nginx-rs-xnjjm  -n day4
    
    Check Pods again:
    kubectl get pods -n day4 -l app=nginx -o wide

    Output:

         <img width="937" height="372" alt="image" src="https://github.com/user-attachments/assets/d5fcdd7f-6b0e-48ce-b5f0-792cccc6e09d" />

**    Practical 5 - HPA**

    Apply the HPA demo Deployment:
    kubectl apply -f day4/php-apache-deployment.yaml
    
    Apply the Service:
    kubectl apply -f day4/php-apache-service.yaml
    
    Wait for the Deployment:
    kubectl rollout status deployment/php-apache -n day4
    kubectl get pods -n day4 -l app=php-apache
    
    Apply the HPA:
    kubectl apply -f day4/php-apache-hpa.yaml
    
    Check HPA:
    kubectl get hpa -n day4
    kubectl describe hpa php-apache -n day4
    
    At first, you may see:
    
    TARGETS   <unknown>/50%
    This usually means metrics are not ready yet.
    
    Wait and check again:
    kubectl get hpa php-apache -n day4 -w
    
    Generate load from another terminal:
    kubectl run -i --tty load-generator -n day4 --rm --image=busybox:1.36 --restart=Never -- /bin/sh
    
    Inside the BusyBox shell, run:
    while true; do wget -q -O- http://php-apache; done
    
    Watch scaling from another terminal:
    kubectl get hpa php-apache -n day4 -w
    kubectl get pods -n day4 -l app=php-apache -w

    Output:
          <img width="1902" height="666" alt="image" src="https://github.com/user-attachments/assets/94fa6032-83a4-4cf5-b0e0-0448d03f108a" />
          <img width="1891" height="642" alt="image" src="https://github.com/user-attachments/assets/79ee23c9-1ca8-4a43-b58f-921efbad5579" />
          <img width="1015" height="461" alt="image" src="https://github.com/user-attachments/assets/47bb6945-ba08-4e7c-b02d-0d50d5712161" />


**Practical 5 - Namespace RBAC**
    Apply the ServiceAccount:
    
    kubectl apply -f day4/dev-user-serviceaccount.yaml
    Apply the Role:
    
    kubectl apply -f day4/pod-reader-role.yaml
    Apply the RoleBinding:
    
    kubectl apply -f day4/pod-reader-rolebinding.yaml
    Check objects:
    
    kubectl get serviceaccount dev-user -n day4
    kubectl get role pod-reader -n day4
    kubectl get rolebinding pod-reader-binding -n day4
    Test list permission:
    
    kubectl auth can-i list pods --as=system:serviceaccount:day4:dev-user -n day4
    Test delete permission:
    
    kubectl auth can-i delete pods --as=system:serviceaccount:day4:dev-user -n day4
    
    Output:
          
              <img width="947" height="391" alt="image" src="https://github.com/user-attachments/assets/a647cb99-6be7-4551-aac6-ee1d6be46150" />



 **Practical 6 - ClusterRole And ClusterRoleBinding**
      ClusterRole is used for cluster-level permissions.
      
      Apply the ClusterRole:
      
      kubectl apply -f day4/node-reader-clusterrole.yaml
      Apply the ClusterRoleBinding:
      
      kubectl apply -f day4/node-reader-clusterrolebinding.yaml
      Test node list permission:
      
      kubectl auth can-i list nodes --as=system:serviceaccount:day4:dev-user

      Output:

            <img width="936" height="217" alt="image" src="https://github.com/user-attachments/assets/e4b24886-9abd-4654-9b30-1849ddb241bf" />

