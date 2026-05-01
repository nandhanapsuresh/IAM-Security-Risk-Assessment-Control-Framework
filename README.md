# NovaPay Financial Technologies — IAM Security Assessment & Control Framework

**Assessment Period:** April 2026
**Prepared by:** Nandhana P Suresh, LEAD College (Autonomous), Palakkad  
**Total Effort:** 90 hours | **Project Date:** April 2026

---

## Overview

This repository contains the complete deliverables for the **IAM Security Risk Assessment & Control Framework** industry project conducted for NovaPay Financial Technologies — a Series-C FinTech company processing $2.3B in daily transactions, subject to PCI-DSS Level 1, SOC 2 Type II, and FFIEC regulatory requirements.

The project assessed 44 identities across 5 departments and 5 critical systems, analysed 165 authentication log events (May 2025), identified 15 security risks, and produced a Defence-in-Depth control framework with 31 controls across 8 domains.

---

## Repository Contents

| File                                        | Type           | Description                                                                                                                         |
| ------------------------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `IAM_Inventory.xlsx`                        | Excel Workbook | Core identity inventory with risk flags, department access matrix, and risk assessment scoring                                      |
| `IAM_Inventory_WithLogs.xlsx`               | Excel Workbook | Inventory extended with 165 authentication log events (May 1–15, 2025), including anomaly flags and analyst notes                   |
| `NovaPay_IAM_OrganizationOverview.docx`     | Word Document  | Section 1 of the assessment report — company profile, identity types, authentication methods, MFA coverage, and RBAC model          |
| `NovaPay_IAM_SecurityControls_Updated.docx` | Word Document  | Section 2 — Full control framework (8 domains, 31 controls) with implementation steps, standards alignment, and remediation roadmap |
| `NovaPay_IAM_Final.docx`                    | Word Document  | Complete industry project report including objectives, methodology, problem statement, findings, recommendations, and conclusion    |

---

## IAM_Inventory.xlsx — Sheet Guide

| Sheet                      | Contents                                                                                                                                                      |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **IAM Identity Inventory** | 44 identities (USR-, SVC-, SHR-, BRK- prefixes) with role, department, access level, MFA status, last login, account status, and analyst-annotated risk flags |
| **Risk Dashboard**         | 12 risk categories with counts, severity, priority (P1–P3), and remediation owners                                                                            |
| **Dept Access Matrix**     | Department × System access matrix covering Web App, AWS Cloud, Admin Dashboard, VPN, and Database                                                             |
| **Risk_Assessment**        | CIA Triad × Likelihood/Impact risk scoring for all 15 identified risks (R-001 – R-015)                                                                        |

---

## IAM_Inventory_WithLogs.xlsx — Sheet Guide

| Sheet    | Contents                                                                                                                                                                  |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Logs** | 165 authentication events (EVT-XXXX) with timestamp, user ID, event type, system, source IP, geolocation, MFA method/result, anomaly flags, risk score, and analyst notes |

---

## Key Findings Summary

The assessment identified **7 CRITICAL, 5 HIGH, 2 MEDIUM, and 1 LOW** risk findings:

### Critical Risks (P1 — Immediate Action Required)

| Risk                         | Affected IDs                                | Description                                                                                                      |
| ---------------------------- | ------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| Shared Accounts (No MFA/SSO) | SHR-7001, SHR-7002, SHR-7003                | Generic Admin Dashboard accounts with static passwords unchanged 400+ days, bypassing all IdP controls           |
| Stale API/DB Credentials     | SVC-6001 (312d), SVC-6005 (187d) + 3 others | Service account credentials unrotated 88–222 days past 90-day policy; staging DB with real PAN data              |
| Orphaned/Terminated Accounts | USR-8001, USR-8002, USR-8003                | Active accounts for terminated/transferred staff; USR-8001 confirmed active in May 2025 logs                     |
| Admin Without MFA            | USR-2005                                    | Finance Manager (Level 4) with expired MFA exemption and no second factor — escalated to CISO without resolution |

### High Risks (P2 — This Sprint)

- **Role Creep** — 3 Senior Developers retaining Engineering Lead group membership from a 14-month-old restructure (USR-1001, USR-1004, USR-1008)
- **Expired MFA Exemptions** — 5 accounts with exemptions elapsed 45–125 days past policy limits
- **Inactive Active Accounts** — 3 accounts with 61–166 days since last login still set to Active
- **Over-Privileged User** — USR-2003 (Finance Analyst) holding Admin Dashboard edit rights beyond RBAC scope

