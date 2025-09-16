# Amavisd-new Installation Guide

Amavisd-new is a free and open-source Mail Filter. Interface between mailer and content checkers

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
  - Network: 10024 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 10024 (default amavisd-new port)
- **Dependencies**:
  - perl, spamassassin, clamav
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

# Install amavisd-new
sudo dnf install -y amavisd-new perl, spamassassin, clamav

# Enable and start service
sudo systemctl enable --now amavisd

# Configure firewall
sudo firewall-cmd --permanent --add-service=amavisd-new
sudo firewall-cmd --reload

# Verify installation
amavisd-new --version || systemctl status amavisd
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install amavisd-new
sudo apt install -y amavisd-new perl, spamassassin, clamav

# Enable and start service
sudo systemctl enable --now amavisd

# Configure firewall
sudo ufw allow 10024

# Verify installation
amavisd-new --version || systemctl status amavisd
```

### Arch Linux

```bash
# Install amavisd-new
sudo pacman -S amavisd-new

# Enable and start service
sudo systemctl enable --now amavisd

# Verify installation
amavisd-new --version || systemctl status amavisd
```

### Alpine Linux

```bash
# Install amavisd-new
apk add --no-cache amavisd-new

# Enable and start service
rc-update add amavisd default
rc-service amavisd start

# Verify installation
amavisd-new --version || rc-service amavisd status
```

### openSUSE/SLES

```bash
# Install amavisd-new
sudo zypper install -y amavisd-new perl, spamassassin, clamav

# Enable and start service
sudo systemctl enable --now amavisd

# Configure firewall
sudo firewall-cmd --permanent --add-service=amavisd-new
sudo firewall-cmd --reload

# Verify installation
amavisd-new --version || systemctl status amavisd
```

### macOS

```bash
# Using Homebrew
brew install amavisd-new

# Start service
brew services start amavisd-new

# Verify installation
amavisd-new --version
```

### FreeBSD

```bash
# Using pkg
pkg install amavisd-new

# Enable in rc.conf
echo 'amavisd_enable="YES"' >> /etc/rc.conf

# Start service
service amavisd start

# Verify installation
amavisd-new --version || service amavisd status
```

### Windows

```powershell
# Using Chocolatey
choco install amavisd-new

# Or using Scoop
scoop install amavisd-new

# Verify installation
amavisd-new --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/amavisd

# Set up basic configuration
sudo tee /etc/amavisd/amavisd-new.conf << 'EOF'
# Amavisd-new Configuration
$max_servers = 4
EOF

# Test configuration
sudo amavisd-new -t || sudo amavisd configtest

# Reload service
sudo systemctl reload amavisd
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R amavisd-new:amavisd-new /etc/amavisd
sudo chmod 750 /etc/amavisd

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable amavisd

# Start service
sudo systemctl start amavisd

# Stop service
sudo systemctl stop amavisd

# Restart service
sudo systemctl restart amavisd

# Reload configuration
sudo systemctl reload amavisd

# Check status
sudo systemctl status amavisd

# View logs
sudo journalctl -u amavisd -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add amavisd default

# Start service
rc-service amavisd start

# Stop service
rc-service amavisd stop

# Restart service
rc-service amavisd restart

# Check status
rc-service amavisd status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'amavisd_enable="YES"' >> /etc/rc.conf

# Start service
service amavisd start

# Stop service
service amavisd stop

# Restart service
service amavisd restart

# Check status
service amavisd status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start amavisd-new
brew services stop amavisd-new
brew services restart amavisd-new

# Check status
brew services list | grep amavisd-new
```

### Windows Service Manager

```powershell
# Start service
net start amavisd

# Stop service
net stop amavisd

# Using PowerShell
Start-Service amavisd
Stop-Service amavisd
Restart-Service amavisd

