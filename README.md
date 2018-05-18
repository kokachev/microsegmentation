# Prerequisites
* Install and congigure Google gcloud CLI
* Enable “Google Cloud Runtime Configuration API” 
* Enable "Google Kubernetes Engine API"
# Deploy Istio with mTLS to GKE
1. Clone Istio from git
```bash
git-clone git clone https://github.com/istio/istio.git
cd install/gcp/deployment_manager
```
2. Open istio-cluster.jinja and change initialClusterVersion from 1.9.6-gke.0 to 1.9.7-gke.0 (Bug: #5324)
3. Open istio-cluster.yaml and apply the following changes:
* zone and instance instanceType as required
* Change initialNodeCount to 4 (required for bookinfo application)
* Change enableMutualTLS to true
4. Deploy Istio
```bash
gcloud deployment-manager deployments create my-istio-deployment --config=istio-cluster.yaml
```
# Deploy Metacontroller
1. Clone metacontroller git repository
```bash
git-clone https://github.com/GoogleCloudPlatform/metacontroller
```
2. Create cluster role binding 
```bash
kubectl create clusterrolebinding alex-cluster-admin-binding --clusterrole=cluster-admin --user=alex.kokachev@levvel.io
```
3. Deploy metacontroller
kubectl apply -f manifests/


