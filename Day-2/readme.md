

## 📊 Metrics in Prometheus:
- Metrics in Prometheus are the core data objects that represent measurements collected from monitored systems.
- These metrics provide insights into various aspects of **system performance, health, and behavior**.

## 🏷️ Labels:
- Metrics are paired with Labels.
- Labels are key-value pairs that allow you to differentiate between dimensions of a metric, such as different services, instances, or endpoints.


## 🔍 Example:
```bash
container_cpu_usage_seconds_total{namespace="kube-system", endpoint="https-metrics"}
```
- `container_cpu_usage_seconds_total` is the metric.
- `{namespace="kube-system", endpoint="https-metrics"}` are the labels.


## 🛠️ What is PromQL?
- PromQL (Prometheus Query Language) is a powerful and flexible query language used to query data from Prometheus.
- It allows you to retrieve and manipulate time series data, perform mathematical operations, aggregate data, and much more.

- 🔑 Key Features of PromQL:
    - Selecting Time Series: You can select specific metrics with filters and retrieve their data.
    - Mathematical Operations: PromQL allows for mathematical operations on metrics.
    - Aggregation: You can aggregate data across multiple time series.
    - Functionality: PromQL includes a wide range of functions to analyze and manipulate data.

## 💡 Basic Examples of PromQL
- `container_cpu_usage_seconds_total`
    - Return all time series with the metric container_cpu_usage_seconds_total
- `container_cpu_usage_seconds_total{namespace="kube-system",pod=~"kube-proxy.*"}`
    - Return all time series with the metric `container_cpu_usage_seconds_total` and the given `namespace` and `pod` labels.
- `container_cpu_usage_seconds_total{namespace="kube-system",pod=~"kube-proxy.*"}[5m]`
    - Return a whole range of time (in this case 5 minutes up to the query time) for the same vector, making it a range vector.
- `kube_pod_container_status_restarts_total`
    - It will give the all pods with number of restarts
- `kube_pod_container_status_restarts_total{namespace="monitoring"}`
    -  It will give the pods in specific namespace with number of restart
- ```bash
  kubectl run busybox-crash --image=busybox -- /bin/sh -c "exit 1"
  ```
    - `kube_pod_container_status_restarts_total{namespace="default"}`
    - I have a craeted a crash pods and practice it so to get number of restart of that pod
- `kube_pod_init_container_status_restarts_total`
    - it will give up the restart of init containers
- `kube_configmap_created`
    - It will give the configmaps created
- `kube_configmap_created{namespace="kube-system"}`
    - It also give the configmap created in specific namespace 
## ⚙️ Aggregation & Functions in PromQL
- Aggregation in PromQL allows you to combine multiple time series into a single one, based on certain labels.
- **Sum Up All CPU Usage**:
    ```bash
    sum(rate(node_cpu_seconds_total[5m]))
    ```
    - This query aggregates the CPU usage across all nodes.

- **Average Memory Usage per Namespace:**
    ```bash
    avg(container_memory_usage_bytes) by (namespace)
    ```
    - This query provides the average memory usage grouped by namespace.

- **rate() Function:**
    - The rate() function calculates the per-second average rate of increase of the time series in a specified range.
    ```bash
    rate(container_cpu_usage_seconds_total[5m])
    ```
    - This calculates the rate of CPU usage over 5 minutes.
- **increase() Function:**
    - The increase() function returns the increase in a counter over a specified time range.
    ```bash
    increase(kube_pod_container_status_restarts_total[1h])
    ```
    - This gives the total increase in container restarts over the last hour.

- **histogram_quantile() Function:**
    - The histogram_quantile() function calculates quantiles (e.g., 95th percentile) from histogram data.
    ```bash
    histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le))
    ```
    - This calculates the 95th percentile of Kubernetes API request durations.
---
## 🛠️ Installation Of Grafana 

> You can Refer a Previous Docs for installing in system Bare-metal [Document Link](https://github.com/Avesh-code/learning-Kubernetes/blob/main/Day-12-12-11-25.md)

or 

> You can Download using Prometheus stack in kubernetes [Document Link](https://github.com/Avesh-code/learning-Kubernetes/blob/main/Day-11-11-11-25.md)

