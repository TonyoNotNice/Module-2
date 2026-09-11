# Module 2 — Least-Privilege Roles & Custom Privileges

A hands-on SAP HANA Cloud security module demonstrating role-based access control (RBAC) and the principle of least privilege — applied to real-time South African government tender data.

## Objectives

- Design a custom role with minimal privileges for a specific job function
- Create a non-admin user and grant them the role
- Enforce read-only access: verify the user can SELECT but cannot write
- Build a real-time data pipeline to make the exercise realistic

## Data Source

Live data from the **Tenders-SA public widget API** — no authentication required:

| Endpoint | Purpose | Rows Loaded |
|---|---|---|
| `/api/widgets/sector-trends` | Tender value and volume by sector | 10 |
| `/api/widgets/winners-feed` | Live feed of recent tender awards | 100 |

**Total: 110 real SA tender records** loaded into SAP HANA Cloud.

## Architecture
Tenders-SA Public API
│
│ HTTPS GET (JSON)
▼
Python Script (requests + hdbcli)
│
│ Parameterized INSERT
▼
SAP HANA Cloud
Schema: TENDER_MONITOR
├── SECTOR_TRENDS
└── RECENT_AWARDS
│
│ Granted via role
▼
Role: TENDER_ANALYST (SELECT only)
│
│ Granted to
▼
User: TENDER_VIEWER

## Role and User Design

**Role: `TENDER_ANALYST`**

```sql
CREATE ROLE TENDER_ANALYST;
GRANT SELECT ON SCHEMA TENDER_MONITOR TO TENDER_ANALYST;
