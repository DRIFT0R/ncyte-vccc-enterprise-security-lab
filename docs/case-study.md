# CyberShield Enterprise Infrastructure & Security Platform — Case Study

## Project Summary

CyberShield is a simulated B2B cybersecurity services company built as part of NCyTE's Virtual Cybersecurity Career Challenge (VCCC). The project involved designing, deploying, documenting, and validating an enterprise-style infrastructure environment that supported realistic business operations.

The environment included segmented networking, firewall-controlled traffic, Active Directory and DNS, DMZ-hosted web services, database-backed e-commerce functionality, AI-assisted lead generation, internal email, monitoring, backup and recovery planning, project management, and security assessment work.

The goal was to create a functional enterprise environment that demonstrated how infrastructure, security, application services, monitoring, and business workflows connect in a real organization.

## Business Scenario

CyberShield was designed as a cybersecurity solutions provider offering secure network hardware, endpoint protection platforms, hardened workstations, and managed security services.

The infrastructure supported:

- Public-facing product and service information
- E-commerce-style customer order flow
- Internal identity and authentication services
- Internal workstation access
- Internal email communication
- Database-backed business records
- AI-assisted customer lead generation
- Monitoring and performance analytics
- Backup and disaster recovery testing
- Security assessment and vulnerability planning

## My Role

My work focused on the parts of the project that connected infrastructure, application functionality, AI automation, monitoring, project management, and documentation.

My primary contributions included:

- Defined the CyberShield business concept, including cybersecurity products and managed service offerings
- Created and maintained project planning materials, including the Gantt chart and documentation structure
- Helped design the enterprise network diagram and segmented architecture
- Completed a baseline NIST Cybersecurity Framework assessment using CISA's Cyber Security Evaluation Tool
- Built and deployed the CyberShield web application
- Implemented e-commerce functionality, including product and service listings, shopping cart behavior, and checkout flow
- Designed the relational database schema for products, orders, order items, and AI-generated leads
- Integrated an AI chatbot into the Flask application using a GPU-backed OpenWebUI-compatible API
- Designed an agentic workflow to classify user intent, generate recommendations, assign priority, route leads, and store structured results
- Implemented infrastructure monitoring and analytics using Prometheus and Grafana
- Created dashboard views for system performance and service visibility
- Helped document firewall rules, IP addressing, routing, NAT, service ports, and infrastructure dependencies
- Contributed to vulnerability assessment and remediation planning

## Architecture Overview

The CyberShield environment used a defense-in-depth design with multiple trust zones.

| Zone | Network | Purpose |
|---|---|---|
| WAN | 172.31.0.0/24 | Simulated external network and upstream access |
| DMZ | 10.0.1.0/24 | Public-facing web and database services |
| Transit | 192.168.1.0/24 | Firewall-to-LAN routing segment |
| Internal LAN | 192.168.2.0/24 | Active Directory, workstations, monitoring, and internal services |

Traffic between zones was controlled through an OPNsense firewall and VyOS routing infrastructure. Public-facing services were placed in the DMZ, while identity services, workstations, monitoring, and administrative systems were placed inside the trusted LAN.

## Major Systems

| System | Role |
|---|---|
| OPNsense Firewall | Enforced segmentation, firewall rules, and NAT |
| VyOS WAN Router | Provided upstream routing |
| VyOS LAN Router | Routed internal LAN traffic through the firewall |
| Windows Server 2025 AD/DNS | Provided Active Directory and internal DNS |
| Windows Server 2025 Web Server | Hosted the CyberShield web application behind IIS |
| RHEL Database Server | Hosted MariaDB backend database |
| Windows 11 Client | Domain-joined internal workstation |
| RHEL Client | Domain-integrated Linux workstation and Grafana host |
| Prometheus | Collected infrastructure metrics |
| Grafana | Visualized monitoring data |
| hMailServer | Provided internal lab email service |

## Web Application

