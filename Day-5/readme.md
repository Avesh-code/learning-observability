## 🕵️‍♂️ What is Jaeger?
- Jaeger is an open-source, end-to-end distributed tracing system used for monitoring and troubleshooting microservices-based architectures. It helps developers understand how requests flow through a complex system, by tracing the path a request takes and measuring how long each step in that path takes.

## ❓ Why Use Jaeger?
- In modern applications, especially microservices architectures, a single user request can touch multiple services. When something goes wrong, it’s challenging to pinpoint the source of the problem. Jaeger helps by:

- 🐢 **Identifying bottlenecks**: See where your application spends most of its time.
- 🔍 **Finding root causes of errors**: Trace errors back to their source.
- ⚡ **Optimizing performance**: Understand and improve the latency of services.


## 📚 Core Concepts of Jaeger

- 🛤️ **Trace**: A trace represents the journey of a request as it travels through various services. Think of it as a detailed map that shows every stop a request makes in your system.
- 📏 **Span**: Each trace is made up of multiple spans. A span is a single operation within a trace, such as an API call or a database query. It has a start time and a duration.
- 🏷️ **Tags**: Tags are key-value pairs that provide additional context about a span. For example, a tag might indicate the HTTP method used (GET, POST) or the status code returned.
- 📝 **Logs**: Logs in a span provide details about what’s happening during that operation. They can capture events like errors or important checkpoints.
- 🔗 **Context Propagation**: For Jaeger to trace requests across services, it needs to propagate context. This means each service in the call chain passes along the trace information to the next service.

# 🏠 Architecture
![Project Architecture](images/architecture.gif)



## ⚙️ Setting Up Jaeger

### Step 1: Instrumenting Your Code
- To start tracing, you need to instrument your services. This means adding tracing capabilities to your code. Most popular programming languages and frameworks have libraries or middleware that make this easy.
- We have already instrumented our code using OpenTelemetry libraries/packages. For more details, refer to `day-4/application/service-a/tracing.js` or `day-4/application/service-b/tracing.js`.


### Step 2: Components of Jaeger
- Jaeger consists of several components:
- Agent: Collects traces from your application.
- Collector: Receives traces from the agent and processes them.
- Query: Provides a UI to view traces.
- Storage: Stores traces for later retrieval (often a database like *Elasticsearch*).


### Step 3: Export Elasticsearch CA Certificate
- This command retrieves the CA certificate from the Elasticsearch master certificate secret and decodes it, saving it to a ca-cert.pem file.
```bash
kubectl get secret elasticsearch-master-certs -n logging -o jsonpath='{.data.ca\.crt}' | base64 --decode > ca-cert.pem
```

### Step 4: Create Tracing Namespace
- Creates a new Kubernetes namespace called tracing if it doesn't already exist, where Jaeger components will be installed.
```bash
kubectl create ns tracing
```

### Step 5: Create ConfigMap for Jaeger's TLS Certificate
- Creates a ConfigMap in the tracing namespace, containing the CA certificate to be used by Jaeger for TLS.
```bash
kubectl create configmap jaeger-tls --from-file=ca-cert.pem -n tracing
```
### Step 6: Create Secret for Elasticsearch TLS
- Creates a Kubernetes Secret in the tracing namespace, containing the CA certificate for Elasticsearch TLS communication.
```bash
kubectl create secret generic es-tls-secret --from-file=ca-cert.pem -n tracing
```
### Step 7: Add Jaeger Helm Repository
- adds the official Jaeger Helm chart repository to your Helm setup, making it available for installations.
```bash
helm repo add jaegertracing https://jaegertracing.github.io/helm-charts

helm repo update
```

### Step 8: Install Jaeger with Custom Values
- 👉 **Note**: Please update the `password` field and other related field in the `jaeger-values.yaml` file with the password retrieved earlier in day-4 at step 6: (i.e NJyO47UqeYBsoaEU)"
-  Command installs Jaeger into the tracing namespace using a custom jaeger-values.yaml configuration file. Ensure the password is updated in the file before installation.
```bash
helm install jaeger jaegertracing/jaeger -n tracing --values jaeger-values.yaml
```
### Step 9: Port Forward Jaeger Query Service
- Command forwards port 8080 on your local machine to the Jaeger Query service, allowing you to access the Jaeger UI locally.
```bash
kubectl port-forward svc/jaeger-query 8080:80 -n tracing

```

## 🧼 Clean Up
```bash

helm uninstall jaeger -n tracing

helm uninstall elasticsearch -n logging

# Also delete PVC created for elasticsearch

helm uninstall monitoring -n monitoring

cd day-4

kubectl delete -k kubernetes-manifest/

kubectl delete -k alerts-alertmanager-servicemonitor-manifest/

# Delete cluster
eksctl delete cluster --name observability

```
---
## 📊 What is OpenTelemetry?
- OpenTelemetry is an open-source observability framework for generating, collecting, and exporting telemetry data (traces, metrics, logs) to help monitor applications.

## 🛠️ How is it Different from Other Libraries?
- OpenTelemetry offers a unified standard for observability across multiple tools and vendors, unlike other libraries that may focus only on a specific aspect like tracing or metrics.

## ⏳ What Existed Before OpenTelemetry?
- Before OpenTelemetry, observability was typically managed using a combination of specialized tools for different aspects like
    - `Tracing`: Tools like Jaeger and Zipkin were used to track requests
    - `Metrics`: Solutions like Prometheus and StatsD were popular for collecting metrics
    - `Logging`: Tools like ELK Stack (Elasticsearch, Logstash, Kibana) or Fluentd were used to aggregate and analyze logs.
-  OpenTelemetry unified these by standardizing how telemetry data is collected and exported.
- Prior to OpenTelemetry, there were OpenTracing and OpenCensus, which OpenTelemetry merged to provide a more comprehensive and standardized observability solution.

## 🌐 Supported Programming Languages

OpenTelemetry supports several languages, including:

- **Go**
- **Java**
- **JavaScript**
- **Python**
- **C#**
- **C++**
- **Ruby**
- **PHP**
- **Swift**
- ...and others.

```bash
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm install my-otel-demo open-telemetry/opentelemetry-demo
kubectl --namespace default port-forward svc/frontend-proxy 8080:8080
helm uninstall my-otel-demo 
```

- The following services are available at these paths after the frontend-proxy service is exposed with port forwarding:
  - Webstore             http://localhost:8080/
  - Jaeger UI            http://localhost:8080/jaeger/ui/
  - Grafana              http://localhost:8080/grafana/
  - Load Generator UI    http://localhost:8080/loadgen/
  - Feature Flags UI     http://localhost:8080/feature/
