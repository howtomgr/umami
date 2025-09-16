# umami Installation Guide

umami is a free and open-source simple, fast, privacy-focused web analytics. Umami collects only the metrics you care about and respects user privacy, serving as a lightweight alternative to Google Analytics or Matomo

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
  - RAM: 512MB minimum (1GB+ recommended)
  - Storage: 1GB for analytics
  - Network: HTTPS for tracking
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 3000 (default umami port)
  - Database connection
- **Dependencies**:
  - Node.js 16.13+ or Docker
  - PostgreSQL 12+ or MySQL 8.0+
  - npm or yarn package manager
  - Git for source installation
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

# Install umami
sudo dnf install -y umami

# Enable and start service
sudo systemctl enable --now umami

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Verify installation
umami --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install umami
sudo apt install -y umami

# Enable and start service
sudo systemctl enable --now umami

# Configure firewall
sudo ufw allow 3000

# Verify installation
umami --version
```

### Arch Linux

```bash
# Install umami
sudo pacman -S umami

# Enable and start service
sudo systemctl enable --now umami

# Verify installation
umami --version
```

### Alpine Linux

```bash
# Install umami
apk add --no-cache umami

# Enable and start service
rc-update add umami default
rc-service umami start

# Verify installation
umami --version
```

### openSUSE/SLES

```bash
# Install umami
sudo zypper install -y umami

# Enable and start service
sudo systemctl enable --now umami

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Verify installation
umami --version
```

### macOS

```bash
# Using Homebrew
brew install umami

# Start service
brew services start umami

# Verify installation
umami --version
```

### FreeBSD

```bash
# Using pkg
pkg install umami

# Enable in rc.conf
echo 'umami_enable="YES"' >> /etc/rc.conf

# Start service
service umami start

# Verify installation
umami --version
```

### Windows

```bash
# Using Chocolatey
choco install umami

# Or using Scoop
scoop install umami

# Verify installation
umami --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/umami

# Set up basic configuration
cat > /etc/umami/.env << 'EOF'
# Database connection
DATABASE_URL=postgresql://umami:password@localhost:5432/umami
# Or for MySQL:
# DATABASE_URL=mysql://umami:password@localhost:3306/umami

# Application settings
APP_SECRET=your-random-secret-string
CLIENT_IP_HEADER=
REMOVE_TRAILING_SLASH=1
HOSTNAME=0.0.0.0
PORT=3000

# Optional settings
DISABLE_BOT_CHECK=0
DISABLE_LOGIN=0
FORCE_SSL=0
CORS_MAX_AGE=86400
EOF

# Test configuration
umami --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable umami

# Start service
sudo systemctl start umami

# Stop service
sudo systemctl stop umami

# Restart service
sudo systemctl restart umami

# Check status
sudo systemctl status umami

# View logs
sudo journalctl -u umami -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add umami default

# Start service
rc-service umami start

# Stop service
rc-service umami stop

# Restart service
rc-service umami restart

# Check status
rc-service umami status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'umami_enable="YES"' >> /etc/rc.conf

# Start service
service umami start

# Stop service
service umami stop

# Restart service
service umami restart

# Check status
service umami status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start umami
brew services stop umami
brew services restart umami

# Check status
brew services list | grep umami
```

### Windows Service Manager

```powershell
# Start service
net start umami

# Stop service
net stop umami

# Using PowerShell
Start-Service umami
Stop-Service umami
Restart-Service umami

# Check status
Get-Service umami
```

## Advanced Configuration

### Environment Variables
```bash
# Core settings
APP_SECRET=generate-long-random-string  # Required
DATABASE_URL=postgresql://user:pass@host:5432/umami

# Network configuration
HOSTNAME=0.0.0.0
PORT=3000
BASE_PATH=/analytics  # If serving from subdirectory

