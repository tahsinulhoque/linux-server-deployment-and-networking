# Linux Server Deployment and Networking

A practical project completed as part of **Codomax Internship -- Module
02**, covering Linux administration fundamentals, networking, AWS EC2
provisioning, SSH access, firewall configuration, and Nginx deployment.

> **Project result:** Deployed an Ubuntu Server 24.04 LTS instance on
> AWS EC2, configured SSH and UFW access, installed Nginx, and
> successfully accessed the Nginx welcome page through the instance's
> public IPv4 address.

## Table of Contents

-   [Project Overview](#project-overview)
-   [Why I Built This Project](#why-i-built-this-project)
-   [Objectives](#objectives)
-   [Technologies and Services](#technologies-and-services)
-   [Architecture](#architecture)
-   [AWS Infrastructure](#aws-infrastructure)
-   [Implementation](#implementation)
-   [Commands Reference](#commands-reference)
-   [Challenges and Troubleshooting](#challenges-and-troubleshooting)
-   [Verification and Results](#verification-and-results)
-   [Screenshots](#screenshots)
-   [Key Learnings](#key-learnings)
-   [Repository Structure](#repository-structure)
-   [Acknowledgement](#acknowledgement)

## Project Overview

This project demonstrates the process of preparing and deploying a Linux
web server in AWS. Linux fundamentals and networking commands were
practised in WSL, while server deployment was completed on an Ubuntu EC2
instance.

Work completed:

1.  Practised Linux command-line and system administration operations.
2.  Inspected IP addresses, network interfaces, routes, and DNS
    resolution.
3.  Provisioned an Ubuntu EC2 instance.
4.  Configured SSH access using a PEM key pair.
5.  Configured AWS Security Group inbound rules.
6.  Configured the Ubuntu UFW firewall.
7.  Installed and started Nginx.
8.  Verified the web server using command-line tools and a browser.

## Why I Built This Project

I built this project to move beyond learning Linux and networking
concepts theoretically and apply them in a practical server environment.

My goals were to:

-   Practise Linux command-line operations and understand common system
    administration tasks.
-   Understand how IP addressing, routing, DNS, ports, and firewalls
    affect server connectivity.
-   Provision and access a Linux server on AWS EC2.
-   Learn SSH key-based authentication and troubleshoot connection
    issues.
-   Understand the relationship between AWS Security Groups and the
    Linux UFW firewall.
-   Deploy Nginx and verify that a web server is accessible over the
    internet.
-   Improve practical troubleshooting skills by investigating real
    connectivity and configuration problems.

## Objectives

-   [x] Practise Linux file and directory operations.
-   [x] Practise permissions, users, groups, processes, and services.
-   [x] Inspect network interfaces, IP configuration, and routes.
-   [x] Practise DNS and connectivity checks.
-   [x] Provision an Ubuntu EC2 instance.
-   [x] Configure SSH key-based access.
-   [x] Configure AWS Security Group inbound rules.
-   [x] Configure UFW on Ubuntu.
-   [x] Install and enable Nginx.
-   [x] Verify the Nginx service and HTTP response.
-   [x] Access the Nginx welcome page through the EC2 public IPv4
    address.

## Technologies and Services

  Category                  Technology
  ------------------------- ---------------------------
  Cloud provider            Amazon Web Services (AWS)
  Compute                   Amazon EC2
  Operating system          Ubuntu Server 24.04 LTS
  Virtual networking        Amazon VPC
  Network segmentation      Public Subnet
  Network access control    AWS Security Group
  Remote access             OpenSSH
  Host firewall             UFW
  Web server                Nginx
  Local Linux environment   WSL
  Version control           Git and GitHub

## Architecture

The architecture diagram is maintained in the `architecture/` directory.

![Linux Server Deployment and Networking
Architecture](architecture/architecture.png)

### Traffic Flow

1.  A user sends an HTTP request to the EC2 instance's public IPv4
    address.
2.  The request reaches the AWS VPC through the Internet Gateway.
3.  The public subnet's route table routes internet-bound traffic
    through the Internet Gateway.
4.  The EC2 Security Group allows inbound HTTP traffic on TCP port `80`.
5.  The Ubuntu UFW firewall allows HTTP traffic on port `80`.
6.  Nginx receives the request and returns the default welcome page.

### Main Components

-   **AWS VPC:** Provides the isolated virtual network.
-   **Public Subnet:** Hosts the EC2 instance.
-   **Internet Gateway:** Enables communication between the VPC and the
    internet.
-   **Route Table:** Routes internet-bound traffic through the Internet
    Gateway.
-   **Security Group:** Controls traffic reaching the EC2 instance.
-   **EC2:** Runs the Ubuntu Server operating system.
-   **UFW:** Provides host-level firewall rules.
-   **Nginx:** Serves the default web page over HTTP.

## AWS Infrastructure

  Resource            Configuration
  ------------------- ----------------------------
  EC2 instance name   `codomax-linux-web-server`
  Instance type       `t3.micro`
  Operating system    Ubuntu Server 24.04 LTS
  VPC                 `codomax-module-02-vpc`
  Subnet              `codomax-public-subnet`
  Security Group      `codomax-web-server-sg`
  SSH                 TCP port `22`
  HTTP                TCP port `80`
  Web server          Nginx

### Security Group Inbound Rules

  ------------------------------------------------------------------------
  Type             Protocol                          Port Source
  ---------------- ---------------- --------------------- ----------------
  SSH              TCP                                 22 Administrator
                                                          public IP
                                                          address(es),
                                                          `/32` per
                                                          address

  HTTP             TCP                                 80 `0.0.0.0/0`
  ------------------------------------------------------------------------

During troubleshooting, the outbound public IP observed from WSL changed
between `103.189.159.244` and `103.189.159.246`. Both addresses were
added as individual `/32` SSH sources when needed. SSH should be
restricted to trusted administrator IP addresses. Avoid leaving SSH open
to `0.0.0.0/0` in a production environment.

The EC2 public IPv4 address is not hard-coded here because it can change
after an instance is stopped and started unless an Elastic IP is
assigned.

## Implementation

### 1. Linux Environment

WSL was used to practise Linux command-line operations and networking
tools.

### 2. EC2 Instance Provisioning

An Ubuntu EC2 instance was launched with a key pair and attached to the
project VPC and public subnet. Auto-assign public IPv4 was enabled.

### 3. SSH Access

The downloaded PEM key was copied into the WSL user's SSH directory. Its
permissions were restricted before connecting.

``` bash
chmod 400 ~/.ssh/codomax-linux-web-server.pem
ssh -i ~/.ssh/codomax-linux-web-server.pem ubuntu@<EC2_PUBLIC_IP>
```

Replace `<EC2_PUBLIC_IP>` with the current public IPv4 address shown in
the EC2 console.

### 4. Server Update

``` bash
sudo apt update
sudo apt upgrade -y
```

### 5. Nginx Installation and Service Configuration

``` bash
sudo apt install nginx -y
sudo systemctl enable --now nginx
sudo systemctl status nginx
```

### 6. UFW Firewall

UFW was enabled and configured to allow SSH and HTTP traffic.

``` bash
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw enable
sudo ufw status
```

The verified UFW output showed `Status: active`, with OpenSSH and
`80/tcp` allowed. AWS Security Groups and UFW are separate firewall
layers. Both must allow the required traffic.

### 7. Web Server Verification

From the EC2 instance:

``` bash
curl -I http://localhost
```

From a browser:

``` text
http://<EC2_PUBLIC_IP>
```

The expected result is the **Welcome to nginx!** page.

## Commands Reference

The following commands cover the Linux, networking, SSH, and server
administration work practised during this project. Replace placeholders
such as `<PUBLIC_IP>` with the relevant value from your environment.

### A. Navigation and File Management

``` bash
pwd
ls
ls -lah
cd <directory>
mkdir <directory>
touch <filename>
cp <source> <destination>
mv <source> <destination>
rm <filename>
```

  Command     Purpose
  ----------- --------------------------------------------------------
  `pwd`       Display the current working directory
  `ls`        List files and directories
  `ls -lah`   List all entries with details and human-readable sizes
  `cd`        Change the current directory
  `mkdir`     Create a directory
  `touch`     Create an empty file or update its timestamp
  `cp`        Copy files or directories
  `mv`        Move or rename files
  `rm`        Remove files

### B. Permissions, Users, and Groups

``` bash
ls -l
chmod 400 <key-file>
chmod <mode> <file>
chown <user>:<group> <file>
whoami
id
groups
```

  Command    Purpose
  ---------- ----------------------------------------
  `ls -l`    Display file permissions and ownership
  `chmod`    Change file or directory permissions
  `chown`    Change file ownership
  `whoami`   Display the current username
  `id`       Display user and group IDs
  `groups`   Display group memberships

### C. Process and Service Management

``` bash
ps
ps aux
top
kill <PID>
systemctl status <service>
sudo systemctl restart <service>
sudo systemctl enable <service>
```

  Command               Purpose
  --------------------- ------------------------------------------------------
  `ps`                  Display process information
  `ps aux`              Display detailed information about running processes
  `top`                 Monitor processes and system resource usage
  `kill`                Send a signal to a process
  `systemctl status`    Check a systemd service's status
  `systemctl restart`   Restart a service
  `systemctl enable`    Configure a service to start at boot

### D. System and Network Information

``` bash
hostnamectl
ip addr show
ip route
```

  Command          Purpose
  ---------------- ---------------------------------------------
  `hostnamectl`    Display or manage hostname information
  `ip addr show`   Display network interfaces and IP addresses
  `ip route`       Display the IP routing table

### E. DNS and Connectivity Troubleshooting

``` bash
ping <hostname-or-IP>
nslookup <domain>
dig <domain>
curl -I http://localhost
curl -4 https://checkip.amazonaws.com
curl ifconfig.me
nc -vz -w 5 <PUBLIC_IP> 22
nc -vz -w 5 <PUBLIC_IP> 80
```

  -----------------------------------------------------------------------------
  Command                                   Purpose
  ----------------------------------------- -----------------------------------
  `ping`                                    Test basic network reachability

  `nslookup`                                Query DNS records

  `dig`                                     Inspect DNS responses

  `curl -I`                                 Request HTTP response headers

  `curl -4 https://checkip.amazonaws.com`   Display the current public IPv4
                                            address

  `curl ifconfig.me`                        Display the public IP address

  `nc -vz -w 5`                             Test TCP port reachability with a
                                            timeout
  -----------------------------------------------------------------------------

A ping request may be blocked even when a service is reachable. TCP port
checks are more useful when troubleshooting SSH and HTTP access.

### F. SSH and PEM Key Management

``` bash
ls -l <key-file>
ls -lah ~/.ssh/
chmod 400 ~/.ssh/codomax-linux-web-server.pem
ssh -i ~/.ssh/codomax-linux-web-server.pem ubuntu@<PUBLIC_IP>
ssh -vvv -o ConnectTimeout=10 -i ~/.ssh/codomax-linux-web-server.pem ubuntu@<PUBLIC_IP>
```

  Command               Purpose
  --------------------- --------------------------------------
  `ls -l`               Verify that the key file exists
  `ls -lah ~/.ssh/`     Inspect files in the SSH directory
  `chmod 400`           Restrict access to the private key
  `ssh -i`              Connect using a specific private key
  `ssh -vvv`            Enable verbose SSH troubleshooting
  `ConnectTimeout=10`   Set a 10-second connection timeout

**Security:** Never upload a PEM private key, password, AWS access key,
or other secret to GitHub.

### G. Package Management

Run these commands on the Ubuntu EC2 instance:

``` bash
sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
```

  Command                  Purpose
  ------------------------ -----------------------------------
  `apt update`             Refresh package index information
  `apt upgrade -y`         Upgrade installed packages
  `apt install nginx -y`   Install Nginx

### H. UFW Firewall

``` bash
sudo ufw status
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw enable
```

  Command               Purpose
  --------------------- ------------------------------
  `ufw status`          Display UFW status and rules
  `ufw allow OpenSSH`   Allow SSH traffic
  `ufw allow 80/tcp`    Allow HTTP traffic
  `ufw enable`          Enable the UFW firewall

Before enabling UFW on a remote server, ensure SSH is allowed to avoid
locking yourself out.

### I. Nginx Service Management and Verification

``` bash
sudo systemctl enable --now nginx
sudo systemctl status nginx
curl -I http://localhost
```

  -----------------------------------------------------------------------
  Command                             Purpose
  ----------------------------------- -----------------------------------
  `systemctl enable --now nginx`      Enable Nginx at boot and start it
                                      immediately

  `systemctl status nginx`            Verify that Nginx is running

  `curl -I http://localhost`          Verify the local HTTP response
  -----------------------------------------------------------------------

## Challenges and Troubleshooting

### 1. SSH Connection Timeout and Changing Public IP

**Problem:** SSH connections initially timed out even though the EC2
instance was running.

**Investigation and resolution:**

-   Checked the current outbound public IP from WSL using
    `curl -4 https://checkip.amazonaws.com` and `curl ifconfig.me`.
-   Observed two different public IP addresses: `103.189.159.244` and
    `103.189.159.246`.
-   The changing outbound IP meant the existing Security Group SSH
    source `/32` rule did not always match the current address.
-   Added the observed administrator IP addresses as individual `/32`
    SSH sources when required.
-   Tested TCP port `22` with `nc`.
-   Once the current IP was allowed and TCP port `22` became reachable,
    SSH connected successfully.

**Learning:** An ISP or network connection may use more than one
outbound public IP. A Security Group rule restricted to one `/32`
address can stop matching when the outbound IP changes. Check the
current public IP and update the rule when necessary. Keep SSH
restricted to trusted addresses.

### 2. PEM Key Permission Error in WSL

**Problem:** OpenSSH rejected the PEM file because its permissions were
too open when the key was stored under a Windows-mounted path such as
`/mnt/e/Downloads/`.

**Resolution:** The PEM file was copied into the WSL user's SSH
directory and its permissions were restricted:

``` bash
chmod 400 ~/.ssh/codomax-linux-web-server.pem
```

SSH access then succeeded.

### 3. Difference Between SSH Timeout and HTTP Connection Refused

During the initial connectivity checks, the two ports returned different
results:

-   **Port 22 (SSH): `Connection timed out`** --- the connection attempt
    received no response. The Security Group source restriction was
    investigated because the SSH inbound rule did not match the active
    public IP at that point.
-   **Port 80 (HTTP): `Connection refused`** --- the TCP connection was
    rejected at that time, before Nginx was installed and serving HTTP.

After the Security Group rules were corrected, SSH became reachable.
After Nginx was installed and started, the local HTTP check returned
`HTTP/1.1 200 OK` and the Nginx welcome page became accessible through
the public IP.

**Learning:** A timeout and a connection refusal are different symptoms.
A timeout commonly indicates dropped or unanswered traffic, while a
refusal indicates that the connection was actively rejected, often
because no service was listening on that port or a host-level rule
rejected it. The surrounding network and service state must be checked
before deciding the cause.

### 4. HTTP and HTTPS

The Nginx server in this project was verified over **HTTP**. The default
Nginx page does not mean HTTPS has been configured. HTTPS requires a TLS
certificate and corresponding server configuration.

## Verification and Results

  Check                Verified result
  -------------------- --------------------------------------
  EC2 instance state   Running
  EC2 status checks    3/3 checks passed
  SSH access           Successful key-based login
  Ubuntu version       Ubuntu Server 24.04.5 LTS
  UFW                  Active; OpenSSH and `80/tcp` allowed
  Nginx service        Active (running)
  Local HTTP check     `HTTP/1.1 200 OK`
  Browser test         Nginx Welcome page displayed

**Final result:** The Ubuntu EC2 instance successfully served the
default Nginx welcome page through its public IPv4 address.

## Screenshots

### AWS Infrastructure

![Security Group inbound rules](screenshots/aws-infrastructure/sg.png)

### Other Project Evidence

Add the relevant screenshots from the repository in the categories below
as they are available:

-   Linux fundamentals and command-line practice: `linux-practice/`
-   Networking and connectivity checks: `screenshots/`
-   AWS infrastructure and security configuration: `screenshots/`
-   SSH and Nginx deployment: `screenshots/`

## Key Learnings

-   Practised Linux command-line operations and basic administration.
-   Improved understanding of users, groups, permissions, processes, and
    services.
-   Learned how to inspect network interfaces, IP addresses, and routes.
-   Practised DNS lookup and TCP connectivity troubleshooting.
-   Provisioned and accessed an Ubuntu server on AWS EC2.
-   Used SSH key-based authentication and corrected private key
    permissions in WSL.
-   Learned how changing public IP addresses can affect IP-restricted
    Security Group rules.
-   Understood the difference between a TCP connection timeout and
    connection refusal.
-   Understood how AWS Security Groups and UFW work as separate firewall
    layers.
-   Installed, enabled, and verified the Nginx web server.

## Repository Structure

``` text
linux-server-deployment-and-networking/
│
├── README.md
├── architecture/
│   └── architecture.png
├── linux-practice/
├── screenshots/
│   └── aws-infrastructure/
│       └── sg.png
```

## Acknowledgement

Completed as part of the **Codomax Internship -- Module 02: Linux
Fundamentals, Networking Fundamentals, and Linux Server Deployment**.

------------------------------------------------------------------------

*This repository documents a learning project. Review and tighten
network access rules before using a similar configuration for a
production workload.*
