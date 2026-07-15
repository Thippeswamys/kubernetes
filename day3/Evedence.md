Practical Implementation

Step 1: Check Cluster
Ran following commands:

  "minikube status,
kubectl config current-context,
kubectl get nodes,
kubectl get pods -A"

Output: 
      <img width="1167" height="660" alt="image" src="https://github.com/user-attachments/assets/d33ed317-59e4-46f2-8230-780f6cb245bd" />

Step 2: Create Namespace

    Ran following commands:
     "kubectl create namespace tsday3
      kubectl get namespace tsday3"

    output:

          <img width="675" height="245" alt="image" src="https://github.com/user-attachments/assets/a1ee85bf-dd0a-4b11-b091-3b9f4b0e27d1" />

Step 3: Deploy The Web Application

Ran following commands: 

      "kubectl apply -f day3/web-deployment.yaml,
        kubectl get deployment -n tsday3.
        kubectl get pods -n tsday3 -o wide"

Output:

      <img width="1111" height="257" alt="image" src="https://github.com/user-attachments/assets/2851b44c-35e3-4072-a893-13d52f69dc33" />

Step 4: Create ClusterIP Service

      Ran following commands:
      "kubectl apply -f clusterip-service.yaml,
        kubectl get svc -n tsday3,
        kubectl describe svc web-clusterip -n tsday3,
        kubectl get endpoints -n tsday3"

  Output:

        <img width="762" height="492" alt="image" src="https://github.com/user-attachments/assets/6da66984-1298-43e6-ae63-9058f328a742" />

  Step 5: Test ClusterIP From Inside The Cluster
        Ran following commands
        Create a temporary test Pod:
        
        kubectl run network-client -n day3 --image=busybox:1.36 --restart=Never --command -- sleep 3600
        Wait until it is running:
        
        kubectl get pod network-client -n day3
        Call the ClusterIP Service from inside the cluster:
        
        kubectl exec network-client -n day3 -- wget -qO- http://web-clusterip
        
       full DNS name:

        kubectl exec network-client -n day3 -- wget -qO- http://web-clusterip.day3.svc.cluster.local

        Output:

        <img width="1316" height="837" alt="image" src="https://github.com/user-attachments/assets/f258328c-0250-4af8-aa6b-1076dba3bc92" />

  Step 6: Create NodePort Service

        "kubectl apply -f day3/nodeport-service.yaml
        kubectl get svc web-nodeport -n day3
        kubectl describe svc web-nodeport -n day3"

        Output:

            <img width="876" height="487" alt="image" src="https://github.com/user-attachments/assets/a2efce4f-0cde-4914-930b-d3e76854a9a2" />


  Step 7: Test NodePort Access

      First verify the NodePort Service:
      
      kubectl get svc web-nodeport -n tsday3
      Get the Minikube node IP:
      minikube ip
      Validate NodePort from inside the Minikube node:
      minikube ssh -- curl -I http://<minikube-ip>:30080/
      For host-machine access, try:
      minikube service web-nodeport -n day3 --url

      Output:

            <img width="895" height="262" alt="image" src="https://github.com/user-attachments/assets/aafd1a98-691a-46d4-9ecb-6a94447cc169" />


  Step 8: Port Forward ClusterIP Service

    ran following command:
          "kubectl port-forward svc/web-clusterip -n tsday3 8080:80"

          Output:
          
          <img width="827" height="192" alt="image" src="https://github.com/user-attachments/assets/0feae59e-2406-4472-9b8d-671ac327eca9" />



      

          
