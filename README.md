# flarum Installation Guide

flarum is a free and open-source modern forum software. Flarum provides next-generation forum software that makes online discussion fun

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
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 500MB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default flarum port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
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

# Install flarum
sudo dnf install -y flarum

# Enable and start service
sudo systemctl enable --now flarum

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
flarum --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install flarum
sudo apt install -y flarum

# Enable and start service
sudo systemctl enable --now flarum

# Configure firewall
sudo ufw allow 80

# Verify installation
flarum --version
```

### Arch Linux

```bash
# Install flarum
sudo pacman -S flarum

# Enable and start service
sudo systemctl enable --now flarum

# Verify installation
flarum --version
```

### Alpine Linux

```bash
# Install flarum
apk add --no-cache flarum

# Enable and start service
rc-update add flarum default
rc-service flarum start

# Verify installation
flarum --version
```

### openSUSE/SLES

```bash
# Install flarum
sudo zypper install -y flarum

# Enable and start service
sudo systemctl enable --now flarum

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
flarum --version
```

### macOS

```bash
# Using Homebrew
brew install flarum

# Start service
brew services start flarum

# Verify installation
flarum --version
```

### FreeBSD

```bash
# Using pkg
pkg install flarum

# Enable in rc.conf
echo 'flarum_enable="YES"' >> /etc/rc.conf

# Start service
service flarum start

# Verify installation
flarum --version
```

### Windows

```bash
# Using Chocolatey
choco install flarum

# Or using Scoop
scoop install flarum

# Verify installation
flarum --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/flarum

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
flarum --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable flarum

# Start service
sudo systemctl start flarum

# Stop service
sudo systemctl stop flarum

# Restart service
sudo systemctl restart flarum

# Check status
sudo systemctl status flarum

# View logs
sudo journalctl -u flarum -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add flarum default

# Start service
rc-service flarum start

# Stop service
rc-service flarum stop

# Restart service
rc-service flarum restart

# Check status
rc-service flarum status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'flarum_enable="YES"' >> /etc/rc.conf

# Start service
service flarum start

# Stop service
service flarum stop

# Restart service
service flarum restart

# Check status
service flarum status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start flarum
brew services stop flarum
brew services restart flarum

# Check status
brew services list | grep flarum
```

### Windows Service Manager

```powershell
# Start service
net start flarum

# Stop service
net stop flarum

# Using PowerShell
Start-Service flarum
Stop-Service flarum
Restart-Service flarum

# Check status
Get-Service flarum
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream flarum_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name flarum.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name flarum.example.com;

    ssl_certificate /etc/ssl/certs/flarum.example.com.crt;
    ssl_certificate_key /etc/ssl/private/flarum.example.com.key;

    location / {
        proxy_pass http://flarum_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName flarum.example.com
    Redirect permanent / https://flarum.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName flarum.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/flarum.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/flarum.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend flarum_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/flarum.pem
    redirect scheme https if !{ ssl_fc }
    default_backend flarum_backend

backend flarum_backend
    balance roundrobin
    server flarum1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R flarum:flarum /etc/flarum
sudo chmod 750 /etc/flarum

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status flarum

# View logs
sudo journalctl -u flarum -f

# Monitor resource usage
top -p $(pgrep flarum)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/flarum"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/flarum-backup-$DATE.tar.gz" /etc/flarum /var/lib/flarum

echo "Backup completed: $BACKUP_DIR/flarum-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop flarum

# Restore from backup
tar -xzf /backup/flarum/flarum-backup-*.tar.gz -C /

# Start service
sudo systemctl start flarum
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u flarum -n 100
sudo tail -f /var/log/flarum/flarum.log

# Check configuration
flarum --version

# Check permissions
ls -la /etc/flarum
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep flarum)

# Check disk I/O
iotop -p $(pgrep flarum)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  flarum:
    image: flarum:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/flarum
      - ./data:/var/lib/flarum
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update flarum

# Debian/Ubuntu
sudo apt update && sudo apt upgrade flarum

# Arch Linux
sudo pacman -Syu flarum

# Alpine Linux
apk update && apk upgrade flarum

# openSUSE
sudo zypper update flarum

# FreeBSD
pkg update && pkg upgrade flarum

# Always backup before updates
tar -czf /backup/flarum-pre-update-$(date +%Y%m%d).tar.gz /etc/flarum

# Restart after updates
sudo systemctl restart flarum
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/flarum

# Clean old logs
find /var/log/flarum -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/flarum
```

## Additional Resources

- Official Documentation: https://docs.flarum.org/
- GitHub Repository: https://github.com/flarum/flarum
- Community Forum: https://forum.flarum.org/
- Best Practices Guide: https://docs.flarum.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
