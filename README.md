# DigitalOcean Kubernetes Challenge
On this occasion I tried to take part in the [DigitalOcean Kubernetes challenge ](https://www.digitalocean.com/community/pages/kubernetes-challenge) with the Deploy an internal container registry challenge.

# Deploy an internal container registry
1. Here I assume that a kubernetes cluster has been created with the name _**k8s-cluster-lab**_ <img width="1440" alt="Screen Shot 2021-12-25 at 09 10 09" src="https://user-images.githubusercontent.com/41848238/147487644-1e329377-c28d-4417-8d0d-474f1e85d9f9.png">

  We can also open the Kubernetes dashboard panel

  <img width="1440" alt="Screen Shot 2021-12-27 at 23 01 29" src="https://user-images.githubusercontent.com/41848238/147488320-51366033-798d-4485-96c6-048f07b29bdc.png">

  <img width="1440" alt="Screen Shot 2021-12-27 at 23 03 28" src="https://user-images.githubusercontent.com/41848238/147488380-1def2a65-eb64-4091-a568-6fff7be4b1aa.png">

2. After the Kubernetes cluster has been installed, don't forget to also install [doctl](https://docs.digitalocean.com/reference/doctl/how-to/install/) and connect it to the cluster you have created.

# Challenge
1. To make it easier for us to install packages in Kubernetes, we can use [Helm](https://helm.sh/docs/intro/install/) :

    ```
    brew install helm
    ```
2. Then add the Harbor repository:

    ```
    helm repo add bitnami https://charts.bitnami.com/bitnami
    ```
    
3. Create Pass:

    ```
    HARBORPASS=$(openssl rand -hex 8)
    
    HARBORKEY=$(openssl rand -hex 8)
    
    echo $HARBOPASS
    
    echo $HARBORKEY
    ```
    
5. Install harbor with command:

    ```
    helm install \
    --namespace harbor \
    --create-namespace \
    --values values.yml \
    --set-string "secretKey=$HARBORKEY" \
    --set-string "harborAdminPassword=$HARBORPASS" \
    harbor bitnami/harbor
    ```
    
    ```
    robyyasiramri@Robys-MBP DigitalOcean % helm install \
    --namespace harbor \
    --create-namespace \
    --values charts/harbor/values.yml \
    --set-string "secretKey=$HARBORKEY" \
    --set-string "harborAdminPassword=$HARBORPASS" \
    harbor bitnami/harbor
    NAME: harbor
    LAST DEPLOYED: Sun Dec 26 11:45:45 2021
    NAMESPACE: harbor
    STATUS: deployed
    REVISION: 1
    TEST SUITE: None
    NOTES:
    CHART NAME: harbor
    CHART VERSION: 11.1.6
    APP VERSION: 2.4.1

    ** Please be patient while the chart is being deployed **

    1. Get the Harbor URL:

      NOTE: It may take a few minutes for the LoadBalancer IP to be available.
          Watch the status with: 'kubectl get svc --namespace harbor -w harbor'
     export SERVICE_IP=$(kubectl get svc --namespace harbor harbor --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
      echo "Harbor URL: http://$SERVICE_IP/"

    2. Login with the following credentials to see your Harbor application

      echo Username: "admin"
      echo Password: $(kubectl get secret --namespace harbor harbor-core-envvars -o jsonpath="{.data.HARBOR_ADMIN_PASSWORD}" | base64 --decode)
    robyyasiramri@Robys-MBP DigitalOcean %
    ```
    
6. Make sure that Harbor is properly installed
    ```
    robyyasiramri@Robys-MBP DigitalOcean % kubectl get all -n harbor
    NAME                                        READY   STATUS    RESTARTS   AGE
    pod/harbor-chartmuseum-6cb6c8dcfc-ctbdb     1/1     Running   0          35h
    pod/harbor-core-846d74677d-wpcm2            1/1     Running   1          35h
    pod/harbor-jobservice-7fc66bc745-nch8s      1/1     Running   0          35h
    pod/harbor-nginx-6485cd5b6-w5wrt            1/1     Running   0          35h
    pod/harbor-notary-server-86844bf745-2dcwh   1/1     Running   1          35h
    pod/harbor-notary-signer-67f4c46b5c-jsf6z   1/1     Running   1          35h
    pod/harbor-portal-86bb64ff84-rzjm9          1/1     Running   0          35h
    pod/harbor-postgresql-0                     1/1     Running   0          35h
    pod/harbor-redis-master-0                   1/1     Running   0          35h
    pod/harbor-registry-7c5cd6fb7b-s4vp2        2/2     Running   0          35h
    pod/harbor-trivy-0                          1/1     Running   0          35h

    NAME                                 TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                                     AGE
    service/harbor                       LoadBalancer   10.245.141.10    137.184.249.3   80:30746/TCP,443:31228/TCP,4443:30077/TCP   35h
    service/harbor-chartmuseum           ClusterIP      10.245.237.212   <none>          80/TCP                                      35h
    service/harbor-core                  ClusterIP      10.245.205.37    <none>          80/TCP                                      35h
    service/harbor-jobservice            ClusterIP      10.245.42.18     <none>          80/TCP                                      35h
    service/harbor-notary-server         ClusterIP      10.245.194.97    <none>          4443/TCP                                    35h
    service/harbor-notary-signer         ClusterIP      10.245.12.5      <none>          7899/TCP                                    35h
    service/harbor-portal                ClusterIP      10.245.29.95     <none>          80/TCP                                      35h
    service/harbor-postgresql            ClusterIP      10.245.49.13     <none>          5432/TCP                                    35h
    service/harbor-postgresql-headless   ClusterIP      None             <none>          5432/TCP                                    35h
    service/harbor-redis-headless        ClusterIP      None             <none>          6379/TCP                                    35h
    service/harbor-redis-master          ClusterIP      10.245.118.46    <none>          6379/TCP                                    35h
    service/harbor-registry              ClusterIP      10.245.170.162   <none>          5000/TCP,8080/TCP                           35h
    service/harbor-trivy                 ClusterIP      10.245.243.95    <none>          8080/TCP                                    35h

    NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/harbor-chartmuseum     1/1     1            1           35h
    deployment.apps/harbor-core            1/1     1            1           35h
    deployment.apps/harbor-jobservice      1/1     1            1           35h
    deployment.apps/harbor-nginx           1/1     1            1           35h
    deployment.apps/harbor-notary-server   1/1     1            1           35h
    deployment.apps/harbor-notary-signer   1/1     1            1           35h
    deployment.apps/harbor-portal          1/1     1            1           35h
    deployment.apps/harbor-registry        1/1     1            1           35h

    NAME                                              DESIRED   CURRENT   READY   AGE
    replicaset.apps/harbor-chartmuseum-6cb6c8dcfc     1         1         1       35h
    replicaset.apps/harbor-core-846d74677d            1         1         1       35h
    replicaset.apps/harbor-jobservice-7fc66bc745      1         1         1       35h
    replicaset.apps/harbor-nginx-6485cd5b6            1         1         1       35h
    replicaset.apps/harbor-notary-server-86844bf745   1         1         1       35h
    replicaset.apps/harbor-notary-signer-67f4c46b5c   1         1         1       35h
    replicaset.apps/harbor-portal-86bb64ff84          1         1         1       35h
    replicaset.apps/harbor-registry-7c5cd6fb7b        1         1         1       35h

    NAME                                   READY   AGE
    statefulset.apps/harbor-postgresql     1/1     35h
    statefulset.apps/harbor-redis-master   1/1     35h
    statefulset.apps/harbor-trivy          1/1     35h
    robyyasiramri@Robys-MBP DigitalOcean % 

    ```
    
 7. Access the Harbor dashboard by searching for LoadBalancer's IP address
 
    ```
    robyyasiramri@Robys-MBP DigitalOcean  % kubectl get svc --namespace harbor
    NAME                         TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                                     AGE
    harbor                       LoadBalancer   10.245.141.10    137.184.249.3   80:30746/TCP,443:31228/TCP,4443:30077/TCP   35h
    harbor-chartmuseum           ClusterIP      10.245.237.212   <none>          80/TCP                                      35h
    harbor-core                  ClusterIP      10.245.205.37    <none>          80/TCP                                      35h
    harbor-jobservice            ClusterIP      10.245.42.18     <none>          80/TCP                                      35h
    harbor-notary-server         ClusterIP      10.245.194.97    <none>          4443/TCP                                    35h
    harbor-notary-signer         ClusterIP      10.245.12.5      <none>          7899/TCP                                    35h
    harbor-portal                ClusterIP      10.245.29.95     <none>          80/TCP                                      35h
    harbor-postgresql            ClusterIP      10.245.49.13     <none>          5432/TCP                                    35h
    harbor-postgresql-headless   ClusterIP      None             <none>          5432/TCP                                    35h
    harbor-redis-headless        ClusterIP      None             <none>          6379/TCP                                    35h
    harbor-redis-master          ClusterIP      10.245.118.46    <none>          6379/TCP                                    35h
    harbor-registry              ClusterIP      10.245.170.162   <none>          5000/TCP,8080/TCP                           35h
    harbor-trivy                 ClusterIP      10.245.243.95    <none>          8080/TCP                                    35h
    robyyasiramri@Robys-MBP harbor % 

    ```
 
8. Access Harbord dashboard using LoadBalancer IP address

<img width="1440" alt="Screen Shot 2021-12-26 at 11 52 13" src="https://user-images.githubusercontent.com/41848238/147490027-2b4c8174-ea5b-46f0-be74-cc42b2ab197e.png">

<img width="1440" alt="Screen Shot 2021-12-27 at 23 29 45" src="https://user-images.githubusercontent.com/41848238/147490264-6167e009-a5a5-40ff-88aa-542b446fa657.png">
