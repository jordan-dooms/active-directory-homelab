# active-directory-homelab
Hands-on Windows Active Directory home lab demonstrating domain administration, Group Policy, DNS, endpoint management, and security monitoring.

# Active Directory Home Lab
_________________________

# Overview

- This project documents the design, deployment, and administration of a Windows-based Active Directory home lab built using VMware Workstation Pro.

- The purpose of this lab is to develop and demonstrate hands-on experience with enterprise IT infrastructure, identity management, endpoint administration, networking, and cybersecurity.

- The environment is designed to simulate a small business network and provide a controlled environment for testing administrative and security concepts.

# Objectives
- Deploy and configure Windows Server 2022
- Implement Active Directory Domain Services (AD DS)
- Configure DNS and domain services
- Join Windows 11 endpoints to the domain
- Create and manage users, groups, and computer objects
- Implement Group Policy
- Configure network segmentation
- Centralize Windows security logging
- Monitor activity using Splunk
- Build a foundation for future vulnerability management and incident response exercises
- Lab Environment
- Virtualization
- VMware Workstation Pro
- Server
- Windows Server 2022
- Active Directory Domain Services
- DNS
- Splunk Enterprise
- Endpoints
- Windows 11
- Domain-joined client systems
- Security & Networking
- pfSense
- Kali Linux
- Network segmentation
- Windows Event Logging
- Splunk Universal Forwarder
- Network Architecture

# The lab uses separate networks to simulate a production environment and an attacker/security-testing environment.

[Lab Network]

- 192.168.50.0/24

[Attack Network]

- 192.168.60.0/24

[Domain]

- corp.lab

# Infrastructure

| System | Role | IP Address |
|---|---|---|
| **DC01** | Domain Controller / DNS | `192.168.50.40` |
| **WIN11-CMD** | Windows 11 Command Center | `192.168.50.10` |
| **WIN11-TEST** | Windows 11 Test Client | `192.168.50.30` |
| **Kali** | Security Testing System | `192.168.60.x` |

# Architecture Diagram

An architecture diagram will be added to illustrate the relationship between the firewall, networks, domain controller, Windows endpoints, and security-testing systems.

# Active Directory

The Windows Server 2022 system was configured as the domain controller for the corp.lab environment.

# The lab includes hands-on administration of:
- Users
- Security groups
- Computer objects
- Organizational Units
- Group Policy
- Domain authentication
- DNS
- Endpoint Administration

Windows 11 systems were joined to the corp.lab domain.

# Endpoint administration includes:
- Domain joining
- User authentication
- Group Policy application
- Windows security configuration
- Event logging
- Remote administration
- Security Monitoring

# Splunk Enterprise is used to collect and analyze Windows event logs from systems within the lab.

- Windows systems use Splunk Universal Forwarders to send logs to the Splunk server.

This provides a foundation for:
- Security event analysis
- Authentication monitoring
- Threat detection
- Incident investigation
- SIEM development
- Troubleshooting & Lessons Learned

This section will document issues encountered during the development of the lab, the troubleshooting process used to identify the cause, and the solution implemented.

Documenting failures and troubleshooting is an intentional part of this project because effective IT and cybersecurity work requires the ability to diagnose and resolve problems in addition to configuring systems successfully.

# Future Improvements
- 

# Planned improvements include:
- Expand Group Policy configuration
- Add additional security controls
- Expand Splunk dashboards and detection capabilities
- Integrate vulnerability scanning
- Perform controlled attack simulations
- Develop incident-response scenarios
- Integrate additional Microsoft security technologies
- Expand the environment into Azure
- Skills Demonstrated
- Active Directory
- Windows Server
- Windows 11
- DNS
- Group Policy
- Identity & Access Management
- Endpoint Administration
- Network Segmentation
- pfSense
- Splunk
- Windows Event Logging
- SIEM
- PowerShell
- Troubleshooting
- Cybersecurity Fundamentals

  
[Disclaimer]
This is a privately maintained home lab created for educational, professional development, and cybersecurity practice purposes.
