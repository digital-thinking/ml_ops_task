# Python Guestbook Application with Monitoring

This repository contains a Python-based Guestbook application with a comprehensive monitoring and alerting setup.

## Overview

The application consists of three main components:
- Frontend service (Python Flask)
- Backend service (Python Flask)
- MongoDB database

Monitoring is provided by:
- Prometheus & Grafana for metrics monitoring
- ELK Stack (Elasticsearch, Logstash, Kibana) for logging
- Elastic APM for application performance monitoring
- Alertmanager for alert management

## Prerequisites

- Minikube 1.20+ (running on Windows)
- kubectl 1.20+
- Docker running
- Skaffold 2.0+

## Setup Instructions

### 1. Start Minikube

```bash
minikube start
```

### 2. Deploy Everything with Skaffold

The entire application with monitoring can be deployed using a single command:

```
skaffold dev
```

This will:
- Deploy the monitoring stack (Prometheus, Grafana, ELK, APM)
- Build the frontend and backend Docker images
- Deploy the application components (frontend, backend, MongoDB)
- Stream logs from the deployed containers

All this happens automatically without requiring any additional scripts or manual steps. The monitoring setup includes:
- Prometheus for metrics collection
- Grafana for metrics visualization
- Alertmanager for alert management
- Elasticsearch for log storage and analysis
- Kibana for log visualization
- APM Server for application performance monitoring
- Filebeat for log collection

This will:
- Build the frontend and backend Docker images
- Deploy the frontend, backend, and MongoDB services
- Stream logs from the deployed containers

### 4. Access the Application and Monitoring

- **Guestbook Application**: 
  ```bash
  minikube service python-guestbook-frontend
  ```

- **Grafana**:
  ```bash
  minikube service grafana -n monitoring
  ```
  Default credentials: admin/admin

- **Kibana**:
  ```bash
  minikube service kibana -n monitoring
  ```

## Monitoring Components

### Metrics Monitoring (Prometheus/Grafana)

The following metrics are collected:
- CPU and memory usage
- Request counts and rates
- Response time (latency)
- Error rates

Custom dashboards are provided for:
- System overview
- Application performance
- Database performance

### Log Monitoring (ELK Stack)

All application logs are collected by Filebeat and stored in Elasticsearch. Kibana provides:
- Log search and filtering
- Log visualization
- Alerts based on log patterns

### Application Performance Monitoring (Elastic APM)

The Python applications are instrumented with Elastic APM, which provides:
- Distributed tracing
- Transaction monitoring
- Error tracking
- Performance metrics

### Alerting

Alerting is configured for:
- High CPU/memory usage
- High error rates
- Slow response times
- Application crashes

Alerts are sent via email using Alertmanager.

## Alert Configuration

Alerts are defined in Prometheus rules and sent to Alertmanager, which routes them to the appropriate receiver (email in this case).

To configure email alerts:
1. Update `monitoring/alertmanager/alertmanager-config.yaml` with your SMTP server details
2. Apply the changes:
   ```bash
   kubectl apply -f monitoring/alertmanager/alertmanager-config.yaml
   ```

## Cleanup

To clean up resources:

```bash
skaffold delete
kubectl delete namespace monitoring
```

## Troubleshooting

- **Prometheus not scraping metrics**: Check the pod annotations and ensure the `/metrics` endpoint is accessible
- **Elastic APM not receiving data**: Verify the APM server URL is correctly set in the application
- **Filebeat not collecting logs**: Check the Filebeat configuration and log paths

## Additional Information

- Metrics are exposed on the `/metrics` endpoint for both frontend and backend services
- Application logs are written to stdout/stderr and collected by Filebeat
- Elastic APM transactions are sent directly to the APM server
