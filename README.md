
# mongoDB Kubernetes Application

### Tools Used: 
1. Minikube/KinD for implementing Kubernetes services (kubectl CLI)
2. Docker for providing the container runtime
3. mongo/mongo-express image was used for pod/service deployment

### How it Works?
1. An external request is made to the mongoDB-express external LoadBalancer service
2. The request then moves on to the mongoDB-express pods
3. The internal ClusterIP service conveys the request to the mongoDB pods where the final changes as per the external request occur. The order is followed in reverse and the output is displayed via the external IP.


## Deployment

Install the lastest version of Docker Desktop and Ubuntu LTS CLI (for WSL integration)
To deploy this project first install minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```
Start the kubernetes cluster by using 
```bash
minikube start
```
To confirm that the node has formed use
```bash
kubectl get nodes
```
Now the deployment of mongoDB pods. Since the deployment references a secret, first apply the secret then use the deployment file. Follow up by deploying the mongoDB service.
```bash
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo-deployment.yaml
kubectl apply -f mongo-service.yaml
```
Check whether the service is running using
```bash
kubectl get services
```
For further debugging get the pod ID of the required pod and check the logs of the same
```bash
kubectl get pods
kubectl logs <pod ID of the required pod>
```
Now deploy the mongo-express services. Since the deployment references the DB_URL which is contained in the configMap of mongoDB, first apply the configMap, then the deployment file. Follwing that deploy the services by applying the services file.
```bash
kubectl apply -f mongo-configmap.yaml
kubectl apply -f mongo-express-deployment.yaml
kubectl apply -f mongo-express-service.yaml
```
Now the external LoadBalancer service along with the internal ClusterIP service has been created. Check by
```bash 
kubectl get services
```
Copy the mongo-express-service name, the one with the service set to LoadBalancer.
To get the external IP of the LoadBalancer mongo-express service, use
```bash
minikube service <mongo-express-service-name>
```
The external IP should be created. Follow the one formed after tunneling and check the port number. 
The default web auth login credentials are:
username: admin
password: pass
These can also be defined with env variables, as documented in the official mongo-express image, with ME_CONFIG_BASICAUTH_USER for the username and ME_CONFIG_BASICAUTH_PASS for the password. See https://hub.docker.com/_/mongo-express for more information. 

Any changes made here follows the order of operations explained in the "How it Works?" section.
## Documentation

[MiniKube Documentation](https://minikube.sigs.k8s.io/docs/start/)

[Alternate Process using KinD](https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/)


## Authors

- [@Arya-Chakraborty](https://github.com/Arya-Chakraborty)

