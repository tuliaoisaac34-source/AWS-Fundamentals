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

Created a custom index page in the Nginx web root directory.

Command used:

```bash
echo "Week 1 AWS VPC Portfolio - Nginx Test Server" | sudo tee /var/www/html/index.html
```

Tested the local web server using:

```bash
curl http://localhost
```

Expected result:

```text
Week 1 AWS VPC Portfolio - Nginx Test Server
```

This confirmed that Nginx successfully served the custom web page through HTTP.

![Successful Curl Test](https://github.com/user-attachments/assets/64dc9cb5-8a39-421e-a704-2ef0a52f83dd)

## 4. Simulating a Web Server Outage

Stopped the Nginx service to simulate a web server failure.

Commands used:

```bash
sudo service nginx stop
sudo service nginx status
```

Expected result: Nginx service status displays `inactive (dead)`.

![Nginx Stopped](https://github.com/user-attachments/assets/1ef55775-3d3a-457a-aee5-c07e99f142a1)

## 5. HTTP Connectivity Failure Verification

Attempted to access the local web server while Nginx was stopped.

Command used:

```bash
curl http://localhost
```

Expected result: Connection failure because no Nginx service was listening on the HTTP port.

This demonstrated how stopping a web server affects service availability.

![Nginx Connection Failure](https://github.com/user-attachments/assets/1c766365-433c-4963-9fd9-9474e81cfa21)

## 6. Nginx Service Recovery

Restarted Nginx to restore web server availability.

Commands used:

```bash
sudo service nginx start
sudo service nginx status
curl http://localhost
```

Expected result:
- Nginx returns to `active (running)`.
- The custom web page becomes accessible again.
- HTTP requests return the expected page content.

![Nginx Recovered](https://github.com/user-attachments/assets/e3ec39a2-7d83-4e3e-bca6-21c6b2ae578f)

## 7. Port 80 and HTTP Service Verification

Performed additional checks to verify that Nginx was listening on TCP port 80 and returning a successful HTTP response.

Commands used:

```bash
sudo ss -tulpn | grep :80
curl -I http://localhost
```

Expected results:
- Nginx is listening on TCP port 80.
- HTTP response returns `200 OK`.

![Nginx Port 80 Verification](https://github.com/user-attachments/assets/8a8e26f0-5e92-4fab-aee1-be4bfb3a1a9a)

## 8. Skills and Concepts Practiced

- Installing and configuring Nginx on Ubuntu Linux
- Managing Linux services using `sudo service`
- Understanding the Nginx web root directory
- Hosting and testing a custom HTML page
- Validating HTTP responses using `curl`
- Checking listening ports using `ss`
- Simulating web server downtime
- Troubleshooting service availability
- Recovering a stopped service
- Verifying HTTP service operation

## 9. Project Summary

Successfully configured and administered a local Nginx web server using Ubuntu WSL. Created a custom web page, validated HTTP responses using `curl`, simulated a service outage, and restored service availability through Linux service management.

**Environment limitation:** This lab was performed locally using Ubuntu WSL. It demonstrates Linux web server administration concepts but does not represent an actual Nginx deployment on an AWS EC2 instance.
