# Comprehensive Monitoring Toolkit Setup Guide

This guide provides detailed instructions for setting up, configuring, and deploying the Python Monitoring Toolkit in various environments.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation Methods](#installation-methods)
3. [Component Overview](#component-overview)
4. [Basic Setup](#basic-setup)
5. [Advanced Configuration](#advanced-configuration)
6. [Integration Examples](#integration-examples)
7. [Production Deployment](#production-deployment)
8. [Monitoring Best Practices](#monitoring-best-practices)
9. [Troubleshooting Guide](#troubleshooting-guide)
10. [Performance Tuning](#performance-tuning)

## Prerequisites

### System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| Python | 3.8+ | 3.9+ |
| RAM | 512MB | 2GB+ |
| CPU | 1 core | 2+ cores |
| Disk Space | 500MB | 2GB+ |
| OS | Linux/macOS/Windows | Ubuntu 20.04+ |

### Required Permissions

- **System Monitoring**: Read access to `/proc` (Linux) or equivalent
- **Log Analysis**: Read access to log files
- **Network Checks**: May require elevated privileges for ICMP ping
- **Port Binding**: Permission to bind to dashboard port (default: 5000)

## Installation Methods

### Method 1: Quick Install (Recommended for Testing)

```bash
# Clone the repository
git clone https://github.com/dnoice/Monitoring-Toolkit.git
cd Monitoring-Toolkit

# Install minimal dependencies
pip install psutil flask flask-socketio plotly numpy scikit-learn pyyaml jinja2

# Run quick start
python quick_start_example.py
```

### Method 2: Full Install (Recommended for Production)

```bash
# Clone the repository
git clone https://github.com/dnoice/Monitoring-Toolkit.git
cd Monitoring-Toolkit

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install all dependencies
pip install -r requirements.txt

# Install optional dependencies based on needs
pip install asyncpg  # For PostgreSQL monitoring
pip install aioredis # For Redis monitoring
pip install motor    # For MongoDB monitoring
```

### Method 3: Docker Install

```bash
# Clone the repository
git clone https://github.com/dnoice/Monitoring-Toolkit.git
cd Monitoring-Toolkit

# Build Docker image
docker build -t monitoring-toolkit .

# Run container
docker run -d --name monitoring \
  -p 5000:5000 \
  -v $(pwd)/config:/app/config \
  -v $(pwd)/logs:/app/logs \
  monitoring-toolkit
```

## Component Overview

### 1. System Resource Monitor
Monitors system resources in real-time.

**Key Features:**
- CPU usage (per-core and total)
- Memory (RAM and swap)
- Disk usage and I/O
- Network traffic and speed
- Process monitoring

**Configuration:**
```yaml
system_monitor:
  enabled: true
  history_size: 300        # Number of data points to keep
  sample_interval: 1.0     # Seconds between samples
  thresholds:
    cpu: 80.0             # Alert when CPU > 80%
    memory: 85.0          # Alert when memory > 85%
    disk: 90.0            # Alert when disk > 90%
    network_mbps: 100.0   # Alert when network > 100 Mbps
```

### 2. Log Analyzer
Analyzes logs for patterns, anomalies, and issues.

**Supported Formats:**
- Apache Combined/Common
- Nginx
- Syslog
- JSON logs
- Python logs
- Custom patterns

**Configuration:**
```yaml
log_analyzer:
  enabled: true
  db_path: "./data/logs.db"
  analysis_interval: 300    # Analyze every 5 minutes
  anomaly_window: 60        # Look for anomalies in last 60 minutes
  pattern_min_support: 5    # Minimum occurrences for pattern
  watch_files:
    - "/var/log/nginx/access.log"
    - "/var/log/app/application.log"
  custom_patterns:
    - name: "custom_app"
      regex: '^(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) \[(\w+)\] (.*)$'
      fields: ["timestamp", "level", "message"]
```

### 3. Service Health Checker
Monitors various services and endpoints.

**Supported Types:**
- HTTP/HTTPS endpoints
- TCP ports
- PostgreSQL databases
- Redis instances
- MongoDB databases
- DNS resolution
- Custom checks

**Configuration:**
```yaml
health_checker:
  enabled: true
  services:
    - name: "web_app"
      type: "https"
      endpoint: "https://example.com"
      health_check_path: "/health"
      interval: 30
      timeout: 10
      expected_status: 200
      headers:
        User-Agent: "Monitoring/1.0"
      thresholds:
        response_time: 2.0
        verify_ssl: true
```

### 4. Alert Manager
Handles alert routing and notifications.

**Supported Channels:**
- Email (SMTP)
- Slack webhooks
- Discord webhooks
- PagerDuty
- Generic webhooks
- Console/Log output

**Configuration:**
```yaml
alert_manager:
  enabled: true
  channels:
    - name: "email_critical"
      type: "email"
      enabled: true
      config:
        smtp_host: "smtp.gmail.com"
        smtp_port: 587
        smtp_tls: true
        smtp_username: "alerts@example.com"
        smtp_password: "${SMTP_PASSWORD}"  # Use environment variable
        from: "alerts@example.com"
        to:
          - "ops-team@example.com"
      severity_filter: ["critical", "error"]
      
  rules:
    - name: "critical_alerts"
      condition: "severity:critical"
      severity: "critical"
      message_template: |
        🚨 {{ alert.name }}
        {{ alert.message }}
        Time: {{ alert.timestamp }}
      channels: ["email_critical"]
      cooldown: 300  # 5 minutes
```

### 5. Real-time Dashboard
Web-based monitoring dashboard.

**Features:**
- Real-time metrics visualization
- Service status overview
- Active alerts display
- Log streaming
- Process monitoring

**Configuration:**
```yaml
dashboard:
  enabled: true
  host: "0.0.0.0"    # Listen on all interfaces
  port: 5000         # Web server port
  debug: false       # Production setting
```

## Basic Setup

### Step 1: Create Directory Structure

```bash
mkdir -p monitoring/{config,data,logs}
cd monitoring
```

### Step 2: Download Components

```bash
# Clone all files from repository
git clone https://github.com/dnoice/Monitoring-Toolkit.git .
```

### Step 3: Create Basic Configuration

Create `monitoring_config.yaml`:

```yaml
# Basic monitoring configuration
system_monitor:
  enabled: true
  sample_interval: 2.0
  thresholds:
    cpu: 80.0
    memory: 85.0

log_analyzer:
  enabled: true
  db_path: "./data/logs.db"
  watch_files:
    - "./logs/app.log"

health_checker:
  enabled: true
  services:
    - name: "local_app"
      type: "http"
      endpoint: "http://localhost:8080"
      interval: 30

alert_manager:
  enabled: true
  channels:
    - name: "console"
      type: "log"
      config: {}

dashboard:
  enabled: true
  host: "localhost"
  port: 5000

integration:
  logging:
    level: "INFO"
    file: "./logs/monitoring.log"
```

### Step 4: Set Environment Variables

```bash
# Create .env file
cat > .env << EOF
SMTP_PASSWORD=your_password_here
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/YOUR/WEBHOOK/URL
DB_PASSWORD=your_db_password
EOF

# Load environment variables
export $(cat .env | xargs)
```

### Step 5: Run the System

```bash
# Test configuration
python test_monitoring_system.py

# Start monitoring
python monitoring_orchestrator.py
```

## Advanced Configuration

### Custom Service Health Checks

Create custom health checks for your services:

```python
# custom_checks.py
from service_health_checker import ServiceConfig, HealthCheckResult, ServiceStatus
from datetime import datetime
import aiohttp

async def check_elasticsearch(service: ServiceConfig) -> HealthCheckResult:
    """Custom Elasticsearch health check"""
    start_time = datetime.now()
    
    try:
        async with aiohttp.ClientSession() as session:
            # Check cluster health
            async with session.get(
                f"{service.endpoint}/_cluster/health",
                timeout=aiohttp.ClientTimeout(total=service.timeout)
            ) as response:
                data = await response.json()
                
                # Map ES status to our status
                status_map = {
                    'green': ServiceStatus.HEALTHY,
                    'yellow': ServiceStatus.DEGRADED,
                    'red': ServiceStatus.UNHEALTHY
                }
                
                return HealthCheckResult(
                    service_name=service.name,
                    service_type="elasticsearch",
                    status=status_map.get(data['status'], ServiceStatus.UNKNOWN),
                    response_time=(datetime.now() - start_time).total_seconds(),
                    timestamp=datetime.now(),
                    message=f"Cluster: {data['cluster_name']}, Status: {data['status']}",
                    details={
                        'cluster_name': data['cluster_name'],
                        'status': data['status'],
                        'number_of_nodes': data['number_of_nodes'],
                        'active_shards': data['active_shards']
                    }
                )
                
    except Exception as e:
        return HealthCheckResult(
            service_name=service.name,
            service_type="elasticsearch",
            status=ServiceStatus.UNHEALTHY,
            response_time=(datetime.now() - start_time).total_seconds(),
            timestamp=datetime.now(),
            message=f"Health check failed: {str(e)}",
            error=str(e)
        )
```

Use in configuration:

```yaml
health_checker:
  services:
    - name: "elasticsearch"
      type: "custom"
      endpoint: "http://localhost:9200"
      custom_check: "custom_checks.check_elasticsearch"
      interval: 60
      timeout: 10
```

### Custom Log Patterns

Add patterns for your application logs:

```yaml
log_analyzer:
  custom_patterns:
    # Spring Boot pattern
    - name: "spring_boot"
      regex: '^(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}\.\d{3})\s+(\w+)\s+(\d+)\s+---\s+\[(.*?)\]\s+(\S+)\s*:\s+(.*)$'
      fields: ["timestamp", "level", "pid", "thread", "logger", "message"]
    
    # Django pattern
    - name: "django"
      regex: '^\[(\d{2}/\w{3}/\d{4} \d{2}:\d{2}:\d{2})\] (\w+) (.*)$'
      fields: ["timestamp", "level", "message"]
    
    # Custom API logs
    - name: "api_request"
      regex: '^(\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2})\s+(\w+)\s+(\S+)\s+(\d{3})\s+(\d+)ms\s+"([^"]*)"$'
      fields: ["timestamp", "method", "path", "status", "duration", "user_agent"]
```

### Alert Routing

Configure sophisticated alert routing:

```yaml
alert_manager:
  # Suppression rules
  suppression_rules:
    - name: "quiet_hours"
      time_range:
        start: 22  # 10 PM
        end: 6     # 6 AM
      min_severity: "critical"  # Only critical alerts during quiet hours
    
    - name: "staging_suppress"
      tags:
        environment: "staging"
      min_severity: "error"  # Suppress warnings in staging
  
  # Escalation rules
  rules:
    - name: "database_critical"
      condition: "tag:service:database AND severity:critical"
      severity: "critical"
      message_template: |
        🚨 DATABASE CRITICAL ALERT
        
        Service: {{ alert.tags.service }}
        Message: {{ alert.message }}
        
        This requires immediate attention!
      channels: ["pagerduty", "email_oncall", "slack_critical"]
      cooldown: 60  # 1 minute for critical
    
    - name: "high_error_rate"
      condition: "tag:metric:error_rate"
      severity: "warning"
      message_template: |
        ⚠️ High Error Rate Detected
        
        {{ alert.message }}
        Current rate: {{ alert.tags.value }}%
      channels: ["slack_alerts"]
      cooldown: 900  # 15 minutes
```

## Integration Examples

### Integration with Prometheus

Export metrics in Prometheus format:

```python
# prometheus_exporter.py
from flask import Flask, Response
import prometheus_client
from prometheus_client import Gauge, generate_latest

app = Flask(__name__)

# Create metrics
cpu_usage = Gauge('system_cpu_usage_percent', 'CPU usage percentage')
memory_usage = Gauge('system_memory_usage_percent', 'Memory usage percentage')
disk_usage = Gauge('system_disk_usage_percent', 'Disk usage percentage', ['mountpoint'])

@app.route('/metrics')
def metrics():
    # Get latest metrics from monitoring system
    monitor = get_monitor_instance()  # Your monitoring instance
    summary = monitor.get_summary()
    
    # Update Prometheus metrics
    cpu_usage.set(summary['summary']['cpu_percent'])
    memory_usage.set(summary['summary']['memory_percent'])
    
    # Disk metrics per partition
    for disk in summary['summary']['disk_usage']:
        disk_usage.labels(mountpoint=disk['mount']).set(disk['percent'])
    
    return Response(generate_latest(), mimetype='text/plain')
```

### Integration with Grafana

Use the dashboard API to feed Grafana:

```yaml
# grafana_datasource.yaml
apiVersion: 1
datasources:
  - name: MonitoringToolkit
    type: prometheus
    url: http://monitoring-system:9090
    access: proxy
    isDefault: true
```

### Integration with Kubernetes

Deploy as a DaemonSet for node monitoring:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: monitoring-toolkit
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: monitoring-toolkit
  template:
    metadata:
      labels:
        app: monitoring-toolkit
    spec:
      hostNetwork: true
      hostPID: true
      containers:
      - name: monitoring
        image: monitoring-toolkit:latest
        securityContext:
          privileged: true
        volumeMounts:
        - name: proc
          mountPath: /host/proc
          readOnly: true
        - name: sys
          mountPath: /host/sys
          readOnly: true
        env:
        - name: HOST_PROC
          value: /host/proc
        - name: HOST_SYS
          value: /host/sys
      volumes:
      - name: proc
        hostPath:
          path: /proc
      - name: sys
        hostPath:
          path: /sys
```

## Production Deployment

### 1. Security Hardening

#### Use HTTPS for Dashboard

```python
# secure_dashboard.py
from flask import Flask
from flask_sslify import SSLify

app = Flask(__name__)
sslify = SSLify(app)

# Or use a reverse proxy (recommended)
```

Nginx configuration:

```nginx
server {
    listen 443 ssl http2;
    server_name monitoring.example.com;
    
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

#### Add Authentication

```python
# auth_dashboard.py
from flask_httpauth import HTTPBasicAuth
from werkzeug.security import generate_password_hash, check_password_hash

auth = HTTPBasicAuth()

users = {
    "admin": generate_password_hash("secure_password"),
    "viewer": generate_password_hash("read_only_password")
}

@auth.verify_password
def verify_password(username, password):
    if username in users and check_password_hash(users.get(username), password):
        return username

# Apply to routes
@app.route('/')
@auth.login_required
def index():
    return render_template('dashboard.html')
```

### 2. High Availability Setup

#### Multiple Dashboard Instances

```yaml
# docker-compose-ha.yml
version: '3.8'

services:
  monitoring1:
    image: monitoring-toolkit:latest
    environment:
      - INSTANCE_ID=1
    networks:
      - monitoring
    
  monitoring2:
    image: monitoring-toolkit:latest
    environment:
      - INSTANCE_ID=2
    networks:
      - monitoring
    
  loadbalancer:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - monitoring1
      - monitoring2
    networks:
      - monitoring

networks:
  monitoring:
    driver: bridge
```

### 3. Database Optimization

#### For Large-Scale Log Analysis

```sql
-- Create indexes for better performance
CREATE INDEX idx_logs_timestamp ON logs(timestamp);
CREATE INDEX idx_logs_level_timestamp ON logs(level, timestamp);
CREATE INDEX idx_logs_source_timestamp ON logs(source, timestamp);

-- Partition by date for large datasets
CREATE TABLE logs_2024_01 PARTITION OF logs
    FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

### 4. Resource Limits

#### Docker Resource Constraints

```yaml
services:
  monitoring:
    image: monitoring-toolkit:latest
    deploy:
      resources:
        limits:
          cpus: '2.0'
          memory: 2G
        reservations:
          cpus: '1.0'
          memory: 1G
```

#### Systemd Resource Limits

```ini
[Service]
# CPU and Memory limits
CPUQuota=200%
MemoryLimit=2G
MemoryHigh=1500M

# File descriptor limits
LimitNOFILE=65536
LimitNPROC=4096
```

## Monitoring Best Practices

### 1. Alerting Strategy

- **Alert Fatigue Prevention**: Set appropriate thresholds
- **Escalation Path**: Define clear escalation for different severities
- **Business Hours**: Consider time-based routing
- **Deduplication**: Use cooldown periods effectively

### 2. Data Retention

```yaml
integration:
  cleanup:
    enabled: true
    retention_days: 30
    
  # Different retention for different data types
  retention_policies:
    metrics: 7        # 7 days for high-frequency metrics
    logs: 30          # 30 days for logs
    alerts: 90        # 90 days for alert history
    anomalies: 180    # 180 days for anomaly data
```

### 3. Performance Optimization

```yaml
# Optimized configuration for production
system_monitor:
  sample_interval: 5.0      # 5 seconds instead of 1
  history_size: 720         # 1 hour of 5-second samples

log_analyzer:
  analysis_interval: 600    # 10 minutes
  batch_size: 10000         # Process in larger batches
  
health_checker:
  # Stagger health checks
  services:
    - name: "service1"
      interval: 30
      initial_delay: 0
    - name: "service2"
      interval: 30
      initial_delay: 10    # Offset by 10 seconds
```

## Troubleshooting Guide

### Common Issues and Solutions

#### 1. High Memory Usage

**Symptoms:**
- Dashboard becomes slow
- System monitor using excessive RAM

**Solutions:**
```yaml
# Reduce history size
system_monitor:
  history_size: 100  # Keep less historical data

# Increase intervals
log_analyzer:
  analysis_interval: 1800  # 30 minutes
```

#### 2. Dashboard WebSocket Disconnections

**Symptoms:**
- Dashboard shows "Disconnected" frequently
- Real-time updates stop working

**Solutions:**
- Check proxy configuration for WebSocket support
- Increase timeout values:

```nginx
proxy_read_timeout 86400;
proxy_send_timeout 86400;
proxy_connect_timeout 86400;
```

#### 3. Log Analyzer Performance

**Symptoms:**
- Log analysis takes too long
- High CPU usage during analysis

**Solutions:**
```python
# Add log rotation
import logging.handlers

handler = logging.handlers.RotatingFileHandler(
    'app.log',
    maxBytes=100*1024*1024,  # 100MB
    backupCount=5
)
```

#### 4. Alert Storm

**Symptoms:**
- Too many alerts being sent
- Email/Slack rate limits hit

**Solutions:**
```yaml
alert_manager:
  # Global rate limit
  global_rate_limit:
    max_alerts_per_hour: 50
    
  # Increase cooldown
  rules:
    - name: "default"
      cooldown: 1800  # 30 minutes
```

### Debug Mode

Enable comprehensive debugging:

```yaml
integration:
  logging:
    level: "DEBUG"
    format: "%(asctime)s - %(name)s - %(levelname)s - %(filename)s:%(lineno)d - %(funcName)s() - %(message)s"
    
  # Component-specific debugging
  debug:
    system_monitor: true
    log_analyzer: true
    health_checker: true
    alert_manager: true
    dashboard: true
```

### Health Check Endpoint

Add monitoring for the monitoring system:

```python
# healthcheck.py
@app.route('/health')
def health():
    checks = {
        'system_monitor': check_component_health('system_monitor'),
        'log_analyzer': check_component_health('log_analyzer'),
        'health_checker': check_component_health('health_checker'),
        'alert_manager': check_component_health('alert_manager'),
        'dashboard': True  # We're responding, so dashboard is up
    }
    
    overall = all(checks.values())
    status_code = 200 if overall else 503
    
    return jsonify({
        'status': 'healthy' if overall else 'unhealthy',
        'checks': checks,
        'timestamp': datetime.now().isoformat()
    }), status_code
```

## Performance Tuning

### 1. CPU Optimization

```yaml
# For CPU-constrained environments
system_monitor:
  sample_interval: 10.0     # Sample less frequently
  cpu_calculation_method: "simple"  # Use simpler calculations

log_analyzer:
  ml_features:
    anomaly_detection: false  # Disable ML features
    pattern_recognition: "basic"  # Use basic pattern matching
```

### 2. Memory Optimization

```python
# Use generators for large datasets
async def analyze_large_file(self, filepath: str):
    async with aiofiles.open(filepath, 'r') as f:
        async for line in f:  # Generator, doesn't load entire file
            yield self.process_line(line)
```

### 3. I/O Optimization

```yaml
# Batch operations
log_analyzer:
  batch_size: 5000          # Process more logs per batch
  commit_interval: 100      # Commit to DB less frequently

# Use connection pooling
health_checker:
  connection_pool:
    size: 10                # Reuse connections
    timeout: 30
```

### 4. Network Optimization

```yaml
# Reduce network overhead
dashboard:
  update_interval: 5        # Update dashboard every 5 seconds
  batch_updates: true       # Send updates in batches
  compression: true         # Enable WebSocket compression

health_checker:
  parallel_checks: 5        # Check 5 services simultaneously
  dns_cache_ttl: 300       # Cache DNS for 5 minutes
```

## Conclusion

This monitoring toolkit provides enterprise-grade monitoring capabilities with flexibility to scale from single-server deployments to large distributed systems. Start with the basic configuration and gradually add components and features as your monitoring needs grow.

For the latest updates and community support, visit the [GitHub repository](https://github.com/dnoice/Monitoring-Toolkit).

Remember: Good monitoring is not about collecting all possible data, but about collecting the *right* data and making it actionable through intelligent alerting and visualization.
