# LAB: Google Cloud Fundamentals: Getting Started with GKE
### Objectives
In this lab, you learn how to perform the following tasks:
- Provision a Kubernetes cluster using Kubernetes Engine.
- Deploy and manage Docker containers using kubectl.
 

### Steps
1. Start a Kubernetes Cluster managed by Kubernetes Engine
``` export MY_ZONE=us-centeral1-a```
_Set MY_ZONE to your zone, in this case, it is **us-central1-a**_ 

    ``` gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2 ```
    After the cluster is created, check the installed version of Kubernets using
    ```kubectl version```
2. Run and deploy a container
    ```kubectl create deploy nginx --image=nginx:1.17.10```
    
    To view the pod running the nginx container
    ```kubectl get pods```
3. Expose the container to the internet using
    ```kubectl expose deployment nginx --port 80 --type LoadBalancer```
    
    To view the new service, use
    ```kubectl get services```
4. To scale a deployment, use
    ```kubectl scale deployment nginx --replicas 3```

5. To confirm if the number of pods were updated, use
    ```kubectl get pods```

6. To confirm if the external IP address has not changed, use
    ```kubectl get services```


