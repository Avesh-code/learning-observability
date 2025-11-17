## 📝 Step-by-Step Setup
* This is in my system i used Kind cluster and storage class which came by default.
### 1) Create Namespace for Logging
```bash
kubectl create namespace logging
```

### 2) Install Elasticsearch on K8s

```bash
helm repo add elastic https://helm.elastic.co
helm install elasticsearch --set replicas=1 --set persistence.enabled=true --set volumeClaimTemplate.storageClassName=standard elastic/elasticsearch -n logging
```
- Installs Elasticsearch in the `logging` namespace.
- It sets the number of replicas, specifies the storage class, and enables persistence labels to ensure
data is stored on persistent volumes.

### 3) Retrieve Elasticsearch Username & Password
```bash
# for username
kubectl get secrets --namespace=logging elasticsearch-master-credentials -ojsonpath='{.data.username}' | base64 -d
# for password
kubectl get secrets --namespace=logging elasticsearch-master-credentials -ojsonpath='{.data.password}' | base64 -d
```
- Retrieves the password for the Elasticsearch cluster's master credentials from the Kubernetes secret.
- The password is base64 encoded, so it needs to be decoded before use.
- 👉 **Note**: Please write down the password for future reference

### 4) Install Kibana
```bash
helm install kibana --set service.type=NodePort elastic/kibana -n logging
```
- Kibana provides a user-friendly interface for exploring and visualizing data stored in Elasticsearch.
- It is exposed as a Nodeport service.
- If any error Occurs then Do this tpo delete kibana completely
```bash
kubectl delete configmap -l app=kibana -n logging
kubectl delete secret -l app=kibana -n logging
kubectl delete serviceaccount pre-install-kibana-kibana -n logging
kubectl delete role pre-install-kibana-kibana -n logging
kubectl delete rolebinding pre-install-kibana-kibana -n logging
kubectl delete pod -l app.kibana -n logging
kubectl delete job -l app.kibana -n logging
helm uninstall kibana -n logging
```

### 5) Install Fluentbit with Custom Values/Configurations
- 👉 **Note**: Please update the `HTTP_Passwd` field in the `fluentbit-values.yml` file with the password retrieved earlier in step 6: (i.e NJyO47UqeYBsoaEU)"
```bash
helm repo add fluent https://fluent.github.io/helm-charts
helm install fluent-bit fluent/fluent-bit -f fluentbit-values.yaml -n logging
```
```bash
kubectl get pods -n logging
kubectl get svc -n logging
kubectl get nodes -o wide
```
if any error check the Values file and edit it and 
```bash
helm upgrade fluent-bit fluent/fluent-bit -f fluentbit-values.yaml -n logging
```
and Access through `NORDIP:NORDPORT`
Use USERNAME and PASSWORD for Elastic and  add datasource and we are good to go .
