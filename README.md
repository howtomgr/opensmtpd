# OpenSMTPD Installation Guide

OpenSMTPD is a free and open-source Mail Server. A FREE implementation of the SMTP protocol

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 2 cores minimum (4+ cores recommended)
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 1GB for installation
  - Network: 25/587 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 25/587 (default opensmtpd port)
- **Dependencies**:
  - opensmtpd-extras
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install opensmtpd
sudo dnf install -y opensmtpd opensmtpd-extras

# Enable and start service
sudo systemctl enable --now opensmtpd

# Configure firewall
sudo firewall-cmd --permanent --add-service=opensmtpd
sudo firewall-cmd --reload

# Verify installation
opensmtpd --version || systemctl status opensmtpd
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install opensmtpd
sudo apt install -y opensmtpd opensmtpd-extras

# Enable and start service
sudo systemctl enable --now opensmtpd

# Configure firewall
sudo ufw allow 25/587

# Verify installation
opensmtpd --version || systemctl status opensmtpd
```

### Arch Linux

```bash
# Install opensmtpd
sudo pacman -S opensmtpd

# Enable and start service
sudo systemctl enable --now opensmtpd

# Verify installation
opensmtpd --version || systemctl status opensmtpd
```

### Alpine Linux

```bash
# Install opensmtpd
apk add --no-cache opensmtpd

# Enable and start service
rc-update add opensmtpd default
rc-service opensmtpd start

# Verify installation
opensmtpd --version || rc-service opensmtpd status
```

### openSUSE/SLES

```bash
# Install opensmtpd
sudo zypper install -y opensmtpd opensmtpd-extras

# Enable and start service
sudo systemctl enable --now opensmtpd

# Configure firewall
sudo firewall-cmd --permanent --add-service=opensmtpd
sudo firewall-cmd --reload

# Verify installation
opensmtpd --version || systemctl status opensmtpd
```

### macOS

```bash
# Using Homebrew
brew install opensmtpd

# Start service
brew services start opensmtpd

# Verify installation
opensmtpd --version
```

### FreeBSD

```bash
# Using pkg
pkg install opensmtpd

# Enable in rc.conf
echo 'opensmtpd_enable="YES"' >> /etc/rc.conf

# Start service
service opensmtpd start

# Verify installation
opensmtpd --version || service opensmtpd status
```

### Windows

```powershell
# Using Chocolatey
choco install opensmtpd

# Or using Scoop
scoop install opensmtpd

# Verify installation
opensmtpd --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/opensmtpd

# Set up basic configuration
sudo tee /etc/opensmtpd/opensmtpd.conf << 'EOF'
# OpenSMTPD Configuration
limit session 100, limit mta 50
EOF

# Test configuration
sudo opensmtpd -t || sudo opensmtpd configtest

# Reload service
sudo systemctl reload opensmtpd
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R opensmtpd:opensmtpd /etc/opensmtpd
sudo chmod 750 /etc/opensmtpd

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable opensmtpd

# Start service
sudo systemctl start opensmtpd

# Stop service
sudo systemctl stop opensmtpd

# Restart service
sudo systemctl restart opensmtpd

# Reload configuration
sudo systemctl reload opensmtpd

# Check status
sudo systemctl status opensmtpd

# View logs
sudo journalctl -u opensmtpd -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add opensmtpd default

# Start service
rc-service opensmtpd start

# Stop service
rc-service opensmtpd stop

# Restart service
rc-service opensmtpd restart

# Check status
rc-service opensmtpd status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'opensmtpd_enable="YES"' >> /etc/rc.conf

# Start service
service opensmtpd start

# Stop service
service opensmtpd stop

# Restart service
service opensmtpd restart

# Check status
service opensmtpd status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start opensmtpd
brew services stop opensmtpd
brew services restart opensmtpd

# Check status
brew services list | grep opensmtpd
```

### Windows Service Manager

```powershell
# Start service
net start opensmtpd

# Stop service
net stop opensmtpd

# Using PowerShell
Start-Service opensmtpd
Stop-Service opensmtpd
Restart-Service opensmtpd

# Check status
Get-Service opensmtpd
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/opensmtpd/opensmtpd.conf << 'EOF'
limit session 100, limit mta 50
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart opensmtpd
```

### Clustering and High Availability

```bash
# Configure clustering (if supported)
# See official documentation for cluster setup

