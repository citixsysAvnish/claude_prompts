Below are reusable **Claude prompts** for creating a **Frappe Framework application**, including architecture, DocTypes, workflows, APIs, permissions, reports, and deployment. These prompts are designed for Claude Code / Claude with VS Code.

---

## Prompt 1: Act as Frappe Framework Expert

```
You are a senior Frappe Framework and ERPNext developer with 10+ years of experience.

Your task is to help me design and develop a production-ready Frappe application.

Follow Frappe best practices:
- Use Python backend
- Use Frappe ORM
- Use DocTypes instead of custom database tables wherever possible
- Follow Frappe app structure
- Use hooks.py properly
- Use server scripts only when required
- Create clean APIs
- Apply role-based permissions
- Write maintainable code

Before creating anything:
1. Analyze the business requirement
2. Suggest application architecture
3. Define DocTypes
4. Define workflow
5. Define user roles
6. Define permissions
7. Define reports
8. Define integrations
9. Then start development

Always explain:
- Why you choose a specific design
- Alternative approaches
- Security considerations
- Performance considerations

Do not generate code until the design is approved.
```

---

# Prompt 2: Create New Frappe App

```
Create a new Frappe application called:

App Name:
[ENTER APP NAME]

Business Purpose:
[ENTER DESCRIPTION]

Generate:

1. App architecture
2. Module structure
3. Required DocTypes
4. Child Tables
5. Naming Series
6. Custom Fields
7. Workflow states
8. Roles
9. Permissions Matrix
10. Reports
11. Dashboard Charts
12. API endpoints
13. Scheduled Jobs
14. Email Notifications

Follow Frappe Framework standards.

Provide the solution as a technical design document first.
```

---

# Prompt 3: Create Business Workflow

```
Design a complete workflow in Frappe for:

Business Process:
[Example: Purchase Request Approval]

Create:

1. Workflow States
Example:
Draft
Submitted
Manager Approval
Finance Approval
Approved
Rejected
Cancelled

2. Workflow Actions

3. Allowed Roles

4. State Transition Diagram

5. Email Notifications

6. Escalation Rules

7. Audit Trail Requirements

8. Permission Rules

Provide workflow JSON configuration also.
```

---

# Prompt 4: Create DocTypes

```
Design Frappe DocTypes for this requirement:

Requirement:
[ENTER REQUIREMENT]

Create:

Parent DocType:
Name:
Purpose:

Fields:
- Field Name
- Field Type
- Mandatory
- Default Value
- Validation Rule
- Description

Child Tables:

Naming Series:

Controller Logic Required:

Server Scripts Required:

Client Scripts Required:

Permissions:

Workflow:

Create the final DocType JSON structure.
```

---

# Prompt 5: Generate Frappe Backend Code

```
Act as a senior Frappe Python developer.

Create backend code for:

DocType:
[NAME]

Requirement:
[DESCRIPTION]


Generate:

1. hooks.py changes
2. Python Controller
3. validate()
4. before_save()
5. before_submit()
6. on_cancel()
7. Custom methods
8. API methods
9. Error handling
10. Logging

Follow:
- frappe.whitelist()
- frappe.db methods
- frappe.throw()
- frappe.msgprint()

Use clean production coding standards.
```

---

# Prompt 6: Create Frappe REST API

```
Create REST API endpoints in Frappe for:

Integration:
[System Name]

Purpose:
[Example: Sync Customer Data]


Create:

API Name:
HTTP Method:
Authentication:
Request JSON:
Response JSON:

Validation:
Error Handling:
Database Operations:

Also create:
- Python API method
- API documentation
- Sample CURL request
```

---

# Prompt 7: Integration Workflow (ERP / External System)

```
Design an integration between Frappe ERP and:

External System:
[SAP / SAGE X3 / iVend / CRM]

Objects:
[List Objects]

Example:
Customer
Product
Inventory
Sales Invoice


Design:

1. Integration Architecture
2. Data Flow Diagram
3. Field Mapping
4. Sync Frequency
5. Error Handling
6. Retry Mechanism
7. Queue Processing
8. Background Jobs
9. API Security
10. Logging Framework

Provide enterprise-grade integration design.
```

---

# Prompt 8: Create Frappe Reports

```
Create reports in Frappe for:

Business Requirement:
[ENTER]

Generate:

Report Name:

Type:
- Query Report
- Script Report

Filters:

Columns:

SQL Query:

Python Logic:

Permissions:

Performance Optimization:

Index Recommendation:
```

---

# Prompt 9: Create Complete Module Example

Example: Inventory Management Module

```
Create a complete Frappe module for Inventory Management.

Requirements:

Features:
- Product Master
- Warehouse
- Stock Entry
- Stock Transfer
- Approval Workflow
- Stock Ledger
- Inventory Reports

Create:

1. DocTypes
2. Database Design
3. Workflow
4. Roles
5. Permissions
6. APIs
7. Reports
8. Notifications
9. Dashboard
10. Deployment Steps

Develop this like a commercial ERP module.
```

---

# Prompt 10: Claude Code Development Prompt (VS Code)

```
You are working inside VS Code on a Frappe Framework project.

Your responsibilities:

1. Analyze existing code before modification.
2. Never overwrite existing functionality.
3. Follow existing coding patterns.
4. Explain every file change.
5. Create migration patches for database changes.
6. Add comments where business logic exists.
7. Write test cases.
8. Check security issues.
9. Optimize SQL queries.
10. Verify compatibility with Frappe version.

Before coding:
- Show implementation plan.

After coding:
- Provide:
  - Changed files
  - Migration steps
  - Testing steps
  - Rollback plan
```

---
