## ⚠️ Configure Alertmanager
- Review the Alertmanager configuration files located in `day-4/alerts-alertmanager-servicemonitor-manifest` but below is the brief overview
    - Before configuring Alertmanager, we need credentials to send emails. For this project, we are using Gmail, but any SMTP provider like AWS SES can be used. so please grab the credentials for that.
    - Open your Google account settings and search App password & create a new password & put the password in `day-4/alerts-alertmanager-servicemonitor-manifest/email-secret.yml`
    - One last thing, please add your email id in the `day-4/alerts-alertmanager-servicemonitor-manifest/alertmanagerconfig.yml`
- **HighCpuUsage**: Triggers a warning alert if the average CPU usage across instances exceeds 50% for more than 5 minutes.
- **PodRestart**: Triggers a critical alert immediately if any pod restarts more than 2 times.
- Apply the manifest files to your cluster by running:
```bash
kubectl apply -k alerts-alertmanager-servicemonitor-manifest/
```
- Wait for 4-5 minutes and then check the Prometheus UI to confirm that the custom metrics implemented in the Node.js application are available:
    - `http_requests_total`: counter
    - `http_request_duration_seconds`: histogram
    - `http_request_duration_summary_seconds`: summary
    - `node_gauge_example`: gauge for tracking async task duration

### Testing Alerts
- To test the alerting system, manually crash the container more than 2 times to trigger an alert (email notification).
- To crash the application container, hit the following endpoint
- `<<LOAD_BALANCER_DNS_NAME>>/crash`
- You should receive an email once the application container has restarted at least 3 times.

---
##  ⚠️ Configure Alertmanager in My system

- First Clone the Alertmanager from official Website with that build using go 
```bash
git clone https://github.com/prometheus/alertmanager.git
cd alertmanager
go build -o alertmanager ./cmd/alertmanager
sudo cp /path/to/alertmanager /usr/local/bin/
sudo chmod +x /usr/local/bin/alertmanager
sudo mkdir -p /etc/alertmanager
sudo mkdir -p /var/lib/alertmanager
```
- You can Remove the github we did
```bash
rm -rf github.com/alertmanager
```
- create a Alert Manager config file `alertmanager.yml` in desired location.
```bash
vim /etc/alertmanager/alertmanager.yml
```
```yml
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'System@office.com'
  smtp_auth_username: 'aveshpadaya02@gmail.com'
  smtp_auth_password: '----your-app-password----'

route:
  receiver: 'Testing'

receivers:
- name: 'Testing'
  email_configs:
  - to: 'aveshpadaya0@gmail.com'
```
- Also make the changes in config file of prometheus
```bash
vim prometheus.yml
```
```yml
alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']
```
- Restart and Reload Prometheus
```bash 
sudo systemctl daemon-reload
sudo systemctl restart prometheus
sudo systemctl status prometheus
```
- Create an Alert which can triger when the prometheus started
```bash
vim alert.rules.yml
```
```yml
groups:
- name: startup_alerts
  rules:
  - alert: SystemStartup
    expr: time() - process_start_time_seconds{job="prometheus"} < 300
    for: 1m
    labels:
      severity: critical
    annotations:
      summary: "Prometheus system startup detected"
      description: "Prometheus started less than 5 minutes ago."
```
- Configure alert rule in Prometheus
```bash
vim prometheus.yml
```
```yml
rule_files:
  - "path/to/alert.rules.yml" # change the path
```
- Restart and Check Status of Prometheus
```bash
sudo systemctl daemon-reload
sudo systemctl restart prometheus
sudo systemctl status prometheus
```
- Create a service `alertmanager.service`
```bash
sudo vim  /etc/systemd/system/alertmanager.service
```
```service
[Unit]
Description=Prometheus Alertmanager
After=network.target

[Service]
User=alertmanager
Group=alertmanager
Type=simple
ExecStart=/usr/local/bin/alertmanager \
  --config.file=/etc/alertmanager/alertmanager.yml \
  --storage.path=/var/lib/alertmanager

Restart=on-failure

[Install]
WantedBy=multi-user.target
```
- Enable and Start the Alert Manager
```bash
sudo systemctl daemon-reload
sudo systemctl enable alertmanager
sudo systemctl start alertmanager
```
- check status of Alertmanager
```bash
sudo systemctl status alertmanager
```
- If error plz check the logs 
```bash
sudo journalctl -u alertmanager -f
```
---
