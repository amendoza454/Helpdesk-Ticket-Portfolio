
# NMG-0049 — DNS & Network Connectivity Troubleshooting

## Incident Overview

| Field | Details |
|---|---|
| Ticket ID | NMG-0049 |
| Organization | Northstar Medical Group (Simulated) |
| Category | Networking / DNS / Active Directory |
| Priority | Medium |
| Status | Resolved |
| Affected User | Jane Cooper (jcooper) |
| Department | Human Resources |
| Workstation | NMG-WS01 |
| Domain Controller | NMG-DC01 |
| Domain | NMG.com |
| Environment | Windows 11 Pro / Windows Server 2019 |

## 1. Issue Description

During a simulated IT Help Desk troubleshooting exercise, a DNS configuration issue was identified on NMG-WS01.

The workstation was querying the NAT network adapter's DNS server (`192.168.1.1`) instead of the internal Active Directory DNS server (`192.168.50.10`).

As a result, `NMG.com` resolved to an incorrect public IP address (`67.215.13.38`) rather than the internal domain controller.

This configuration could interfere with Active Directory authentication, domain controller discovery, and access to internal network resources.

**Objective:** Identify the incorrect DNS configuration, restore internal domain name resolution, and verify access to Active Directory resources.

### Initial Help Desk Ticket

The following Jira-style image illustrates the simulated incident before remediation.

**Note:** This is an AI-generated illustration, not a screenshot from a live Jira instance. Any additional failures depicted in the illustration were not independently verified.

![Simulated Initial Incident Ticket](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/NMG-0049_Initial_Incident_Simulated.png)

### Initial DNS Resolution Error

The workstation initially used `192.168.1.1` for DNS and returned a public IP address.

**Recorded findings:**

```text
DNS Server: 192.168.1.1
Domain: NMG.com
Resolved Address: 67.215.13.38
```

The following image reconstructs the original DNS lookup using the values observed during troubleshooting.

**SIMULATED / RECONSTRUCTED SCREENSHOT — Not original command output.**

![Reconstructed DNS Error](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/NMG-0049_Initial_DNS_Error_Simulated.png)

## 2. Lab Environment

This incident was investigated in my Northstar Medical Group Active Directory homelab using Oracle VirtualBox.

**Technologies Used:**
- Windows Server 2019
- Windows 11 Pro
- Active Directory Domain Services (AD DS)
- Windows DNS
- TCP/IP Networking
- Oracle VirtualBox
- Command Prompt
- Network Connections

**Affected Systems:**
- Domain Controller: NMG-DC01
- Workstation: NMG-WS01
- Domain: NMG.com
- Internal DNS Server: 192.168.50.10
- Workstation Internal IP: 192.168.50.20

The workstation uses two network adapters:

| Adapter | Purpose |
|---|---|
| Ethernet 2 (Internal) | Active Directory lab network |
| Ethernet (NAT) | External network connectivity |

## 3. Troubleshooting Process

### Step 1 — Verify Network Connectivity

Opened Command Prompt on NMG-WS01 and tested connectivity to the domain controller.

```cmd
ping 192.168.50.10
```

**Result:** Successful communication with 0% packet loss.

This confirmed that basic network connectivity between the workstation and domain controller was functioning.

### Step 2 — Investigate DNS Resolution

Executed:

```cmd
nslookup NMG.com
```

**Finding:** The workstation queried DNS server `192.168.1.1` and returned the public IP address `67.215.13.38`.

To test the internal DNS server directly, executed:

```cmd
nslookup NMG.com 192.168.50.10
```

**Result:** The internal DNS server successfully returned the domain controller's internal IP address.

This indicated that the internal DNS service was functioning and that the workstation's DNS server selection was the primary issue.

### Step 3 — Inspect Network Adapter Configuration

Reviewed the workstation's DNS settings using:

```cmd
netsh interface ipv4 show dnsservers
```

**Findings:**

| Network Adapter | DNS Server |
|---|---|
| Ethernet (NAT) | 192.168.1.1 |
| Ethernet 2 (Internal) | 192.168.50.10 |

The NAT adapter obtained its DNS configuration through DHCP, while the internal adapter used the domain controller as its DNS server.

![Network Adapter DNS Configuration](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/NMG-0049_Network_Adapter_DNS.png)

### Step 4 — Adjust Network Interface Metrics

Opened Network Connections:

```cmd
ncpa.cpl
```

Navigated to each adapter's IPv4 advanced settings and configured the following interface metrics:

| Network Adapter | Interface Metric |
|---|---|
| Ethernet 2 (Internal) | 10 |
| Ethernet (NAT) | 50 |

The internal adapter was assigned a lower metric than the NAT adapter.

This change was followed by successful DNS resolution using the internal DNS server.

![Network Interface Metrics](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/NMG-0049_Interface_Metrics.png)

### Step 5 — Flush DNS Resolver Cache

Executed:

```cmd
ipconfig /flushdns
```

**Output:**

```text
Windows IP Configuration

Successfully flushed the DNS Resolver Cache.
```

This cleared cached DNS resolver information before retesting domain resolution.

## 4. Root Cause Analysis

