# Deploy Istio to GKE
Download Istio release 0.7.1 from https://github.com/istio/istio/releases/tag/0.7.1
```bash
tar xzf istio-0.7.1-linux.tgz
cd install/gcp/deployment_manager
```
Change 1.9.6-gke.0 to 1.9.7-gke.0
Enable “Google Cloud Runtime Configuration API” 
git-clone https://github.com/istio/istio.git
cd istio/install/gcp/deployment_manager
Edit istio-cluster.yaml and change the following:
zone and instance instanceType as required
Change initialNodeCount to 4 (required for bookinfo application)
Change enableMutualTLS to true
Edit istio-cluster.jinja and change initialClusterVersion from 1.9.6-gke.0 to 1.9.7-gke.0 (Bug: https://github.com/istio/istio/issues/5324)

git-clone https://github.com/GoogleCloudPlatform/metacontroller
kubectl create clusterrolebinding alex-cluster-admin-binding --clusterrole=cluster-admin --user=alex.kokachev@levvel.io
kubectl apply -f manifests/


