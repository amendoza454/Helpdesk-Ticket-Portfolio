
# NMG-0047 — New Employee Onboarding & Access Troubleshooting

## Incident Overview

| Field | Details |
|---|---|
| Ticket ID | NMG-0047 |
| Organization | Northstar Medical Group (Simulated) |
| Category | Active Directory / Onboarding / RBAC |
| Priority | High |
| Status | Resolved |
| Affected User | Jane Cooper (jcooper) |
| Department | Human Resources |
| Position | HR Payroll Specialist |
| Domain | NMG.com |
| Environment | Windows Server 2019 / Active Directory |
| Virtualization | Oracle VirtualBox |

## 1. Issue Description

Jane Cooper, a newly onboarded HR Payroll Specialist, reported that she could successfully sign in but could not access the departmental resources required for her role.

The simulated help desk ticket identified the following problems:

- Access denied when attempting to open HR shared resources.
- Different desktop policies and restrictions compared with HR coworkers.
- Incorrect or missing mapped network drives.
- The issue affected Jane but not other HR employees.

**Objective:** Investigate Jane's Active Directory configuration, identify the cause of the access issue, correct her account placement, and verify appropriate departmental access.

### Initial Help Desk Ticket

The following simulated Jira-style ticket documents the reported incident.

![NMG-0047 Jira Ticket](https://raw.githubusercontent.com/amendoza454/Basic-Employee-Onboarding---AD-RBAC/main/Screenshots/Day-4_Jira_Ticket_NMG-0047.png)

## 2. Lab Environment

This incident was investigated in my Northstar Medical Group Active Directory homelab.

**Technologies Used:**

- Windows Server 2019
- Active Directory Domain Services (AD DS)
- Active Directory Users and Computers (ADUC)
- Organizational Units (OUs)
- Role-Based Access Control (RBAC)
- Active Directory Security Groups
- Oracle VirtualBox

**Active Directory Configuration:**

| Component | Configuration |
|---|---|
| Domain Controller | NMG-DC01 |
| Domain | NMG.com |
| Affected Account | jcooper |
| Intended Department | HR |
| Intended Security Group | HR-Users |
| Intended Organizational Unit | HR |

## 3. Troubleshooting Process

### Step 1 — Review the Reported Issue

Reviewed the simulated help desk ticket to understand the reported symptoms.

**Findings:**

- Jane could authenticate successfully.
- Other HR employees were not experiencing the same issue.
- Jane's desktop configuration and resource access differed from those of her coworkers.

These findings suggested a possible issue with her Active Directory account configuration rather than a domain-wide outage.

### Step 2 — Inspect Active Directory Account Placement

Opened Active Directory Users and Computers on NMG-DC01.

Located Jane Cooper's account and inspected its organizational unit placement.

**Finding:** Jane's account was located in an incorrect organizational unit.

Incorrect OU placement can cause a user to receive different Group Policy settings when user-scoped policies are linked to specific OUs.

![Jane Cooper Incorrect OU](https://raw.githubusercontent.com/amendoza454/Basic-Employee-Onboarding---AD-RBAC/main/Screenshots/Day-4_jcooper_Incorrect_OU.png)

### Step 3 — Review Departmental Role and Group Membership

Reviewed the Active Directory structure and departmental security groups.

Jane's intended configuration was:

| Setting | Expected Configuration |
|---|---|
| Organizational Unit | HR |
| Security Group | HR-Users |
| Department | Human Resources |
| Access Model | Role-Based Access Control |

Security groups are used to assign access permissions based on departmental roles rather than individual user accounts.

This helps maintain consistent access controls during employee onboarding.

### Step 4 — Correct Organizational Unit Placement

Moved Jane Cooper's user account into the correct HR organizational unit.

This aligned her account placement with the organization's intended Active Directory structure.

![Jane Cooper Correct OU](https://raw.githubusercontent.com/amendoza454/Basic-Employee-Onboarding---AD-RBAC/main/Screenshots/Day-4_jcooper_Correct_OU.png)

### Step 5 — Verify Account Configuration

Reviewed Jane's updated Active Directory account configuration.

Confirmed that her account was positioned within the intended HR organizational structure.

![Jane Cooper Correct Security Group](https://raw.githubusercontent.com/amendoza454/Basic-Employee-Onboarding---AD-RBAC/main/Screenshots/Day-4_Jane_Cooper_Correct_Security_Group.png)

## 4. Root Cause Analysis

**Root Cause:** Incorrect Active Directory organizational unit placement during employee onboarding.

Jane Cooper's account was not located in the intended HR organizational unit.

This created an inconsistency between her account configuration and the organization's departmental structure.

OU placement determines which user-scoped Group Policy Objects may apply to an account, while security group membership is commonly used to control access to departmental resources.

Correcting the OU placement and reviewing role-based group membership helped align Jane's account with the expected HR configuration.

## 5. Resolution

The following corrective actions were completed:

1. Reviewed the reported access and desktop configuration issues.
2. Located Jane Cooper's account in Active Directory.
3. Identified incorrect organizational unit placement.
4. Moved the account to the HR organizational unit.
5. Reviewed the intended HR security group configuration.
6. Verified the corrected account placement.

No domain-wide configuration changes were required.

## 6. Post-Resolution Verification

Verified Jane Cooper's account configuration in Active Directory Users and Computers.

| Verification Test | Result |
|---|---|
| User account located in AD | Passed |
| Incorrect OU identified | Passed |
| Account moved to HR OU | Passed |
| Corrected OU placement verified | Passed |
| Departmental security group reviewed | Passed |

**Result:** The documented Active Directory configuration issue was corrected.

The available Day 4 evidence demonstrates the account configuration correction. It does not independently establish that every originally reported workstation policy, mapped drive, and shared-resource symptom was retested.

## 7. Supporting Incident Documentation

The original incident resolution notes are available in my onboarding repository.

[View NMG-0047 Resolution Notes](https://github.com/amendoza454/Basic-Employee-Onboarding---AD-RBAC/blob/main/Incident%20Reports/NMG-0047-Resolution.txt)

## 8. Resolution Summary

| Item | Outcome |
|---|---|
| Account authentication | Reported working |
| Incorrect OU placement | Identified |
| Correct HR OU placement | Verified |
| Departmental role configuration | Reviewed |
| AD account configuration | Corrected |

**Ticket Status: RESOLVED**

## 9. Skills Demonstrated

- Active Directory troubleshooting
- User account administration
- Organizational Unit management
- Role-Based Access Control (RBAC)
- Security group management
- Employee onboarding support
- Access control investigation
- Root cause analysis
- Incident documentation
- Resolution verification

## 10. Lessons Learned

This exercise demonstrated how incorrect Active Directory account configuration can create inconsistencies during employee onboarding.

**Key Takeaways:**

1. Successful authentication does not guarantee appropriate resource access.
2. Organizational Unit placement affects the application of user-scoped Group Policy.
3. Security group membership should be reviewed when investigating access issues.
4. Employee onboarding should follow standardized account provisioning procedures.
5. Corrective actions should be verified and documented before closing a ticket.

## 11. Related Projects

This incident was investigated as part of my original Active Directory onboarding project.

[Basic Employee Onboarding — Active Directory & RBAC](https://github.com/amendoza454/Basic-Employee-Onboarding---AD-RBAC)

---

**Ticket:** NMG-0047  
**Status:** Resolved  
**Environment:** Northstar Medical Group (Simulated)  
**Category:** IT Help Desk / Active Directory / Onboarding / RBAC

*All employees, incidents, and organizational details are simulated for educational purposes.*
