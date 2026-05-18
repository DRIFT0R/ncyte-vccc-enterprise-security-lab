# CyberShield Network Configuration Record

## Document Purpose

This document provides a public portfolio version of the CyberShield enterprise network configuration record. It summarizes the network architecture, host roles, IP addressing, firewall design, identity services, web application deployment, database configuration, AI integration, monitoring stack, email services, workstation setup, backup and recovery procedures, and service dependencies.

Sensitive credentials and private lab details have been removed.

## Executive Summary

CyberShield is a simulated B2B cybersecurity solutions provider. The infrastructure was designed to support realistic business operations, including e-commerce sales of cybersecurity products and services, centralized identity management, secure internal communications, monitoring and analytics, AI-assisted lead generation, and business continuity through backup and recovery procedures.

The infrastructure uses a defense-in-depth model with a segmented architecture:

- WAN zone for upstream connectivity
- DMZ/screened subnet for public-facing services
- Transit network between firewall and internal routing
- Internal LAN for identity services, workstations, monitoring, and administration

Traffic between zones is controlled through an OPNsense firewall and VyOS routing infrastructure.

## Network Architecture Overview

The CyberShield network follows a segmented architecture designed to enforce trust boundaries and reduce lateral movement.

### Trust Zones

| Zone | Network | Purpose |
|---|---|---|
| WAN | 172.31.0.0/24 | Simulated external network and upstream internet connectivity |
| DMZ | 10.0.1.0/24 | Public-facing web and database services |
| Transit | 192.168.1.0/24 | Dedicated firewall-to-LAN routing segment |
| Internal LAN | 192.168.2.0/24 | Active Directory, workstations, monitoring, and internal services |

### Traffic Flow Summary

Traffic between zones follows controlled paths:

- Internal clients → Router-LAN → Firewall → WAN
- Internal clients → Router-LAN → Firewall → DMZ
- DMZ systems → Firewall → WAN
- No direct LAN-to-DMZ or LAN-to-WAN bypass paths exist
- All inter-zone communication is inspected and controlled by firewall rules and routing policies

## Master Configuration Table

### Core Infrastructure

| Hostname | Role | Interface | IP Address | Zone | OS |
|---|---|---|---|---|---|
| Router-WAN | Edge Router | eth4 | DHCP | WAN | VyOS |
| Router-WAN | Edge Router | eth5 | 172.31.0.9/24 | WAN | VyOS |
| Firewall | OPNsense Firewall | vtnet0 | 172.31.0.15/24 | WAN | OPNsense |
| Firewall | OPNsense Firewall | vtnet1 | 10.0.1.1/24 | DMZ | OPNsense |
| Firewall | OPNsense Firewall | vtnet2 | 192.168.1.1/24 | Transit | OPNsense |
| Router-LAN | Internal Router | eth4 | 192.168.1.2/24 | Transit | VyOS |
| Router-LAN | Internal Router | eth5 | 192.168.2.1/24 | LAN | VyOS |

### DMZ Systems

| Hostname | Role | IP Address | Gateway | Zone | OS |
|---|---|---|---|---|---|
| Web-Server | Web Application Host | 10.0.1.100 | 10.0.1.1 | DMZ | Windows Server 2025 |
| DB-Server | Database Server | 10.0.1.200 | 10.0.1.1 | DMZ | Red Hat Enterprise Linux |

### Internal LAN Systems

| Hostname | Role | IP Address | Gateway | DNS | Zone | OS |
|---|---|---|---|---|---|---|
| AD-DNS | Domain Controller / DNS | 192.168.2.5 | 192.168.2.1 | Self | LAN | Windows Server 2025 |
| Win11-Client | Domain Workstation | 192.168.2.10 | 192.168.2.1 | 192.168.2.5 | LAN | Windows 11 |
| RHEL-Client | Linux Workstation | 192.168.2.20 | 192.168.2.1 | 192.168.2.5 | LAN | Red Hat Enterprise Linux |

## Firewall Configuration

The OPNsense firewall enforces segmentation between the WAN, DMZ, transit, and internal LAN networks.

### Firewall Interfaces