The CyberShield web application was deployed on a Windows Server system in the DMZ. IIS handled inbound HTTP and HTTPS requests and reverse-proxied traffic to a Flask application running through Waitress on localhost.

The web application supported:

- Cybersecurity product listings
- Managed service listings
- Shopping cart behavior
- Checkout flow
- Customer order creation
- Contact forms
- AI chatbot interaction
- AI-generated lead storage

The application connected to a MariaDB database hosted on a RHEL server in the DMZ. Users interacted with database-backed functionality through the web application, while direct access to the database server was restricted by firewall policy.

## AI Chatbot Workflow

The CyberShield platform included an AI-powered chatbot integrated into the Flask backend. The chatbot communicated with an OpenWebUI-compatible API using a GPU-backed language model.

The workflow performed the following actions:

1. Accepted user prompts through the web interface
2. Classified user intent
3. Generated cybersecurity product or service recommendations
4. Assigned a lead priority level
5. Routed the lead to the appropriate business function
6. Generated a structured summary
7. Stored the result in the MariaDB `ai_leads` table
8. Logged workflow execution details and latency metrics

API authentication was handled using environment variables so the key was not exposed to client-side code.

## Database Design

The application used MariaDB with SQLAlchemy models defined in the Flask backend.

Core tables included:

| Table | Purpose |
|---|---|
| `products` | Stores cybersecurity products and services |
| `orders` | Stores customer and order information |
| `order_items` | Stores line items associated with orders |
| `ai_leads` | Stores structured chatbot-generated lead data |

The database supported product retrieval, order processing, AI lead generation, SQL query testing, backup testing, and disaster recovery validation.

## Monitoring and Analytics

The monitoring stack used Prometheus for metrics collection and Grafana for dashboard visualization.

Monitoring covered:

- Windows server metrics
- Linux server metrics
- CPU usage
- Memory usage
- Disk usage
- Network statistics
- System uptime
- Service visibility

Prometheus collected metrics from Windows Exporter and Linux Node Exporter endpoints. Grafana dashboards provided visibility into infrastructure performance and health.

## Security Controls Demonstrated

This project demonstrated several enterprise security concepts:

- Network segmentation
- DMZ isolation
- Defense in depth
- Firewall-controlled traffic flow
- Least-privilege access
- Database access restriction
- Active Directory authentication
- Group Policy controls
- Web server hardening
- Security headers
- Environment variable secret handling
- Backup and recovery testing
- Infrastructure monitoring
- Technical documentation
- Vulnerability assessment planning

## Project Management

The project required coordination across several technical workstreams, including business concept development, network architecture, web application development, database management, AI integration, monitoring, documentation, and vulnerability assessment.

Project planning materials included a Gantt chart, task breakdown, and documentation structure. These helped organize deadlines, dependencies, team responsibilities, and final deliverables.

## Challenges

Key challenges included:

- Coordinating multiple infrastructure components across Windows, Linux, firewall, routing, database, and web layers
- Ensuring DNS, routing, NAT, and firewall policies supported the intended traffic flow
- Deploying the Flask web application behind IIS with reverse proxy behavior
- Integrating AI functionality securely through the backend
- Validating database persistence, backup, and recovery
- Organizing large amounts of technical documentation into a readable record

## Outcome

The final environment demonstrated a functional enterprise-style cybersecurity infrastructure with segmented networking, identity services, DMZ web hosting, database-backed application functionality, AI-assisted lead routing, internal email, monitoring, backup and recovery validation, and project documentation.

The project strengthened my understanding of how real infrastructure depends on coordination between networking, security, identity, applications, databases, monitoring, and documentation.

## Lessons Learned

- Segmented networks require careful planning of routing, NAT, DNS, and firewall policies
- DMZ services should be isolated from internal systems whenever possible
- Web applications need secure deployment architecture, not just functional code
- AI features should be proxied through the backend rather than exposing API keys to the client
- Monitoring is essential for troubleshooting and operational visibility
- Clear documentation makes complex systems easier to maintain and explain
- Project management matters because infrastructure projects involve many interdependent tasks