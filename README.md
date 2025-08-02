# 🚀 Python Monitoring Toolkit

A comprehensive, production-ready monitoring system with real-time dashboards, intelligent alerting, and advanced analytics.

![Python Version](https://img.shields.io/badge/python-3.8%2B-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Status](https://img.shields.io/badge/status-production%20ready-brightgreen)
[![GitHub](https://img.shields.io/badge/GitHub-dnoice%2FMonitoring--Toolkit-181717?logo=github)](https://github.com/dnoice/Monitoring-Toolkit)

## 🌟 Features

### Core Components

1. **System Resource Monitor** 📊
   - Real-time CPU, memory, disk, and network monitoring
   - Process tracking with resource usage
   - Configurable thresholds and alerts
   - Historical data with export capabilities

2. **Advanced Log Analyzer** 📝
   - Multi-format log parsing (Apache, Nginx, Syslog, JSON, etc.)
   - ML-powered anomaly detection
   - Pattern recognition and analysis
   - Full-text search with filters

3. **Service Health Checker** 🏥
   - Multi-protocol support (HTTP/HTTPS, TCP, PostgreSQL, Redis, MongoDB, DNS)
   - Custom health check support
   - Service uptime tracking
   - Configurable retry logic

4. **Alert Manager** 🚨
   - Multi-channel notifications (Email, Slack, Discord, PagerDuty, Webhooks)
   - Intelligent routing and suppression
   - Rate limiting and cooldown periods
   - Template-based messaging

5. **Real-time Dashboard** 📈
   - Beautiful dark-themed web interface
   - WebSocket live updates
   - Interactive Plotly charts
   - Responsive design

6. **Monitoring Orchestrator** 🎯
   - Centralized configuration
   - Component integration
   - Automated alert resolution
   - Scheduled tasks and cleanup

## 🚀 Quick Start

### Prerequisites

- Python 3.8 or higher
- pip (Python package manager)
- 1GB RAM minimum
- 500MB disk space

### Installation

1. **Clone the repository:**
```bash
git clone https://github.com/dnoice/Monitoring-Toolkit.git
cd Monitoring-Toolkit
```

2. **Install core dependencies:**
```bash
pip install psutil flask flask-socketio plotly numpy scikit-learn pyyaml jinja2
```

3. **Run the quick start example:**
```bash
python quick_start_example.py
```

4. **Access the dashboard:**
Open your browser to `http://localhost:5000`

## 📦 Full Installation

### Install all dependencies:
```bash
# Clone the repository if you haven't already
git clone https://github.com/dnoice/Monitoring-Toolkit.git
cd Monitoring-Toolkit

# Install all dependencies
pip install -r requirements.txt
```

### Optional dependencies for additional features:
```bash
# PostgreSQL monitoring
pip install asyncpg

# Redis monitoring
pip install aioredis

# MongoDB monitoring
pip install motor

# Advanced networking
pip install aioping aiodns
```

## 🔧 Configuration

### Basic Configuration

Create `monitoring_config.yaml`:

```yaml
system_monitor:
  enabled: true
  thresholds:
    cpu: 80.0
    memory: 85.0
    disk: 90.0

dashboard:
  enabled: true
  host: localhost
  port: 5000

alert_manager:
  channels:
    - name: console
      type: log
      enabled: true
      config: {}
```

### Advanced Configuration

See the example `monitoring_config.yaml` in the repository for full options including:
- Custom log patterns
- Service health checks
- Email/Slack notifications
- Data retention policies

## 🎯 Usage Examples

### Monitor System Resources

```python
from system_resource_monitor import SystemResourceMonitor

monitor = SystemResourceMonitor()
monitor.set_threshold('cpu', 75.0)
monitor.start_monitoring()

# Get current status
summary = monitor.get_summary()
print(f"CPU: {summary['summary']['cpu_percent']}%")
```

### Analyze Logs

```python
from log_analyzer import LogAnalyzer
import asyncio

async def analyze():
    analyzer = LogAnalyzer()
    await analyzer.analyze_file_async('app.log')
    
    # Find patterns
    patterns = analyzer.find_patterns()
    
    # Detect anomalies
    anomalies = analyzer.detect_anomalies()

asyncio.run(analyze())
```

### Monitor Services

```python
from service_health_checker import HealthChecker, ServiceConfig
import asyncio

async def check():
    checker = HealthChecker()
    
    # Add HTTP service
    checker.add_service(ServiceConfig(
        name="api",
        type="https",
        endpoint="https://api.example.com",
        health_check_path="/health"
    ))
    
    await checker.start()
    status = checker.get_status()

asyncio.run(check())
```

## 🧪 Testing

Run the comprehensive test suite:

```bash
python test_monitoring_system.py
```

This will verify:
- All dependencies are installed
- Components can be imported
- Basic functionality works
- Optional features availability

## 🚀 Production Deployment

### Docker

```bash
# Build the image
docker build -t monitoring-system .

# Run the container
docker run -d -p 5000:5000 \
  -v $(pwd)/config:/app/config \
  -v $(pwd)/logs:/app/logs \
  monitoring-system
```

### Docker Compose

```bash
# Using the provided docker-compose.yml
docker-compose up -d
```

### Systemd Service

```bash
# Copy the service file
sudo cp monitoring.service /etc/systemd/system/

# Enable and start
sudo systemctl enable monitoring
sudo systemctl start monitoring
```

### Kubernetes

```bash
# Apply the deployment
kubectl apply -f monitoring-deployment.yaml
```

## 📊 Dashboard Features

- **Real-time Metrics**: Live CPU, memory, disk, network graphs
- **Service Status**: Health status of all monitored services
- **Alert Display**: Active alerts with severity indicators
- **Log Viewer**: Streaming logs with syntax highlighting
- **Process Monitor**: Top processes by resource usage
- **WebSocket Updates**: Instant updates without page refresh

## 🔒 Security Best Practices

1. **Use environment variables for sensitive data:**
   ```bash
   export SMTP_PASSWORD="your-password"
   export SLACK_WEBHOOK_URL="https://..."
   ```

2. **Enable HTTPS for production dashboards**

3. **Implement authentication for the dashboard**

4. **Regular security updates:**
   ```bash
   pip install --upgrade -r requirements.txt
   ```

## 🛠️ Troubleshooting

### Common Issues

**High CPU Usage**
- Increase `sample_interval` in config
- Reduce `history_size`
- Disable unused features

**Import Errors**
- Run `python test_monitoring_system.py` to check dependencies
- Install missing packages with pip

**Dashboard Not Loading**
- Check if port 5000 is available
- Verify Flask and SocketIO are installed
- Check browser console for errors

**Alerts Not Sending**
- Verify channel configuration
- Check network connectivity
- Review logs for errors

### Debug Mode

Enable debug logging:
```yaml
integration:
  logging:
    level: DEBUG
```

## 📈 Performance Tips

1. **Optimize intervals based on needs:**
   - System monitoring: 1-5 seconds
   - Log analysis: 5-10 minutes
   - Health checks: 30-60 seconds

2. **Use appropriate retention:**
   - Metrics: 7-30 days
   - Logs: 30-90 days
   - Alerts: 7-14 days

3. **Scale horizontally:**
   - Multiple dashboard instances
   - Separate log analysis workers
   - Distributed health checkers

## 🤝 Contributing

We welcome contributions! Please feel free to submit a Pull Request to the [GitHub repository](https://github.com/dnoice/Monitoring-Toolkit).

### How to contribute:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Areas for improvement:
- Additional notification channels
- More service health check types
- Enhanced ML algorithms
- Dashboard UI improvements
- Performance optimizations

## 🐛 Issues

Found a bug or have a feature request? Please open an issue on the [GitHub Issues](https://github.com/dnoice/Monitoring-Toolkit/issues) page.

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/dnoice/Monitoring-Toolkit/blob/main/LICENSE) file for details.

## 🙏 Acknowledgments

- Built with Python and modern async/await patterns
- Uses best-in-class libraries: psutil, Flask, Plotly
- Inspired by enterprise monitoring solutions
- Designed for reliability and extensibility

## 📞 Support

For issues and questions:
1. Check the [troubleshooting section](#troubleshooting)
2. Run the test suite
3. Review the [comprehensive setup guide](https://github.com/dnoice/Monitoring-Toolkit/blob/main/monitoring_setup_guide.md)
4. Open an [issue on GitHub](https://github.com/dnoice/Monitoring-Toolkit/issues)

## 🌟 Star History

If you find this project useful, please consider giving it a star on [GitHub](https://github.com/dnoice/Monitoring-Toolkit)!

---

**Ready to monitor?** Start with `python quick_start_example.py` and scale up as needed!

**Repository:** [https://github.com/dnoice/Monitoring-Toolkit](https://github.com/dnoice/Monitoring-Toolkit)