# Basic load balancing setup example
# Configure multiple instances on different ports
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream opensmtpd_backend {
    server 127.0.0.1:25/587;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name opensmtpd.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name opensmtpd.example.com;

    ssl_certificate /etc/ssl/certs/opensmtpd.example.com.crt;
    ssl_certificate_key /etc/ssl/private/opensmtpd.example.com.key;

    location / {
        proxy_pass http://opensmtpd_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support (if needed)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName opensmtpd.example.com
    Redirect permanent / https://opensmtpd.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName opensmtpd.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/opensmtpd.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/opensmtpd.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:25/587/
    ProxyPassReverse / http://127.0.0.1:25/587/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:25/587/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend opensmtpd_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/opensmtpd.pem
    redirect scheme https if !{ ssl_fc }
    default_backend opensmtpd_backend

backend opensmtpd_backend
    balance roundrobin
    option httpchk GET /health
    server opensmtpd1 127.0.0.1:25/587 check
    server opensmtpd2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R opensmtpd:opensmtpd /etc/opensmtpd
sudo chmod 750 /etc/opensmtpd

# Configure firewall
sudo firewall-cmd --permanent --add-service=opensmtpd
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/opensmtpd.conf << 'EOF'
[opensmtpd]
enabled = true
port = 25/587
filter = opensmtpd
logpath = /var/log/opensmtpd/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/opensmtpd.key \
    -out /etc/ssl/certs/opensmtpd.crt

# Configure SSL in opensmtpd
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE opensmtpd_db;
CREATE USER opensmtpd_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE opensmtpd_db TO opensmtpd_user;
EOF

# Configure opensmtpd to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE opensmtpd_db;
CREATE USER 'opensmtpd_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON opensmtpd_db.* TO 'opensmtpd_user'@'localhost';
FLUSH PRIVILEGES;
EOF
```

## Performance Optimization

### System Tuning

```bash
# Kernel parameters
sudo tee -a /etc/sysctl.conf << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.core.netdev_max_backlog = 5000
vm.swappiness = 10
EOF

sudo sysctl -p

# OpenSMTPD specific tuning
limit session 100, limit mta 50
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
opensmtpd soft nofile 65535
opensmtpd hard nofile 65535
opensmtpd soft nproc 32768
opensmtpd hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'opensmtpd'
    static_configs:
      - targets: ['localhost:25/587']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet opensmtpd; then
    echo "OpenSMTPD is running"
    exit 0
else
    echo "OpenSMTPD is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/opensmtpd << 'EOF'
/var/log/opensmtpd/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 opensmtpd opensmtpd
    postrotate
        systemctl reload opensmtpd > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# OpenSMTPD backup script
BACKUP_DIR="/backup/opensmtpd"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop opensmtpd

# Backup configuration
tar -czf "$BACKUP_DIR/opensmtpd-config-$DATE.tar.gz" /etc/opensmtpd

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/opensmtpd-data-$DATE.tar.gz" /var/lib/opensmtpd

# Start service
systemctl start opensmtpd

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop opensmtpd

# Restore configuration
sudo tar -xzf /backup/opensmtpd/opensmtpd-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/opensmtpd/opensmtpd-data-*.tar.gz -C /

# Set permissions
sudo chown -R opensmtpd:opensmtpd /etc/opensmtpd
sudo chown -R opensmtpd:opensmtpd /var/lib/opensmtpd

# Start service
sudo systemctl start opensmtpd
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u opensmtpd -n 100
sudo tail -f /var/log/opensmtpd/*.log

# Check configuration
sudo opensmtpd -t || sudo opensmtpd configtest

# Check permissions
ls -la /etc/opensmtpd
ls -la /var/lib/opensmtpd
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 25/587
sudo netstat -tlnp | grep 25/587

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 25/587
nc -zv localhost 25/587
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep smtpd)
htop -p $(pgrep smtpd)

# Check connections
ss -ant | grep :25/587 | wc -l

# Monitor I/O
iotop -p $(pgrep smtpd)
```

### Debug Mode

```bash
# Run in debug mode
sudo opensmtpd -d
# or
sudo opensmtpd debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  opensmtpd:
    image: opensmtpd:latest
    container_name: opensmtpd
    ports:
      - "25/587:25/587"
    volumes:
      - ./config:/etc/opensmtpd
      - ./data:/var/lib/opensmtpd
    environment:
      - opensmtpd_CONFIG=/etc/opensmtpd/opensmtpd.conf
    restart: unless-stopped
    networks:
      - opensmtpd_net

networks:
  opensmtpd_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: opensmtpd
spec:
  replicas: 3
  selector:
    matchLabels:
      app: opensmtpd
  template:
    metadata:
      labels:
        app: opensmtpd
    spec:
      containers:
      - name: opensmtpd
        image: opensmtpd:latest
        ports:
        - containerPort: 25/587
        volumeMounts:
        - name: config
          mountPath: /etc/opensmtpd
      volumes:
      - name: config
        configMap:
          name: opensmtpd-config
---
apiVersion: v1
kind: Service
metadata:
  name: opensmtpd
spec:
  selector:
    app: opensmtpd
  ports:
  - port: 25/587
    targetPort: 25/587
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure OpenSMTPD
  hosts: all
  become: yes
  tasks:
    - name: Install opensmtpd
      package:
        name: opensmtpd
        state: present
    
    - name: Configure opensmtpd
      template:
        src: opensmtpd.conf.j2
        dest: /etc/opensmtpd/opensmtpd.conf
        owner: opensmtpd
        group: opensmtpd
        mode: '0640'
      notify: restart opensmtpd
    
    - name: Start and enable opensmtpd
      systemd:
        name: opensmtpd
        state: started
        enabled: yes
  
  handlers:
    - name: restart opensmtpd
      systemd:
        name: opensmtpd
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update opensmtpd

# Debian/Ubuntu
sudo apt update && sudo apt upgrade opensmtpd

# Arch Linux
sudo pacman -Syu opensmtpd

# Alpine Linux
apk update && apk upgrade opensmtpd

# openSUSE
sudo zypper update opensmtpd

# FreeBSD
pkg update && pkg upgrade opensmtpd

# Always backup before updates
tar -czf /backup/opensmtpd-pre-update-$(date +%Y%m%d).tar.gz /etc/opensmtpd

# Restart after updates
sudo systemctl restart opensmtpd
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/opensmtpd -name "*.log" -mtime +30 -delete

# Verify integrity
sudo opensmtpd --verify || sudo opensmtpd check

# Update databases (if applicable)
sudo opensmtpd-update-db

# Optimize performance
sudo opensmtpd-optimize

# Check for security updates
sudo opensmtpd --security-check
```

## Additional Resources

- Official Documentation: https://docs.opensmtpd.org/
- GitHub Repository: https://github.com/opensmtpd/opensmtpd
- Community Forum: https://forum.opensmtpd.org/
- Wiki: https://wiki.opensmtpd.org/
- Comparison vs Postfix, Exim, Sendmail, Qmail: https://docs.opensmtpd.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