# Security settings
DISABLE_LOGIN=0  # Set to 1 to disable login
DISABLE_BOT_CHECK=0  # Set to 1 to track bots
FORCE_SSL=1  # Force HTTPS
SECURE_PROXY=1  # If behind proxy
CLIENT_IP_HEADER=X-Forwarded-For

# Performance
LOG_QUERY=1  # Log database queries
CORS_MAX_AGE=86400
CACHE_TTL=3600  # seconds

# Tracking settings
COLLECT_API_ENDPOINT=/api/collect
TRACKER_SCRIPT_NAME=umami
REMOVE_TRAILING_SLASH=1
DATA_TTL=90  # days to keep raw data
```

### Database Configuration

#### PostgreSQL Optimization
```sql
-- Create optimized database
CREATE DATABASE umami WITH 
    ENCODING = 'UTF8'
    LC_COLLATE = 'en_US.UTF-8'
    LC_CTYPE = 'en_US.UTF-8'
    CONNECTION LIMIT = 100;

-- Create user
CREATE USER umami WITH PASSWORD 'secure-password';
GRANT ALL PRIVILEGES ON DATABASE umami TO umami;

-- Performance tuning
ALTER DATABASE umami SET shared_buffers TO '256MB';
ALTER DATABASE umami SET effective_cache_size TO '1GB';
ALTER DATABASE umami SET work_mem TO '4MB';
```

#### MySQL Optimization  
```sql
-- Create database
CREATE DATABASE umami CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Create user
CREATE USER 'umami'@'%' IDENTIFIED BY 'secure-password';
GRANT ALL PRIVILEGES ON umami.* TO 'umami'@'%';
FLUSH PRIVILEGES;

-- Optimize for analytics workload
SET GLOBAL innodb_buffer_pool_size = 268435456;
SET GLOBAL innodb_log_file_size = 134217728;
SET GLOBAL max_connections = 200;
```

### Custom Tracking Script
```javascript
// Custom umami configuration
(function() {
    // Advanced tracking options
    window.umami = window.umami || {};
    
    // Track custom events
    window.umami.track = function(event, data) {
        if (window.umami && window.umami.trackEvent) {
            window.umami.trackEvent(event, data);
        }
    };
    
    // Custom settings
    window.umami.autoTrack = true;
    window.umami.doNotTrack = true;
    window.umami.domains = ['example.com', 'www.example.com'];
    window.umami.hostUrl = 'https://analytics.example.com';
    window.umami.websiteId = 'your-website-id';
})();
```

### Docker Compose Production Setup
```yaml
version: '3'

services:
  umami:
    image: ghcr.io/umami-software/umami:postgresql-latest
    environment:
      DATABASE_URL: postgresql://umami:password@db:5432/umami
      APP_SECRET: your-app-secret
      HOSTNAME: 0.0.0.0
      PORT: 3000
      # Advanced settings
      REMOVE_TRAILING_SLASH: 1
      DISABLE_BOT_CHECK: 0
      FORCE_SSL: 1
      DATA_TTL: 90
    ports:
      - "3000:3000"
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:3000/api/heartbeat"]
      interval: 30s
      timeout: 10s
      retries: 3

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: umami
      POSTGRES_USER: umami  
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "umami"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

### Multi-tenant Configuration
```javascript
// API configuration for multi-tenant setup
const config = {
    // Enable team features
    enableTeams: true,
    
    // User quotas
    maxWebsitesPerUser: 50,
    maxEventsPerMonth: 10000000,
    
    // Data retention by plan
    dataRetention: {
        free: 30,  // days
        pro: 90,
        enterprise: 365
    },
    
    // Rate limiting
    rateLimits: {
        api: {
            windowMs: 60000,  // 1 minute
            max: 100  // requests
        },
        collect: {
            windowMs: 1000,  // 1 second  
            max: 60  // events
        }
    }
};
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream umami_backend {
    server 127.0.0.1:3000;
}

server {
    listen 80;
    server_name umami.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name umami.example.com;

    ssl_certificate /etc/ssl/certs/umami.example.com.crt;
    ssl_certificate_key /etc/ssl/private/umami.example.com.key;

    location / {
        proxy_pass http://umami_backend;
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
    ServerName umami.example.com
    Redirect permanent / https://umami.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName umami.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/umami.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/umami.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:3000/
    ProxyPassReverse / http://127.0.0.1:3000/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend umami_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/umami.pem
    redirect scheme https if !{ ssl_fc }
    default_backend umami_backend

backend umami_backend
    balance roundrobin
    server umami1 127.0.0.1:3000 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R umami:umami /etc/umami
sudo chmod 750 /etc/umami

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

### Initial Schema Setup
```bash
# PostgreSQL
psql -U umami -d umami < /path/to/umami/sql/schema.postgresql.sql