**Root Cause:** Incorrect DNS server selection on a dual-network-adapter Windows workstation.

NMG-WS01 had two configured DNS servers:

- NAT adapter: `192.168.1.1`
- Internal adapter: `192.168.50.10`

The workstation initially selected the NAT adapter's DNS server, which returned a public IP address for `NMG.com`.

Because Active Directory depends on internal DNS records for domain services, this behavior could interfere with domain resource discovery.

After adjusting interface metrics and flushing the DNS cache, the workstation successfully queried the internal DNS server.

The observed before-and-after results support incorrect DNS server selection as the cause of the incident.

## 5. Resolution

The following corrective actions were completed:

1. Identified the DNS server selected by the workstation.
2. Verified that the internal domain controller DNS service was responding correctly.
3. Reviewed DNS settings on both network adapters.
4. Configured the internal adapter with interface metric `10`.
5. Configured the NAT adapter with interface metric `50`.
6. Flushed the DNS resolver cache.
7. Verified DNS resolution and domain controller discovery.
8. Confirmed access to the HR shared drive.

No changes to Active Directory user accounts or file share permissions were required.

## 6. Post-Resolution Verification

### Test 1 — DNS Resolution

Executed:

```cmd
nslookup NMG.com
```

**Verified Output:**

```text
Server:  UnKnown
Address:  192.168.50.10

Name:    NMG.com
Addresses:  fd17:625c:f037:2:91ab:3401:42c3:b8c6
          192.168.50.10
          10.0.2.15
```

**Result:** Passed.

The workstation successfully queried the internal DNS server.

The `Server: UnKnown` message did not prevent successful DNS resolution and may indicate that a reverse DNS record was unavailable.

![Corrected DNS Resolution](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/NMG-0049_DNS_Resolution_Fixed.png)

### Test 2 — Domain Controller Discovery

Executed:

```cmd
nltest /dsgetdc:NMG.com
```

**Verified Output:**

```text
DC: \\NMG-DC01.NMG.com
Address: \\192.168.50.10
Dom Name: NMG.com
Forest Name: NMG.com

The command completed successfully
```

**Result:** Passed.

The workstation successfully located the Active Directory domain controller.

![Domain Controller Discovery](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/NMG-0049_DC_Discovery.png)

### Test 3 — HR Shared Drive Access

Logged in as `NMG\jcooper` and opened the mapped HR departmental drive (`H:`).

Verified that the existing test file was accessible.

**Result:** Passed.

![HR Shared Drive Verification](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/NMG-0049_HR_Drive_Verification.png)

## 7. Final Help Desk Ticket

The following simulated Jira-style ticket summarizes the investigation, resolution, and successful verification.

**Note:** This image was generated to illustrate the completed incident and does not represent an actual Jira system record.

![NMG-0049 Resolved Jira Ticket](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/NMG-0049_Jira_Ticket.png)

## 8. Resolution Summary

| Verification Test | Result |
|---|---|
| Network connectivity to domain controller | Passed |
| Internal DNS server response | Passed |
| Correct DNS server selection | Passed |
| Internal domain name resolution | Passed |
| Domain controller discovery | Passed |
| HR shared drive access | Passed |

**Final Incident Status: RESOLVED**

## 9. Remaining Configuration Improvement

During verification, the domain controller returned an additional address: `10.0.2.15`.

This address is associated with the domain controller's NAT adapter.

Although domain controller discovery and HR shared drive access succeeded, registering NAT addresses in internal Active Directory DNS can create future connectivity issues.

**Recommended improvement:** Review DNS registration settings on NMG-DC01 and prevent unwanted NAT adapter addresses from being published in internal DNS.

This improvement is separate from the resolved incident.

## 10. Skills Demonstrated

- Windows 11 network troubleshooting
- Active Directory DNS troubleshooting
- TCP/IP connectivity testing
- DNS server configuration analysis
- Network adapter interface metrics
- DNS cache management
- Domain controller discovery using NLTEST
- Network share connectivity verification
- Root cause analysis
- Incident documentation
- Resolution verification

## 11. Lessons Learned

This exercise demonstrated how incorrect DNS server selection can affect Windows domain environments even when basic network connectivity is functioning.

**Key Takeaways:**

1. Successful ping tests do not guarantee correct DNS configuration.
2. Active Directory clients should query DNS servers that can resolve internal domain records.
3. Multiple network adapters can complicate DNS server selection.
4. Testing a specific DNS server helps isolate configuration issues.
5. DNS troubleshooting should include both command-line diagnostics and functional verification.
6. Real troubleshooting evidence should be distinguished from reconstructed or simulated illustrations.

## 12. Related Projects

This incident was investigated using the Active Directory environment configured in my existing infrastructure project.

[Windows Infrastructure & Access Control — AD, GPO & RBAC](https://github.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC)

---

**Ticket:** NMG-0049  
**Status:** Resolved  
**Environment:** Northstar Medical Group (Simulated)  
**Category:** IT Help Desk / DNS / Active Directory / Networking

*All employees, incidents, and organizational details are simulated for educational purposes. AI-generated illustrations are labeled separately from actual homelab screenshots.*
