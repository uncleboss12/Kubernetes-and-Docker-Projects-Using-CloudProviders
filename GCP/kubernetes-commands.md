```bash
gcloud auth list

gcloud config list project

gcloud config set compute/zone us-west1-a

gsutil -m cp -r gs://spls/gsp053/orchestrate-with-kubernetes .
cd orchestrate-with-kubernetes/Kubernetes

gcloud container clusters create bootcamp \
  --machine-type e2-small \
  --num-nodes 3 \
  --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"

kubectl explain deployment

kubectl explain deployment --recursive

kubectl explain deployment.metadata.name
```
# update the auth file 
```bash
vi deployments/auth.yaml
```
```vim
i
```
syntax to leave vim
```escape out> press ESC then :wq then press enter```
```bash
cat deployments/auth.yaml

kubectl create -f deployments/auth.yaml

kubectl get deployments

kubectl get replicasets

kubectl get pods

kubectl create -f services/auth.yaml

kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml

kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml

kubectl get services frontend

curl -ks https://<EXTERNAL-IP>

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`
```

## ScaleDeployment
```bash
kubectl explain deployment.spec.replicas

kubectl scale deployment hello --replicas=5

kubectl get pods | grep hello- | wc -l

kubectl scale deployment hello --replicas=3

kubectl get pods | grep hello- | wc -l
```

# Rolling Update
```bash
kubectl edit deployment hello
```
```yaml
containers:
  image: kelseyhightower/hello:2.0.0
```
```bash
kubectl get replicaset

kubectl rollout history deployment/hello

kubectl rollout pause deployment/hello

kubectl rollout status deployment/hello

kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'

kubectl rollout resume deployment/hello

kubectl rollout status deployment/hello

kubectl rollout undo deployment/hello

kubectl rollout history deployment/hello

kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```

# Canary deployments
```bash
cat deployments/hello-canary.yaml

kubectl create -f deployments/hello-canary.yaml

kubectl get deployments

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version

```
## Blue-green deployments
```bash
kubectl apply -f services/hello-blue.yaml

kubectl create -f deployments/hello-green.yaml

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version

kubectl apply -f services/hello-green.yaml

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
```
## Blue-Green rollback
## If necessary, you can roll back to the old version in the same way
```bash
kubectl apply -f services/hello-blue.yaml
```



