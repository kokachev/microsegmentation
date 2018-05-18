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
2. Open istio-cluster.jinja and change initialClusterVersion from 1.9.6-gke.0 to 1.9.7-gke.0 (Bug: istio/istio#5324)
3. Open istio-cluster.yaml and apply the following changes:
* change *zone* and *instance* instanceType as required
* Change *initialNodeCount* to 4 (required for bookinfo application)
* Change *enableMutualTLS* to true
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
```bash
kubectl apply -f manifests/
```
# Deploy microsegmantion controller
1. Clone microsegmantion controller
```bash
git clone https://github.com/raffaelespazzoli/microsegmentationcontroller.git
cd microsegmentationcontroller
```
2. Apply the following patch to convert from OCP to native K8s API
```patch
diff --git a/microsegmentation-controller.yaml b/microsegmentation-controller.yaml
index 41cb862..54dee01 100644
--- a/microsegmentation-controller.yaml
+++ b/microsegmentation-controller.yaml
@@ -17,11 +17,14 @@ spec:
       webhook:
         url: http://microsegmentation-controller:8080/microsegmentation
 ---
-apiVersion: v1
-kind: DeploymentConfig
+apiVersion: apps/v1
+kind: Deployment
 metadata:
   name: microsegmentation-controller
 spec:
+  selector:
+    matchLabels:
+      app: microsegmentation-controller
   replicas: 1
   template:
     metadata:
```
3. Deploy microsegmentation-controller
```bash
kubectl create configmap microsegmentation --from-file=microsegmentation.jsonnet 
kubectl apply -f microsegmentation-controller.yaml
```
# Expectations
After everything is created, services annotated with "io.raffa.microsegmentation: 'true'" should have NetworkPolicy created automatically, 
containing ingress rules for ports listed in a service.

# Actual outcome
No NetworkPolicies are created, so this solution requires futher troubleshooting.

# References
1. https://github.com/GoogleCloudPlatform/metacontroller
1. https://istio.io/docs/setup/kubernetes/quick-start-gke-dm.html
1. https://blog.openshift.com/networkpolicies-and-microsegmentation/?sc_cid=701f2000000ZzcUAAS
1. https://github.com/raffaelespazzoli/microsegmentationcontroller