| Interface | IP Address | Role |
|---|---|---|
| WAN / vtnet0 | 172.31.0.15/24 | Uplink to WAN through Router-WAN |
| DMZ / vtnet1 | 10.0.1.1/24 | Gateway for screened subnet |
| LAN / vtnet2 | 192.168.1.1/24 | Transit link toward internal LAN |

### Static Routing

A static route allows the firewall to reach the internal LAN behind Router-LAN.

| Destination Network | Gateway | Description |
|---|---|---|
| 192.168.2.0/24 | 192.168.1.2 | Route to internal LAN through Router-LAN |

### NAT

Outbound NAT is configured so internal networks can communicate through the firewall WAN interface.

| Interface | Source Network | NAT Address | Description |
|---|---|---|---|
| WAN | 192.168.1.0/24 | Interface Address | Transit outbound NAT |
| WAN | 192.168.2.0/24 | Interface Address | Internal LAN outbound NAT |
| WAN | 10.0.1.0/24 | Interface Address | DMZ outbound NAT |

### Firewall Policy Summary

Key firewall controls included:

- WAN inbound blocked by default
- Specific inbound web traffic allowed to the DMZ web server
- WAN-to-internal-LAN traffic blocked
- DMZ systems blocked from initiating connections to the internal LAN
- Internal LAN permitted to access the web server
- Direct LAN access to the database server blocked
- DNS and required service traffic permitted where necessary

## Active Directory and DNS

The internal LAN includes a Windows Server 2025 system providing Active Directory Domain Services and DNS.

### Domain Information

| Item | Value |
|---|---|
| Domain | cybershield.local |
| Forest | cybershield.local |
| Domain Controller | AD-DNS |
| Domain Controller IP | 192.168.2.5 |
| Roles | AD DS, DNS, File and Storage Services |

### Organizational Units

The domain included organizational units for:

- Executive
- HR
- Security Operations
- Engineering
- Sales
- IT Support
- Guests
- Workstations
- Servers
- Groups

### Security Groups

Global security groups were created for:

- Admin
- HR users
- SOC analysts
- Engineering developers
- Sales users
- IT support staff

### Group Policy Objects

Group Policy was used to enforce baseline controls, including:

- Domain audit policy
- Workstation baseline policy
- Department user restrictions
- IT support policy
- Guest restrictions
- Password policy
- Account lockout policy

## Web Server Configuration

The CyberShield web application was hosted on a Windows Server 2025 system in the DMZ.

### Web Stack

| Layer | Technology |
|---|---|
| Reverse Proxy | Microsoft IIS |
| Proxy Modules | URL Rewrite and Application Request Routing |
| Backend Server | Waitress WSGI |
| Application Framework | Python Flask |
| Backend Listener | 127.0.0.1:8000 |
| Application Directory | C:\Cybershield |

IIS handled HTTP and HTTPS traffic and proxied requests to the Flask application. Binding the backend to localhost prevented direct external access to the application server.

### DNS

Internal DNS provided hostname resolution for the web application.

| DNS Record | Value |
|---|---|
| Hostname | web.cybershield.local |
| Record Type | A |
| IP Address | 10.0.1.100 |

### HTTP and HTTPS

The web server supported:

- HTTP on port 80
- HTTPS on port 443
- Self-signed TLS certificate for lab use
- HTTP-to-HTTPS redirect behavior

### Web Server Hardening

Hardening controls included:

- Strict-Transport-Security header
- X-Frame-Options header
- X-Content-Type-Options header
- IIS server header reduction
- X-Powered-By header removal
- Backend service bound to localhost only

## AI Service Integration

The CyberShield platform included an AI-powered chatbot integrated into the Flask backend.

The chatbot communicated with an OpenWebUI-compatible API using a GPU-backed model. API authentication was handled through environment variables and the API key was not exposed to client-side code.

### AI Workflow

The AI workflow performed the following steps:

1. Received user prompt through the web interface
2. Classified user intent
3. Generated a relevant product or service recommendation
4. Assigned lead priority
5. Determined routing destination
6. Generated a structured summary
7. Stored lead details in the MariaDB `ai_leads` table
8. Logged workflow execution and latency metrics

