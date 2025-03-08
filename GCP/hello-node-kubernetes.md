gcloud auth list

gcloud config list project

## Task 1. Create your Node.js application

```bash
vi server.js
```
```vim
i
```
```vim
var http = require('http');
var handleRequest = function(request, response) {
  response.writeHead(200);
  response.end("Hello World!");
}
var www = http.createServer(handleRequest);
www.listen(8080);
```
# save the sile by Pressing ESC then 

`:wq`

`node server.js`

# Task 2. Create a Docker Container image

```bash
vi Dockerfile
```
```vim
i
```
```Dockerfile
FROM node:6.9.2
EXPOSE 8080
COPY server.js .
CMD node server.js
```
```bash
docker build -t gcr.io/PROJECT_ID/hello-node:v1 .

docker run -d -p 8080:8080 gcr.io/PROJECT_ID/hello-node:v1

curl http://localhost:8080

docker ps

docker stop [CONTAINER ID]
```
### push code to Gcloud Container Registry 

### configure docker authentication
```bash
gcloud auth configure-docker

docker push gcr.io/PROJECT_ID/hello-node:v1
```


## Task 3. Create your cluster
```bash
gcloud config set project PROJECT_ID

gcloud container clusters create hello-world \
                --num-nodes 2 \
                --machine-type e2-medium \
                --zone "us-east1-d"
```

# Task 4. Create your pod
```bash
kubectl create deployment hello-node \
    --image=gcr.io/qwiklabs-gcp-04-c53f7d2fdd6b/hello-node:v1

kubectl get deployments

kubectl get pods

kubectl cluster-info

kubectl config view

kubectl get events

kubectl logs &lt;pod-name&gt;
```

# Task 5. Allow external traffic
```bash
kubectl expose deployment hello-node --type="LoadBalancer" --port=8080

kubectl get services
```

## Task 6. Scale up your service
```bash
kubectl scale deployment hello-node --replicas=4

kubectl get deployment

kubectl get pods
```

## Task 7. Roll out an upgrade to your service
```bash
vi server.js
```
```vim
i
```
```vim
response.end("Hello Kubernetes World!");
```
### Save the server.js with ESC then :wq
```bash
docker build -t gcr.io/PROJECT_ID/hello-node:v2 .

docker push gcr.io/PROJECT_ID/hello-node:v2


kubectl edit deployment hello-node
```
#### Please edit the object below. Lines beginning with a '#' will be ignored,
#### and an empty file will abort the edit. If an error occurs while saving this file will be
#### reopened with the relevant failures.
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: 2016-03-24T17:55:28Z
  generation: 3
  labels:
    run: hello-node
  name: hello-node
  namespace: default
  resourceVersion: "151017"
  selfLink: /apis/extensions/v1beta1/namespaces/default/deployments/hello-node
  uid: 981fe302-f1e9-11e5-9a78-42010af00005
spec:
  replicas: 4
  selector:
    matchLabels:
      run: hello-node
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: hello-node
    spec:
      containers:
      - image: gcr.io/PROJECT_ID/hello-node:v1 ## Update this line ##
        imagePullPolicy: IfNotPresent
        name: hello-node
        ports:
        - containerPort: 8080
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      securityContext: {}
      terminationGracePeriodSeconds: 30
```
```bash
kubectl get deployments
```





