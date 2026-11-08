# NMG-0048 — Active Directory Account Lockout

## Incident Overview

| Field | Details |
|---|---|
| Ticket ID | NMG-0048 |
| Organization | Northstar Medical Group (Simulated) |
| Category | Identity & Access Management |
| Priority | Medium |
| Status | Resolved |
| Affected User | Jane Cooper (jcooper) |
| Department | Human Resources |
| Workstation | NMG-WS01 |
| Domain Controller | NMG-DC01 |
| Domain | NMG.com |
| Environment | Windows 11 Pro / Windows Server 2019 |

## 1. Issue Description

Jane Cooper, an HR employee, was unable to sign in to her domain account after multiple unsuccessful password attempts.

The Active Directory account lockout policy temporarily blocked authentication after the configured threshold was reached.

**Objective:** Investigate the account lockout, identify its source, restore user access, and verify successful authentication.

### Help Desk Ticket

The following Jira-style ticket documents the simulated incident.

![NMG-0048 Help Desk Ticket](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/Day-6_Jira_Ticket_NMG-0048.png)

### Reported Error

The user's account was locked following repeated unsuccessful sign-in attempts.

![Account Lockout Error](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/Day-6_jcooper_Account_Lockout.png)

## 2. Lab Environment

This incident was reproduced in my Northstar Medical Group Active Directory homelab using Oracle VirtualBox.

**Technologies Used:**
- Windows Server 2019
- Windows 11 Pro
- Active Directory Domain Services (AD DS)
- Active Directory Users and Computers (ADUC)
- Group Policy Management
- Windows Event Viewer
- Command Prompt

**Affected Systems:**
- Domain Controller: NMG-DC01
- Workstation: NMG-WS01
- User Account: NMG\jcooper
- Organizational Unit: HR

## 3. Troubleshooting Process

### Step 1 — Verify Account Lockout Policy

Opened Group Policy Management on NMG-DC01 and reviewed the account lockout settings in the Default Domain Policy.

The following settings were configured for the lab:

| Policy | Configuration |
|---|---|
| Account Lockout Threshold | 5 invalid attempts |
| Account Lockout Duration | 15 minutes |
| Reset Account Lockout Counter | 15 minutes |

These settings allowed the lockout scenario to be reproduced in a controlled environment.

![Account Lockout Group Policy](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/Day-6_Account_Lockout_GPO.png)

### Step 2 — Investigate Windows Security Logs

Opened Event Viewer on NMG-DC01.

Navigated to:

`Windows Logs > Security`

Located **Event ID 4740**, indicating that a domain account had been locked out.

**Findings:**
- Event ID: 4740
- Affected Account: jcooper
- Domain: NMG
- Source Workstation: NMG-WS01

The event confirmed that Active Directory recorded the account lockout and identified the workstation associated with the incident.

![Event ID 4740](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/Day-6_EventID_4740.png)

### Step 3 — Confirm Account Status

Opened Active Directory Users and Computers (ADUC) on NMG-DC01.

Located Jane Cooper's account in the HR organizational unit and inspected its account properties.

**Finding:** The account was locked out.

This confirmed that the authentication issue was related to the account's lockout status.

## 4. Root Cause Analysis

**Root Cause:** Repeated unsuccessful authentication attempts triggered the Active Directory account lockout policy.

The domain was configured to lock accounts after five invalid password attempts.

After the threshold was reached, Jane Cooper's account was temporarily locked, preventing successful authentication.

Windows Security Event ID 4740 confirmed the lockout and identified NMG-WS01 as the associated workstation.

## 5. Resolution

Used Active Directory Users and Computers to restore Jane Cooper's account access.

**Actions Taken:**

1. Opened ADUC on NMG-DC01.
2. Located `jcooper` in the HR organizational unit.
3. Opened the user's account properties.
4. Confirmed the account was locked.
5. Unlocked the account.
6. Applied the changes.
7. Returned to NMG-WS01 to verify authentication.

![Active Directory Account Unlock](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/Day-6_AD_AccountLockout.png)

## 6. Post-Resolution Verification

After unlocking the account, signed in to NMG-WS01 using Jane Cooper's domain credentials.

Opened Command Prompt and executed:

```cmd
whoami
```

**Verified Output:**

```text
nmg\jcooper
```

The output confirmed that Jane Cooper successfully authenticated to the NMG domain.

![Successful Login Verification](https://raw.githubusercontent.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC/main/Screenshots/Day-6_Ticket_Resolved_Login_Verification.png)

**Result:** Successful domain login.

## 7. Resolution Summary

| Verification Test | Result |
|---|---|
| Account lockout policy reviewed | Passed |
| Event ID 4740 identified | Passed |
| Source workstation identified | Passed |
| Account lockout confirmed | Passed |
| Account successfully unlocked | Passed |
| Domain login verified | Passed |

**Final Incident Status: RESOLVED**

## 8. Skills Demonstrated

- Active Directory account administration
- Windows authentication troubleshooting
- Account lockout policy configuration
- Group Policy Management
- Windows Security Event Log analysis
- Event ID 4740 investigation
- Domain account recovery
- Incident documentation
- Root cause analysis
- Resolution verification

## 9. Lessons Learned

This exercise demonstrated how Active Directory account lockout policies protect domain accounts from repeated unsuccessful authentication attempts.

**Key Takeaways:**

1. Active Directory Users and Computers can be used to investigate and unlock domain accounts.
2. Event ID 4740 provides evidence of account lockout activity.
3. Security logs can identify the workstation associated with a lockout.
4. Account access should be verified after unlocking the account.
5. Help Desk troubleshooting should include root cause analysis and documented resolution.

## 10. Related Projects

This troubleshooting scenario was performed using the Active Directory environment configured in my existing infrastructure project.

[Windows Infrastructure & Access Control — AD, GPO & RBAC](https://github.com/amendoza454/Windows-Infrastructure---Access-Control--AD-GPO-RBAC)

---

**Ticket:** NMG-0048  
**Status:** Resolved  
**Environment:** Northstar Medical Group (Simulated)  
**Category:** IT Help Desk / Active Directory / Authentication

*All employees, incidents, and organizational details are simulated for educational purposes.*

---

**Ticket:** NMG-0048  
**Status:** Resolved  
**Environment:** Northstar Medical Group (Simulated)  
**Category:** IT Help Desk / Active Directory / Authentication
