# pfSense Firewall & Network Configuration

## Overview

This document details the deployment and configuration of pfSense as the primary firewall and router for the Active Directory Homelab.

The pfSense firewall provides:

* Internet connectivity for the lab
* Network segmentation
* DHCP services for the internal lab network
* Routing between network interfaces
* Firewall enforcement between trusted and security-testing networks
* A controlled environment for future security testing and monitoring

The lab uses two isolated internal networks:

* **LAB Network:** `192.168.50.0/24`
* **ATTACK Network:** `192.168.60.0/24`

---

## Network Architecture

```text
                         INTERNET
                            │
                            ▼
                       VMware NAT
                            │
                            ▼
                       ┌─────────┐
                       │ pfSense │
                       └────┬────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
              ▼                           ▼
       LAB NETWORK                  ATTACK NETWORK
      192.168.50.0/24               192.168.60.0/24
              │                           │
       ┌──────┼──────┐                    │
       │      │      │                    │
       ▼      ▼      ▼                    ▼
     DC01  WIN11   WIN11                 Kali
     .50.40 .50.10 .50.30
```

---

## VMware Network Configuration

Two isolated VMware virtual networks were used for the internal lab.

| VMware Network | Subnet            | Purpose | DHCP     |
| -------------- | ----------------- | ------- | -------- |
| VMnet1         | `192.168.50.0/24` | LAB     | Disabled |
| VMnet2         | `192.168.60.0/24` | ATTACK  | Disabled |

VMware's built-in DHCP service was disabled because DHCP is controlled by pfSense on the LAB network.

The physical host's VMware VMnet1 adapter was assigned:

```text
IP Address:      192.168.50.2
Subnet Mask:     255.255.255.0
Default Gateway: None
```

This allows the physical host to communicate with the pfSense LAN interface at `192.168.50.1`.

---

## pfSense VM Configuration

The pfSense virtual machine was configured with three network adapters.

| Adapter   | VMware Connection | pfSense Interface | Purpose                  |
| --------- | ----------------- | ----------------- | ------------------------ |
| Adapter 1 | NAT               | WAN (`em0`)       | Internet connectivity    |
| Adapter 2 | VMnet1            | LAN (`em1`)       | Internal lab network     |
| Adapter 3 | VMnet2            | ATTACK (`em2`)    | Security testing network |

### Virtual Machine Resources

* **CPU:** 2 cores
* **RAM:** 4 GB
* **Storage:** 20 GB
* **Operating System:** pfSense CE

---

## Interface Configuration

### WAN

```text
Interface: em0
Connection: VMware NAT
IPv4: DHCP
```

The WAN interface receives its address from VMware's NAT network.

Internet connectivity was verified directly from the pfSense shell using:

```bash
ping -c 4 8.8.8.8
```

Successful replies confirmed that pfSense could reach the Internet.

---

### LAN

```text
Interface: em1
Description: LAN
IPv4: 192.168.50.1/24
```

The LAN interface serves as the default gateway for the internal LAB network.

The physical host accesses the pfSense WebConfigurator through:

```text
https://192.168.50.1
```

---

### ATTACK

```text
Interface: em2
Description: ATTACK
IPv4: 192.168.60.1/24
```

The ATTACK interface provides an isolated network for security testing systems such as Kali Linux.

IPv4 DHCP was intentionally disabled on this interface so security-testing systems can be assigned addresses explicitly.

---

## DHCP Configuration

DHCP was enabled on the LAB interface.

```text
DHCP Range:
192.168.50.100 - 192.168.50.200
```

The lower portion of the subnet is reserved for infrastructure using static IP addresses.

| IP Address           | System       |
| -------------------- | ------------ |
| `192.168.50.1`       | pfSense      |
| `192.168.50.10`      | WIN11-CMD    |
| `192.168.50.30`      | WIN11-TEST   |
| `192.168.50.40`      | DC01         |
| `192.168.50.100-200` | DHCP clients |

---

## Firewall Configuration

The ATTACK interface initially had no firewall rules.

A rule was created to permit outbound traffic from the ATTACK network.

### ATTACK Outbound Rule

```text
Action:       Pass
Address Family: IPv4
Protocol:     Any
Source:       ATTACK net
Destination:  Any
Description:  Allow ATTACK network outbound
```

This provides the Kali/security-testing network with controlled outbound connectivity while maintaining the ability to introduce more restrictive rules as the lab develops.

Future firewall rules will be added as needed to control communication between the LAB and ATTACK networks.

---

## WebConfigurator

The pfSense WebConfigurator was accessed through the LAN interface using HTTPS:

```text
https://192.168.50.1
```

HTTPS was retained rather than reverting the WebConfigurator to HTTP.

The default/local pfSense administrative account was used for initial access.

---

## Troubleshooting

### VMware Host Adapter IP Conflict

#### Problem

The physical Windows host's VMware VMnet1 adapter was initially configured with:

```text
192.168.50.1/24
```

pfSense was also configured with:

```text
192.168.50.1/24
```

This created an IP address conflict and prevented reliable communication with the pfSense WebConfigurator.

#### Resolution

The physical host's VMnet1 adapter was changed to:

```text
192.168.50.2/24
```

pfSense retained:

```text
192.168.50.1/24
```

The resulting configuration was:

```text
Physical Host
    │
    │ 192.168.50.2
    ▼
VMnet1 / 192.168.50.0/24
    │
    ▼
pfSense LAN
192.168.50.1
```

After correcting the conflict, the pfSense WebConfigurator became accessible from the physical host.

---

## Validation

The following checks were completed after deployment:

* [x] pfSense successfully installed
* [x] WAN interface assigned to `em0`
* [x] LAN interface assigned to `em1`
* [x] ATTACK interface assigned to `em2`
* [x] LAN configured as `192.168.50.1/24`
* [x] ATTACK configured as `192.168.60.1/24`
* [x] LAB DHCP range configured
* [x] ATTACK outbound firewall rule configured
* [x] pfSense Internet connectivity verified
* [x] WebConfigurator accessible over HTTPS
* [x] Host-to-pfSense connectivity verified

---

## Lessons Learned

This deployment reinforced several practical networking concepts:

1. **Virtual network segments must have clearly defined responsibilities.**
2. **DHCP should have a single authoritative source on each network.**
3. **Infrastructure systems benefit from predictable static IP addresses.**
4. **Firewall interfaces require explicit rules before traffic should be permitted.**
5. **IP address conflicts can prevent management access even when the underlying VM is functioning correctly.**
6. **Troubleshooting should begin by validating the network path and interface configuration before changing application settings.**

The pfSense deployment establishes the network foundation for the remainder of the Active Directory security lab.
