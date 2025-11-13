# 💡 Introduction to Observability
- Observability is the ability to understand the internal state of a system by analyzing the data it produces, including logs, metrics, and traces.

- Monitoring(Metrics): involves tracking system metrics like CPU usage, memory usage, and network performance. Provides alerts based on predefined thresholds and conditions
    - `Monitoring tells us what is happening.`
- Logging(Logs):  involves the collection of log data from various components of a system.
    - `Logging explains why it is happening.`
- Tracing(Traces): involves tracking the flow of a request or transaction as it moves through different services and components within a system.
    - `Tracing shows how it is happening.`

![Introduction to Observability](images/Introduction-to-Observability.png)

## 🤔 Why Monitoring?
- Monitoring helps us keep an eye on our systems to ensure they are working properly.
- Perpose:  maintaining the **health, performance, and security** of IT environments.
- It enables early detection of issues, ensuring that they can be addressed before causing significant downtime or data loss.

- We use monitoring to:
    - Detect Problems Early
    - Measure Performance:
    - Ensure Availability:

## 🤔 Why Observability?
- Observability helps us understand why our systems are behaving the way they are.
- It’s like having a detailed map and tools to explore and diagnose issues.

- We use observability to:
    - Diagnose Issues:
    - Understand Behavior:
    - Improve Systems:

![why-monitoring-why-observability](images/why-monitoring-why-observability.png)


## 🆚 What is the Exact Difference Between Monitoring and Observability?
- 🔥 Monitoring is the *`when and what`* of a system error, and observability is the *`why and how`*

| Category       | Monitoring                                   | Observability                                         |
|----------------|----------------------------------------------|------------------------------------------------------|
| Focus          | Checking if everything is working as expected| Understanding why things are happening in the system  |
| Data           | Collects metrics like CPU usage, memory usage, and error rates | Collects logs, metrics, and traces to provide a full picture |
| Alerts         | Sends notifications when something goes wrong| Correlates events and anomalies to identify root causes |
| Example        | If a server's CPU usage goes above 90%, monitoring will alert us | If a website is slow, observability helps us trace the user's request through different services to find the bottleneck |
| Insight        | Identifies potential issues before they become critical | Helps diagnose issues and understand system behavior |


## 🔭 Does Observability Cover Monitoring?
- Yes!! Monitoring is subset of Observability
- Observability is a broader concept that includes monitoring as one of its components.
- monitoring focuses on tracking specific metrics and alerting on predefined conditions
- observability provides a comprehensive understanding of the system by collecting and analyzing a wider range of data, including **logs, metrics, and traces**.

## 🖥️ What Can Be Monitored?
- Infrastructure: CPU usage, memory usage, disk I/O, network traffic.
- Applications: Response times, error rates, throughput.
- Databases: Query performance, connection pool usage, transaction rates.
- Network: Latency, packet loss, bandwidth usage.
- Security: Unauthorized access attempts, vulnerability scans, firewall logs.

## 👀 What Can Be Observed?
- Logs: Detailed records of events and transactions within the system.
- Metrics: Quantitative data points like CPU load, memory consumption, and request counts.
- Traces: Data that shows the flow of requests through various services and components.

## 🆚 Monitoring on Bare-Metal Servers vs. Monitoring Kubernetes
- Bare-Metal Servers:
    - Direct Access: Easier access to hardware metrics and logs.
    - Fewer Layers: Simpler environment with fewer abstraction layers.

- Kubernetes:
    - Dynamic Environment: Challenges with monitoring ephemeral containers and dynamic scaling.
    - Distributed Nature: Requires tools that can handle distributed systems and correlate data from multiple sources.

## 🆚 Observing on Bare-Metal Servers vs. Observing Kubernetes
- Bare-Metal Servers:
    - Simpler Observability: Easier to collect and correlate logs, metrics, and traces due to fewer components and layers.

- Kubernetes:
    - Complex Observability: Requires sophisticated tools to handle the dynamic and distributed nature of containers and microservices.
    - Integration: Necessitates the integration of multiple observability tools to get a complete picture of the system.

## ⚒️ What are the Tools Available?
- **Monitoring Tools**: Prometheus, Grafana, Nagios, Zabbix, PRTG.
- **Observability Tools**: ELK Stack (Elasticsearch, Logstash, Kibana), EFK Stack (Elasticsearch, FluentBit, Kibana) Splunk, Jaeger, Zipkin, New Relic, Dynatrace, Datadog.
---
# Monitoring

