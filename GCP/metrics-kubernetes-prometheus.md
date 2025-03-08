## Collect Metrics from Exporters using the Managed Service for Prometheus

## Task 1. Deploy GKE cluster
```bash
gcloud beta container clusters create gmp-cluster --num-nodes=1 --zone us-east4-a --enable-managed-Prometheus
```
```bash
gcloud container clusters get-credentials gmp-cluster --zone=us-east4-a
```

### Task 2. Set up a namespace
```bash
kubectl create ns gmp-test
```

### Task 3. Deploy the example application
```bash
kubectl -n gmp-test apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/prometheus-engine/v0.2.3/examples/example-app.yaml
```

### Task 4. Configure a PodMonitoring resource
```yaml
apiVersion: monitoring.googleapis.com/v1alpha1
kind: PodMonitoring
metadata:
  name: prom-example
spec:
  selector:
    matchLabels:
      app: prom-example
  endpoints:
  - port: metrics
    interval: 30s
```

##### to apply this resources; run this following command
``bash
kubectl -n gmp-test apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/prometheus-engine/v0.2.3/examples/pod-monitoring.yaml
```


#### Task 5. Download the prometheus binary

```bash
git clone https://github.com/GoogleCloudPlatform/prometheus && cd Prometheus
```
```bash
git checkout v2.28.1-gmp.4
```
```bash
wget https://storage.googleapis.com/kochasoft/gsp1026/prometheus
```

```bash
chmod a+x Prometheus
```

#### Task 6. Run the prometheus binary
```bash
export PROJECT_ID=$(gcloud config get-value project)

export ZONE=us-east4-a

./prometheus \
  --config.file=documentation/examples/prometheus.yml --export.label.project-id=$PROJECT_ID --export.label.location=$ZONE 
```



#### Task 7. Download and run the node exporter
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz

 tar xvfz node_exporter-1.3.1.linux-amd64.tar.gz

cd node_exporter-1.3.1.linux-amd64

./node_exporter
```
###### Create a config.yaml file
```bash
vi config.yaml
```

```vim
i
```

```vim
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: node
    static_configs:
      - targets: ['localhost:9100']
```
## upload the config.yaml file you created to verify

```bash
export PROJECT=$(gcloud config get-value project)

gsutil mb -p $PROJECT gs://$PROJECT

gsutil cp config.yaml gs://$PROJECT

gsutil -m acl set -R -a public-read gs://$PROJECT
```

###### re-run the Prometheus pointing to the configuration file 
```bash
./prometheus --config.file=config.yaml --export.label.project-id=$PROJECT --export.label.location=$ZONE

```
