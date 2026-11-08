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
| Environment | Windows 11 / Windows Server 2019 |

## 1. Issue Description

Jane Cooper, an HR employee, was unable to sign in to her domain account after multiple unsuccessful password attempts.

The account was locked by the Active Directory account lockout policy, preventing authentication until the account was unlocked.

**Objective:** Investigate the authentication failure, identify the source of the lockout, restore account access, and verify successful login.

## 2. Lab Environment

This incident was reproduced in my Northstar Medical Group Active Directory homelab.

**Technologies Used:**
- Windows Server 2019
- Windows 11 Pro
- Active Directory Domain Services (AD DS)
- Active Directory Users and Computers (ADUC)
- Group Policy Management
- Windows Event Viewer
- Oracle VirtualBox

**Affected Systems:**
- Domain Controller: NMG-DC01
- Workstation: NMG-WS01
- User Account: NMG\jcooper

## 3. Troubleshooting Process

### Step 1 — Investigate the Account

Opened Active Directory Users and Computers (ADUC) on NMG-DC01.

Located Jane Cooper's account in the HR organizational unit and inspected its account status.

**Finding:** The account was locked out.

![Locked Account](../Screenshots/NMG-0048/Day-6_jcooper_Account_Lockout.png)

### Step 2 — Review Account Lockout Policy

Opened Group Policy Management and inspected the domain's account lockout policy.

The following settings were configured for the lab:

| Policy | Value |
|---|---|
| Account Lockout Threshold | 5 invalid attempts |
| Account Lockout Duration | 15 minutes |
| Reset Account Lockout Counter | 15 minutes |

These settings allowed the account lockout scenario to be reproduced in a controlled environment.

![Account Lockout Policy](../Screenshots/NMG-0048/Day-6_Account_Lockout_GPO.png)

### Step 3 — Investigate Windows Security Logs

Opened Event Viewer on NMG-DC01.

Navigated to:

`Windows Logs > Security`

Located **Event ID 4740**, indicating that a user account was locked out.

**Findings:**
- Event ID: 4740
- Affected Account: jcooper
- Domain: NMG
- Source Workstation: NMG-WS01

The security event confirmed that Active Directory had recorded the account lockout and identified the workstation associated with the event.

![Event ID 4740](../Screenshots/NMG-0048/Day-6_EventID_4740.png)

### Step 4 — Identify the Root Cause

The lockout occurred after multiple unsuccessful authentication attempts against Jane Cooper's domain account.

The configured account lockout threshold was reached, causing Active Directory to temporarily block additional sign-in attempts.

Event Viewer identified NMG-WS01 as the workstation associated with the lockout.

**Root Cause:** Repeated invalid password attempts triggered the Active Directory account lockout policy.

## 4. Resolution

Used Active Directory Users and Computers to restore Jane Cooper's account access.

**Actions Taken:**

1. Located `jcooper` in the HR organizational unit.
2. Opened the account properties.
3. Confirmed the account was locked.
4. Unlocked the account.
5. Applied the changes.
6. Returned to NMG-WS01 to verify authentication.

![Account Unlock](../Screenshots/NMG-0048/Day-6_AD_AccountLockout.png)

## 5. Verification

After unlocking the account, signed in to NMG-WS01 using Jane Cooper's domain credentials.

Executed:

```cmd id="dbj2tx"
whoami
```

**Verified Output:**

```text id="0tj4x3"
nmg\jcooper
```

The command confirmed that the workstation session was authenticated under the intended domain user.

**Result:** Successful login.

![Successful Login Verification](../Screenshots/NMG-0048/Day-6_Ticket_Resolved_Login_Verification.png)

## 6. Incident Ticket

The following Jira-style ticket was created to document the simulated help desk incident.

![NMG-0048 Jira Ticket](../Screenshots/NMG-0048/Day-6_Jira_Ticket_NMG-0048.png)

## 7. Resolution Summary

| Verification | Result |
|---|---|
| Account lockout confirmed | Passed |
| Lockout policy reviewed | Passed |
| Event ID 4740 identified | Passed |
| Source workstation identified | Passed |
| Account successfully unlocked | Passed |
| Domain login verified | Passed |

**Final Status: RESOLVED**

## 8. Skills Demonstrated

- Active Directory account administration
- Windows authentication troubleshooting
- Account lockout policy configuration
- Group Policy Management
- Windows Security Event Log analysis
- Event ID 4740 investigation
- Domain user authentication
- Incident documentation
- Root cause analysis
- Resolution verification

## 9. Lessons Learned

This exercise demonstrated how Active Directory account lockout policies protect domain accounts from repeated unsuccessful authentication attempts.

Key takeaways:

1. Account lockouts can be investigated through Active Directory Users and Computers.
2. Event ID 4740 provides evidence of account lockout activity.
3. Security logs can help identify the workstation associated with an incident.
4. Unlocking an account should be followed by authentication testing.
5. Help desk incidents should include documented troubleshooting steps and verification evidence.

---

**Ticket:** NMG-0048  
**Status:** Resolved  
**Environment:** Northstar Medical Group (Simulated)  
**Category:** IT Help Desk / Active Directory / Authentication
