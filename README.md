# Web Lab 2 – DNS and Apache Authentication

## Overview

This lab consists of deploying a small web infrastructure using **Vagrant** and **VirtualBox**, composed of two Linux virtual machines:

- **dns.sistema.sol**: DNS server using BIND9  
- **tierra.sistema.sol**: Web server using Apache2  

The main goal of the practice is to configure:
- An authoritative DNS server for the domain `sistema.sol`
- An Apache VirtualHost for `discovery.sistema.sol`
- HTTP authentication using **Basic** and **Digest**
- Access control based on users and groups

All configuration is automated through the `Vagrantfile` and files provided in the `config` directory.

---

## Infrastructure Description

### DNS Server (`dns.sistema.sol`)
- IP address: `192.168.56.100`
- Authoritative zone for `sistema.sol`
- Reverse resolution zone for `192.168.56.0/24`
- Configuration and zone files are located in `config/dns`

### Web Server (`tierra.sistema.sol`)
- IP address: `192.168.56.101`
- Apache VirtualHost for `discovery.sistema.sol`
- DocumentRoot deployed from `config/tierra/discovery.sistema.sol`
- HTTP Basic authentication for protected directories
- HTTP Digest authentication for restricted content

---

## Authentication Configuration

### HTTP Basic Authentication
- User database: `/etc/apache2/.htpasswd_basic`
- Groups file: `/etc/apache2/.htgroups`
- Access rules:
  - `/basic`: any authenticated user
  - `/basic/desarrollo`: only users in group `desarrollo`
  - `/basic/ventas`: only users in group `ventas`

### HTTP Digest Authentication
- User database: `/etc/apache2/.htpasswd_digest`
- Access to `/digest` restricted to a single user
- Digest authentication is used to avoid sending passwords in clear text over the network

---

## Host System Considerations (macOS Apple Silicon)

The host system used for this practice is **macOS running on Apple Silicon (M2)**.

Due to compatibility limitations between **VirtualBox**, **Vagrant**, and Apple Silicon, the virtual machines are deployed using an **internal network with port forwarding**. In this scenario, the host system cannot directly use the DNS server running inside the virtual machines.

To allow proper name-based virtual hosting from the host browser, the file **`/etc/hosts`** on the host machine was manually modified to include the following entry: 
``` bash
127.0.0.1  discovery.sistema.sol
```
This ensures that requests made to `http://discovery.sistema.sol:8080` are correctly resolved and forwarded to the Apache VirtualHost running inside the VM.

A screenshot of this modification is included as evidence.

---

## Project Structure

The project follows the structure provided for the lab:
```bash
.
├── Vagrantfile
├── config
│   ├── dns
│   │   ├── db.192.168.56
│   │   ├── db.sistema.sol
│   │   ├── named
│   │   ├── named.conf.local
│   │   └── named.conf.options
│   └── tierra
│       ├── apache2.conf
│       ├── discovery.sistema.sol
│       │   ├── basic
│       │   │   ├── desarrollo
│       │   │   │   └── index.html
│       │   │   ├── index.html
│       │   │   └── ventas
│       │   │       └── index.html
│       │   ├── digest
│       │   │   └── index.html
│       │   └── index.html
│       ├── discovery.sistema.sol.conf
│       └── resolved.conf
├── docs
│   └── lab-web-2-en.pdf
├── evidences
│   ├── discovery.sistema.sol.conf
│   ├── discovery.sistema.sol.conf.png
│   ├── file :etc:hosts.png
│   ├── htgroups.png
│   ├── htpasswd_basic.png
│   ├── htpasswd_digest.png
│   └── hurl .png
└── weblab-2.hurl
```
---

## Evidence

All required configuration files and screenshots demonstrating the correct operation of the system are included in the **`evidences`** directory, as requested in the lab instructions.

## Conclusion

This practice demonstrates the deployment of a complete web service with DNS resolution, Apache virtual hosting, and authentication mechanisms using Infrastructure as Code principles. All requirements defined in the lab statement have been fulfilled, and the necessary host-side adaptations for Apple Silicon systems have been properly documented.