# Check status
Get-Service amavisd
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/amavisd/amavisd-new.conf << 'EOF'
$max_servers = 4
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart amavisd
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
upstream amavisd-new_backend {
    server 127.0.0.1:10024;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name amavisd-new.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name amavisd-new.example.com;

    ssl_certificate /etc/ssl/certs/amavisd-new.example.com.crt;
    ssl_certificate_key /etc/ssl/private/amavisd-new.example.com.key;

    location / {
        proxy_pass http://amavisd-new_backend;
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
    ServerName amavisd-new.example.com
    Redirect permanent / https://amavisd-new.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName amavisd-new.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/amavisd-new.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/amavisd-new.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:10024/
    ProxyPassReverse / http://127.0.0.1:10024/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:10024/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend amavisd-new_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/amavisd-new.pem
    redirect scheme https if !{ ssl_fc }
    default_backend amavisd-new_backend

backend amavisd-new_backend
    balance roundrobin
    option httpchk GET /health
    server amavisd-new1 127.0.0.1:10024 check
    server amavisd-new2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R amavisd-new:amavisd-new /etc/amavisd
sudo chmod 750 /etc/amavisd

# Configure firewall
sudo firewall-cmd --permanent --add-service=amavisd-new
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/amavisd-new.conf << 'EOF'
[amavisd-new]
enabled = true
port = 10024
filter = amavisd-new
logpath = /var/log/amavisd/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/amavisd-new.key \
    -out /etc/ssl/certs/amavisd-new.crt

# Configure SSL in amavisd-new
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE amavisd-new_db;
CREATE USER amavisd-new_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE amavisd-new_db TO amavisd-new_user;
EOF

# Configure amavisd-new to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE amavisd-new_db;
CREATE USER 'amavisd-new_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON amavisd-new_db.* TO 'amavisd-new_user'@'localhost';
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

# Amavisd-new specific tuning
$max_servers = 4
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
amavisd-new soft nofile 65535
amavisd-new hard nofile 65535
amavisd-new soft nproc 32768
amavisd-new hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'amavisd-new'
    static_configs:
      - targets: ['localhost:10024']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet amavisd; then
    echo "Amavisd-new is running"
    exit 0
else
    echo "Amavisd-new is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/amavisd-new << 'EOF'
/var/log/amavisd/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 amavisd-new amavisd-new
    postrotate
        systemctl reload amavisd > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Amavisd-new backup script
BACKUP_DIR="/backup/amavisd-new"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop amavisd

# Backup configuration
tar -czf "$BACKUP_DIR/amavisd-new-config-$DATE.tar.gz" /etc/amavisd

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/amavisd-new-data-$DATE.tar.gz" /var/lib/amavisd-new

# Start service
systemctl start amavisd

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop amavisd

# Restore configuration
sudo tar -xzf /backup/amavisd-new/amavisd-new-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/amavisd-new/amavisd-new-data-*.tar.gz -C /

# Set permissions
sudo chown -R amavisd-new:amavisd-new /etc/amavisd
sudo chown -R amavisd-new:amavisd-new /var/lib/amavisd-new

# Start service
sudo systemctl start amavisd
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u amavisd -n 100
sudo tail -f /var/log/amavisd/*.log

# Check configuration
sudo amavisd-new -t || sudo amavisd configtest

# Check permissions
ls -la /etc/amavisd
ls -la /var/lib/amavisd-new
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 10024
sudo netstat -tlnp | grep 10024

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 10024
nc -zv localhost 10024
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep amavisd)
htop -p $(pgrep amavisd)

# Check connections
ss -ant | grep :10024 | wc -l

# Monitor I/O
iotop -p $(pgrep amavisd)
```

### Debug Mode

```bash
# Run in debug mode
sudo amavisd-new -d
# or
sudo amavisd debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  amavisd-new:
    image: amavisd-new:latest
    container_name: amavisd-new
    ports:
      - "10024:10024"
    volumes:
      - ./config:/etc/amavisd
      - ./data:/var/lib/amavisd-new
    environment:
      - amavisd-new_CONFIG=/etc/amavisd/amavisd-new.conf
    restart: unless-stopped
    networks:
      - amavisd-new_net

networks:
  amavisd-new_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: amavisd-new
spec:
  replicas: 3
  selector:
    matchLabels:
      app: amavisd-new
  template:
    metadata:
      labels:
        app: amavisd-new
    spec:
      containers:
      - name: amavisd-new
        image: amavisd-new:latest
        ports:
        - containerPort: 10024
        volumeMounts:
        - name: config
          mountPath: /etc/amavisd
      volumes:
      - name: config
        configMap:
          name: amavisd-new-config
---
apiVersion: v1
kind: Service
metadata:
  name: amavisd-new
spec:
  selector:
    app: amavisd-new
  ports:
  - port: 10024
    targetPort: 10024
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure Amavisd-new
  hosts: all
  become: yes
  tasks:
    - name: Install amavisd-new
      package:
        name: amavisd-new
        state: present
    
    - name: Configure amavisd-new
      template:
        src: amavisd-new.conf.j2
        dest: /etc/amavisd/amavisd-new.conf
        owner: amavisd-new
        group: amavisd-new
        mode: '0640'
      notify: restart amavisd-new
    
    - name: Start and enable amavisd-new
      systemd:
        name: amavisd
        state: started
        enabled: yes
  
  handlers:
    - name: restart amavisd-new
      systemd:
        name: amavisd
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update amavisd-new

# Debian/Ubuntu
sudo apt update && sudo apt upgrade amavisd-new

# Arch Linux
sudo pacman -Syu amavisd-new

# Alpine Linux
apk update && apk upgrade amavisd-new

# openSUSE
sudo zypper update amavisd-new

# FreeBSD
pkg update && pkg upgrade amavisd-new

# Always backup before updates
tar -czf /backup/amavisd-new-pre-update-$(date +%Y%m%d).tar.gz /etc/amavisd

# Restart after updates
sudo systemctl restart amavisd
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/amavisd -name "*.log" -mtime +30 -delete

# Verify integrity
sudo amavisd-new --verify || sudo amavisd check

# Update databases (if applicable)
sudo amavisd-new-update-db

# Optimize performance
sudo amavisd-new-optimize

# Check for security updates
sudo amavisd-new --security-check
```

## Additional Resources

- Official Documentation: https://docs.amavisd-new.org/
- GitHub Repository: https://github.com/amavisd-new/amavisd-new
- Community Forum: https://forum.amavisd-new.org/
- Wiki: https://wiki.amavisd-new.org/
- Comparison vs MailScanner, MIMEDefang, Scrollout F1: https://docs.amavisd-new.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
