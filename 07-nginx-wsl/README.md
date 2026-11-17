# Linux Web Server Testing – Nginx on Ubuntu WSL

## 1. Project Overview

This lab demonstrates basic Linux web server administration using Nginx on Ubuntu through Windows Subsystem for Linux (WSL).

The objective was to install and manage the Nginx service, host a custom HTML page, validate HTTP connectivity, simulate a service outage, and restore normal operation.

**Tools and Technologies:**
- Ubuntu on WSL
- Nginx Web Server
- Linux Service Management
- `curl`
- HTTP (Port 80)

## 2. Nginx Service Installation and Startup

Installed Nginx and started the service using Linux service management commands.

Commands used:

```bash
sudo apt update
sudo apt install nginx -y
sudo service nginx start
sudo service nginx status
```

Expected result: Nginx service status displays `active (running)`.

![Nginx Running](https://github.com/user-attachments/assets/35f763fe-8beb-465f-8c17-5da01d784189)

## 3. Custom Web Page and HTTP Response Testing

Created a custom