## Metrics vs Monitoring

Metrics are measurements or data points that tell you what is happening. For example, the number of steps you walk each day, your heart rate, or the temperature outside—these are all metrics.

Monitoring is the process of keeping an eye on these metrics over time to understand what’s normal, identify changes, and detect problems. It's like watching your step count daily to see if you're meeting your fitness goal or checking your heart rate to make sure it's in a healthy range.

## 🚀 Prometheus
- Prometheus is an open-source systems monitoring and alerting toolkit originally built at SoundCloud.
- It is known for its robust data model, powerful query language (PromQL), and the ability to generate alerts based on the collected time-series data.
- It can be configured and set up on both bare-metal servers and container environments like Kubernetes.

## 🏠 Prometheus Architecture
- The architecture of Prometheus is designed to be highly flexible, scalable, and modular.
- It consists of several core components, each responsible for a specific aspect of the monitoring process.

![Prometheus Architecture](images/prometheus-architecture.gif)

### 🔥 Prometheus Server
- Prometheus server is the core of the monitoring system. It is responsible for scraping metrics from various configured targets, storing them in its time-series database (TSDB), and serving queries through its HTTP API.
- Components:
    - **Retrieval**: This module handles the scraping of metrics from endpoints, which are discovered either through static configurations or dynamic service discovery methods.
    - **TSDB (Time Series Database)**: The data scraped from targets is stored in the TSDB, which is designed to handle high volumes of time-series data efficiently.
    - **HTTP Server**: This provides an API for querying data using PromQL, retrieving metadata, and interacting with other components of the Prometheus ecosystem.
- **Storage**: The scraped data is stored on local disk (HDD/SSD) in a format optimized for time-series data.

### 🌐 Service Discovery
- Service discovery automatically identifies and manages the list of scrape targets (i.e., services or applications) that Prometheus monitors.
- This is crucial in dynamic environments like Kubernetes where services are constantly being created and destroyed.
- Components:
    - **Kubernetes**: In Kubernetes environments, Prometheus can automatically discover services, pods, and nodes using Kubernetes API, ensuring it monitors the most up-to-date list of targets.
    - **File SD (Service Discovery)**: Prometheus can also read static target configurations from files, allowing for flexibility in environments where dynamic service discovery is not used.

### 📤 Pushgateway
- The Pushgateway is used to expose metrics from short-lived jobs or applications that cannot be scraped directly by Prometheus.
- These jobs push their metrics to the Pushgateway, which then makes them available for Prometheus to scrape(pull).
- Use Case:
    - It's particularly useful for batch jobs or tasks that have a limited lifespan and would otherwise not have their metrics collected.

### 🚨 Alertmanager
- The Alertmanager is responsible for managing alerts generated by the Prometheus server.
- It takes care of deduplicating, grouping, and routing alerts to the appropriate notification channels such as PagerDuty, email, or Slack.

### 🧲 Exporters
- Exporters are small applications that collect metrics from various third-party systems and expose them in a format Prometheus can scrape. They are essential for monitoring systems that do not natively support Prometheus.
- Types of Exporters:
    - Common exporters include the Node Exporter (for hardware metrics), the MySQL Exporter (for database metrics), and various other application-specific exporters.

### 🖥️ Prometheus Web UI
- The Prometheus Web UI allows users to explore the collected metrics data, run ad-hoc PromQL queries, and visualize the results directly within Prometheus.

### 📊 Grafana
- Grafana is a powerful dashboard and visualization tool that integrates with Prometheus to provide rich, customizable visualizations of the metrics data.

### 🔌 API Clients
- API clients interact with Prometheus through its HTTP API to fetch data, query metrics, and integrate Prometheus with other systems or custom applications.

# 🛠️  Installation & Configurations

> NOTE: I have Already done That using Kubernetes you can Access through [link](https://github.com/Avesh-code/learning-Kubernetes/blob/main/Day-11-11-11-25.md)
---
> NOTE: I have Also Installed on  Bare-Metal system for that you can see the documentation through the [link](https://github.com/Avesh-code/learning-Kubernetes/blob/main/Day-12-12-11-25.md)
--- 
* Further I will connect it using Kube-state-metrics and also configure Alert-Manager on my system bare metal.