# MySQL
mysql -u umami -p umami < /path/to/umami/sql/schema.mysql.sql

# Run migrations
npx prisma migrate deploy
```

### Performance Indexes
```sql
-- Additional indexes for better performance
-- PostgreSQL
CREATE INDEX CONCURRENTLY idx_pageview_created_at ON pageview(created_at);
CREATE INDEX CONCURRENTLY idx_pageview_website_id ON pageview(website_id);
CREATE INDEX CONCURRENTLY idx_pageview_session_id ON pageview(session_id);
CREATE INDEX CONCURRENTLY idx_event_created_at ON event(created_at);

-- Partitioning for large datasets (PostgreSQL)
CREATE TABLE pageview_2024_01 PARTITION OF pageview
    FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

### Data Cleanup Scripts
```bash
#!/bin/bash
# cleanup-umami.sh - Remove old analytics data

# Configuration
DAYS_TO_KEEP=90
DB_NAME="umami"
DB_USER="umami"

# Delete old pageviews
psql -U $DB_USER -d $DB_NAME -c "
    DELETE FROM pageview 
    WHERE created_at < NOW() - INTERVAL '$DAYS_TO_KEEP days';
"

# Delete orphaned sessions
psql -U $DB_USER -d $DB_NAME -c "
    DELETE FROM session 
    WHERE id NOT IN (SELECT DISTINCT session_id FROM pageview);
"

# Vacuum and analyze
psql -U $DB_USER -d $DB_NAME -c "VACUUM ANALYZE;"
```

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
sudo systemctl status umami

# View logs
sudo journalctl -u umami -f

# Monitor resource usage
top -p $(pgrep umami)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/umami"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/umami-backup-$DATE.tar.gz" /etc/umami /var/lib/umami

echo "Backup completed: $BACKUP_DIR/umami-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop umami

# Restore from backup
tar -xzf /backup/umami/umami-backup-*.tar.gz -C /

# Start service
sudo systemctl start umami
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u umami -n 100
sudo tail -f /var/log/umami/umami.log

# Check configuration
umami --version

# Check permissions
ls -la /etc/umami
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 3000

# Test connectivity
telnet localhost 3000

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep umami)

# Check disk I/O
iotop -p $(pgrep umami)

# Check connections
ss -an | grep 3000
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  umami:
    image: umami:latest
    ports:
      - "3000:3000"
    volumes:
      - ./config:/etc/umami
      - ./data:/var/lib/umami
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update umami

# Debian/Ubuntu
sudo apt update && sudo apt upgrade umami

# Arch Linux
sudo pacman -Syu umami

# Alpine Linux
apk update && apk upgrade umami

# openSUSE
sudo zypper update umami

# FreeBSD
pkg update && pkg upgrade umami

# Always backup before updates
tar -czf /backup/umami-pre-update-$(date +%Y%m%d).tar.gz /etc/umami

# Restart after updates
sudo systemctl restart umami
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/umami

# Clean old logs
find /var/log/umami -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/umami
```

## Additional Resources

- Official Documentation: https://docs.umami.org/
- GitHub Repository: https://github.com/umami/umami
- Community Forum: https://forum.umami.org/
- Best Practices Guide: https://docs.umami.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
