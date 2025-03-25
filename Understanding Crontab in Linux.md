

### What is Crontab?

`crontab` (short for cron table) is a configuration file used by the `cron` daemon to schedule and automate tasks in Linux. It allows users to execute scripts, commands, and programs at specific intervals without manual intervention.

### Crontab Syntax

Each cron job follows this format:

```
* * * * * command_to_execute
| | | | |
| | | | +---- Day of the week (0-6, Sunday=0)
| | | +------ Month (1-12)
| | +-------- Day of the month (1-31)
| +---------- Hour (0-23)
+------------ Minute (0-59)
```

### Examples of Crontab Entries

- Run a script every day at midnight:
    
    ```
    0 0 * * * /path/to/script.sh
    ```
    
- Run a script every hour:
    
    ```
    0 * * * * /path/to/script.sh
    ```
    
- Run a script every 5 minutes:
    
    ```
    */5 * * * * /path/to/script.sh
    ```
    
- Run a backup script every Sunday at 3 AM:
    
    ```
    0 3 * * 0 /path/to/backup.sh
    ```
    

### Special Crontab Keywords

Instead of numeric values, you can use:

- `@reboot` → Runs the script once at system startup
- `@daily` → Runs once per day (same as `0 0 * * *`)
- `@hourly` → Runs once per hour (same as `0 * * * *`)
- `@weekly` → Runs once per week (same as `0 0 * * 0`)
- `@monthly` → Runs once per month (same as `0 0 1 * *`)

### Managing Crontab

- **Edit crontab:**
    
    ```bash
    crontab -e
    ```
    
- **List scheduled jobs:**
    
    ```bash
    crontab -l
    ```
    
- **Remove all cron jobs:**
    
    ```bash
    crontab -r
    ```
    

### Using Crontab for Automated Tasks

Crontab is commonly used for:

- Running system maintenance scripts
- Scheduling backups
- Automating log rotations
- Restarting services

By using crontab efficiently, you can automate repetitive tasks and improve system management.

Example:

# Automated Network Configuration and HAProxy Setup

## Overview

This script configures a static IP, sets up network routing, and configures HAProxy for load balancing using the Round Robin method.

## Network Configuration

```bash
#!/bin/bash

# Automated Network Configuration Script
# This script sets up a static IP, configures routing, and updates DNS settings.

INTERFACE="eth0"
STATIC_IP="192.168.1.100/24"
GATEWAY="192.168.1.1"
DNS="8.8.8.8 8.8.4.4"

# Set Static IP Address
ip addr flush dev $INTERFACE
ip addr add $STATIC_IP dev $INTERFACE
ip link set $INTERFACE up

echo "Static IP set to $STATIC_IP"

# Configure Default Gateway
ip route add default via $GATEWAY
echo "Default gateway set to $GATEWAY"

# Configure DNS
echo "nameserver $DNS" > /etc/resolv.conf
echo "DNS set to $DNS"

# Enable IP Forwarding (Optional for routing purposes)
echo 1 > /proc/sys/net/ipv4/ip_forward
echo "IP forwarding enabled"

# Restart Network Service
systemctl restart networking
echo "Network configuration complete."
```

## HAProxy Configuration

```bash
# HAProxy Configuration Setup
cat <<EOF > /etc/haproxy/haproxy.cfg
global
    log /dev/log local0
    log /dev/log local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log global
    option httplog
    timeout connect 5s
    timeout client 50s
    timeout server 50s

frontend http_front
    bind *:80
    default_backend servers

backend servers
    balance roundrobin
    server server1 192.168.1.101:80 check
    server server2 192.168.1.102:80 check
EOF

# Restart HAProxy Service
systemctl restart haproxy
echo "HAProxy load balancer configured and restarted."
```