---

## Notable Log Events

| Event ID           | Severity | Description                                                                                   |
| ------------------ | -------- | --------------------------------------------------------------------------------------------- |
| EVT-1161           | CRITICAL | Terminated employee (USR-8001) retrieved `staging/db-credentials` from AWS Secrets Manager    |
| EVT-1149, EVT-1151 | CRITICAL | Terminated account (USR-8003) authenticating from VPS in Panama City and Minsk                |
| EVT-1158           | CRITICAL | Shared account SHR-7001 approved a $12,500 fee waiver — no individual attribution possible    |
| EVT-1125           | HIGH     | Impossible travel: USR-2005 (Finance Manager) Austin TX → Panama City VPS within 2 hours      |
| EVT-1154           | HIGH     | USR-1001 attempted to attach `AdministratorAccess` policy to `svc-payment-processor` IAM role |
| EVT-1088–1094      | HIGH     | 7 rapid login failures on Super-Admin account (USR-5003) — potential credential stuffing      |

---

## Control Framework — 8 Domains

| Domain                                      | Controls | Risks Addressed     | Primary Technology                        |
| ------------------------------------------- | -------- | ------------------- | ----------------------------------------- |
| **CTL-MFA** — MFA Improvements              | 4        | R-007, R-010        | Okta, YubiKey, Authenticator              |
| **CTL-SSO** — SSO Consolidation             | 3        | R-001               | Okta Identity Cloud, SAML 2.0             |
| **CTL-RBAC** — RBAC Corrections             | 5        | R-008, R-009        | SailPoint, Okta Groups, IGA               |
| **CTL-LPE** — Least Privilege Enforcement   | 4        | R-002, R-003, R-012 | AWS IAM, HashiCorp Vault                  |
| **CTL-PWD** — Password & Credential Policy  | 3        | R-001, R-002, R-003 | Vault, Okta Password Policy               |
| **CTL-MON** — Monitoring & Detection        | 5        | R-004 – R-015       | Splunk SIEM, CyberArk PAM, CloudTrail     |
| **CTL-LCM** — Identity Lifecycle Management | 4        | R-004, R-005, R-006 | SailPoint, Okta Workflows, HR integration |
| **CTL-PAM** — Privileged Access Management  | 3        | R-001, R-013, R-015 | CyberArk, AWS IAM, Session recording      |

---

## Technology Stack

| Category                     | Tools                                      |
| ---------------------------- | ------------------------------------------ |
| Identity Provider            | Okta Identity Cloud                        |
| IGA / Access Certification   | SailPoint IdentityNow                      |
| Secrets Management           | HashiCorp Vault                            |
| Privileged Access Management | CyberArk PAM                               |
| SIEM / Log Analysis          | Splunk, AWS CloudTrail, Okta System Log    |
| Cloud IAM                    | AWS IAM                                    |
| MFA Hardware                 | YubiKey 5 Series (FIDO2/WebAuthn)          |
| Data Analysis                | Python (pandas, openpyxl), Microsoft Excel |

---

## Regulatory Alignment

All controls are mapped to:

- **NIST SP 800-53 Rev 5** (AC, IA, AU control families)
- **PCI-DSS v4.0** (Requirements 7, 8, 10, 12)
- **CIS Controls v8** (Controls 4, 5, 6)
- **ISO/IEC 27001:2022** (Annex A.5, A.8, A.9)

---

## Organisation Scope

| Attribute                | Details                                                                 |
| ------------------------ | ----------------------------------------------------------------------- |
| Company                  | NovaPay Financial Technologies, Inc.                                    |
| HQ                       | Austin, TX (primary) / New York, NY (compliance)                        |
| Daily Transaction Volume | $2.3 Billion                                                            |
| Total Employees in Scope | 200                                                                     |
| Departments              | Engineering, Finance, HR, Support, Security                             |
| Systems                  | Web App, Admin Dashboard, AWS Cloud (6 accounts), VPN Portal, Database  |
| Identity Types           | 36 human, 6 service accounts, 3 shared accounts, 2 break-glass accounts |

---

## Disclaimer

**NovaPay Financial Technologies is a fictional company.** All data in this repository — including identities, authentication logs, transaction volumes, risk findings, and organisational details — is entirely fictitious and created for educational and project demonstration purposes only. Any resemblance to real companies, persons, or security incidents is coincidental.