If the AI service was unavailable, the application could still fall back to normal catalog browsing.

## Database Configuration

The CyberShield application database was hosted on a RHEL server in the DMZ and used MariaDB.

### Database Architecture

| Component | Value |
|---|---|
| Database Server | RHEL DB-Server |
| IP Address | 10.0.1.200 |
| Database Engine | MariaDB |
| Application Server | Windows Web-Server |
| ORM | Flask-SQLAlchemy |
| Database Name | ordersdb |

### Core Tables

| Table | Purpose |
|---|---|
| `products` | Product and service catalog |
| `orders` | Customer order information |
| `order_items` | Items associated with each order |
| `ai_leads` | AI-generated lead records |

### Database Security

Database access was restricted at the network level:

- Web Server → Database Server: allowed on database service port
- Internal LAN → Database Server: blocked
- Users interact with the database through the web application only

### Backup and Recovery

Backup and recovery testing included:

- Manual database backup using `mysqldump`
- Automated daily backup using a shell script and cron
- Simulated database corruption
- Restoration from verified backup
- Post-restore validation queries

## Email Server Configuration

The CyberShield environment included an internal email service using hMailServer.

| Component | Value |
|---|---|
| Software | hMailServer |
| Host | AD-DNS |
| Mail Domain | cybershield.local |
| Mail Hostname | mail.cybershield.local |
| Zone | Internal LAN |

### Email Services

The environment supported:

- SMTP
- IMAP
- POP3
- Internal mail routing
- Thunderbird client access from Windows and RHEL workstations

The email server was configured for internal lab use only. A production deployment would require stronger TLS enforcement and certificate management.

## Monitoring and Analytics

The environment used Prometheus and Grafana for infrastructure monitoring.

### Monitoring Architecture

| Component | Role |
|---|---|
| Windows Exporter | Exposes Windows host metrics |
| Linux Node Exporter | Exposes Linux host metrics |
| Prometheus | Collects and stores metrics |
| Grafana | Visualizes metrics in dashboards |

### Monitoring Servers

| Component | Host | IP Address |
|---|---|---|
| Prometheus | Win11-Client | 192.168.2.10 |
| Grafana | RHEL-Client | 192.168.2.20 |

### Metrics Collected

Monitoring covered:

- CPU usage
- Memory usage
- Disk utilization
- Network statistics
- System uptime
- Host availability
- Windows and Linux infrastructure performance

## Workstation Configuration

### Windows Workstation

The Windows 11 workstation was configured with:

- Static IP addressing
- DNS pointing to the AD-DNS server
- Domain join to cybershield.local
- Domain login validation
- Thunderbird email access
- Web application access
- Productivity and browser tools

### RHEL Workstation

The RHEL workstation was configured with:

- Static IP addressing
- DNS pointing to the AD-DNS server
- Hostname configuration
- Required packages for domain integration
- Realm/SSSD domain join
- Kerberos authentication validation
- Thunderbird email access
- Web application access
- Grafana dashboard hosting

## Service Dependencies

| Service | Host | Dependencies |
|---|---|---|
| DNS | AD-DNS | Internal clients, web server, mail server |
| Active Directory | AD-DNS | Workstations, authentication, policies |
| Web Application | Web-Server | IIS, Flask, Waitress, MariaDB, DNS |
| Database | DB-Server | Web application |
| AI Chatbot | Web-Server | OpenWebUI-compatible API, MariaDB |
| Email | AD-DNS | DNS, Thunderbird clients |
| Monitoring | Prometheus/Grafana hosts | Exporters, network reachability |
| Firewall | OPNsense | All inter-zone traffic |
| Routing | VyOS routers | WAN and LAN connectivity |

## Security Concepts Demonstrated

- Segmented enterprise architecture
- Defense in depth
- DMZ design
- Firewall-controlled traffic flow
- NAT and static routing
- Active Directory identity management
- Group Policy enforcement
- Web server hardening
- Reverse proxy design
- Database access restriction
- Environment variable secret handling
- Internal email service deployment
- Infrastructure monitoring
- Backup and recovery validation
- Service dependency mapping
- Technical documentation

