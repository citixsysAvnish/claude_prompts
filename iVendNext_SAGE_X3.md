Absolutely. For this project, I would change the documentation flow from **BRD → PRD → IMPL** to:

**BRD → PRD → Process + Technical Design → Implementation Plan**

The important architectural rule is:

* **Sage X3 → iVend Next:** Master data synchronization using **Sage X3 REST APIs (outbound)**.
* **iVend Next → Sage X3:** Transactional data synchronization using **Sage X3 SOAP APIs (inbound)**.
* **iVend Next:** No external/third-party integration application and no external API layer. The connector/bridge is implemented **inside iVend Next using Frappe DocTypes, Python code, background jobs, and Frappe's internal framework**.
* Integration logs, mappings, queues, statuses, retries, configuration, and synchronization history should be represented as **internal iVend Next DocTypes**.
* The documents should distinguish clearly between **business process**, **integration process**, and **technical implementation**.

Below are the updated prompts you can use.

---

# 2. PRD — Product Requirements Document

### When to use

After the BRD is completed and approved internally.

### Input

* BRD created for the Sage X3 ↔ iVend Next integration
* Customer requirements
* Existing integration requirements and assumptions

### Prompt

# PRD — Product Requirements Document

You are a Senior Business Analyst responsible for creating a **Product Requirements Document (PRD)** for a **Sage X3 ↔ iVend Next integration project**.

Read the requirement document provided at:

`[PATH TO BRD FILE]`

Create a concise, customer-friendly PRD and save it as:

`[OUTPUT FILE PATH]`

---

## DOCUMENT HEADER

Include a header table at the top with:

| Field                 | Value                                         |
| --------------------- | --------------------------------------------- |
| Customer              | [Customer Name]                               |
| Project               | [Project Code / Project Name]                 |
| Platform              | Sage X3 + iVend Next (Frappe)                 |
| Integration Direction | Sage X3 → iVend Next and iVend Next → Sage X3 |
| Prepared by           | CitiXsys Custom Development Team              |
| Document Date         | [Today's Date]                                |
| Version               | 1.0                                           |
| Status                | Draft — Pending Customer Approval             |

---

# TABLE OF CONTENTS

After the header, add a numbered Table of Contents listing all sections.

---

# 1. Original Requirement (Verbatim)

Copy the complete content from the BRD exactly as written.

Do not:

* Rephrase
* Summarize
* Remove content
* Correct wording
* Omit clarification notes
* Omit examples
* Omit updates

The original requirement must remain unchanged.

---

# 2. Overview

Provide a concise business-friendly explanation of:

* Why Sage X3 and iVend Next need to exchange data.
* Which master data moves from Sage X3 to iVend Next.
* Which transactional data moves from iVend Next to Sage X3.
* What business problem the integration solves.
* How synchronization reduces duplicate data entry and manual work.

Do not describe implementation technologies in this section.

---

# 3. Integration Scope

Divide the scope into:

## 3.1 Sage X3 → iVend Next

Identify all master data that must be synchronized.

Examples may include:

* Products
* Product Groups
* Product Categories
* Customers
* Suppliers
* Stores
* Warehouses
* Price Lists
* Currencies
* Countries
* Tax information
* Units of Measure
* Inventory information
* Other agreed master data

Only include objects identified in the BRD.

For every object specify:

| Data Object | Source | Target | Direction | Frequency | Purpose |
| ----------- | ------ | ------ | --------- | --------- | ------- |

## 3.2 iVend Next → Sage X3

Identify all transactional data that must be synchronized.

Examples may include:

* Sales
* Sales Returns
* Payments
* Customer transactions
* Stock movements
* Goods receipts
* Stock transfers
* Inventory adjustments
* Other agreed transactions

Only include objects identified in the BRD.

## 3.3 Out of Scope

List all explicitly excluded items.

If nothing is excluded, write:

"All scenarios mentioned under In Scope are part of this requirement.

Any other scenarios are considered out of scope."

---

# 4. Business Process

Describe the complete business process without technical implementation details.

Cover:

1. Master data creation/update in Sage X3.
2. Master data becoming available for iVend Next.
3. iVend Next receiving and updating the corresponding information.
4. Business users using synchronized master data.
5. Transactions being created in iVend Next.
6. Transaction information being prepared for Sage X3.
7. Sage X3 receiving and processing transactions.
8. Successful and failed synchronization scenarios.
9. Correction and resubmission scenarios.

Use step-by-step business flows.

---

# 5. Business Scenarios / User Flows

Create separate flows for each major integration process.

Examples:

### Scenario 1 — New Product

1. Product is created in Sage X3.
2. Product becomes eligible for synchronization.
3. Product information is transferred to iVend Next.
4. iVend Next creates or updates the product.
5. Product becomes available for business use.
6. Synchronization status is recorded.

### Scenario 2 — Product Update

Describe the expected behaviour when an existing product changes.

### Scenario 3 — New Customer

Describe the complete business flow.

### Scenario 4 — Sales Transaction

Describe how a transaction created in iVend Next reaches Sage X3.

### Scenario 5 — Failed Transaction

Describe how users identify, correct, and resubmit a failed transaction.

Create additional scenarios based on the BRD.

---

# 6. Functional Requirements

List the functional requirements using:

* FR-01
* FR-02
* FR-03
* etc.

Group them into:

## 6.1 Integration Configuration

Examples:

* Business unit/company configuration
* Synchronization enable/disable
* Synchronization frequency
* Object enable/disable
* Mapping configuration

## 6.2 Master Data Synchronization

Define requirements for every master-data object.

## 6.3 Transaction Synchronization

Define requirements for every transaction object.

## 6.4 Error Handling

Define:

* Failed records
* Error visibility
* Retry
* Resubmission
* Duplicate prevention
* Processing status

## 6.5 Synchronization Monitoring

Define what business users need to see regarding:

* Pending
* Processing
* Successful
* Failed
* Retried
* Completed

Do not specify implementation classes, APIs, database tables, or code.

---

# 7. Worked Examples

Include this section only where calculations, mappings, conditional logic, or transformation rules are required.

For each example provide:

| Input | Business Rule | Expected Output |
| ----- | ------------- | --------------- |

Use realistic examples.

---

# 8. Screen Details

Include only if iVend Next requires user-facing screens.

For every screen specify:

### Screen Name

### Navigation

### Purpose

### Fields

| Field | Purpose | Mandatory | Example |
| ----- | ------- | --------- | ------- |

### Actions

| Action | Purpose |
| ------ | ------- |

### Messages

List all user-facing messages and alerts.

Do not describe technical implementation.

---

# 9. Integration Object Mapping

Create a business-level mapping for every synchronized object.

Example:

| Sage X3 Business Object | iVend Next Business Object | Direction | Business Purpose |
| ----------------------- | -------------------------- | --------- | ---------------- |

For field mapping:

| Sage X3 Field | Business Meaning | iVend Next Field | Business Meaning | Required |
| ------------- | ---------------- | ---------------- | ---------------- | -------- |

Do not include database table names or programming variables.

---

# 10. Impact Analysis

Assess impact on:

### Sage X3

* Existing master data
* Existing transactions
* Existing business processes
* Existing configurations

### iVend Next

* Existing master data
* Existing transactions
* Existing business processes
* Existing configurations

### Previously Delivered Customizations

List known customer-specific customizations and explain whether this integration affects them.

### Master Data

Explain which system is the source of truth for each object.

### Transaction Data

Explain which system creates and owns each transaction.

---

# 11. Data Requirements

Group the business data logically.

Examples:

## Product

| Field | Business Purpose |
| ----- | ---------------- |

## Customer

| Field | Business Purpose |
| ----- | ---------------- |

## Sales Transaction

| Field | Business Purpose |
| ----- | ---------------- |

## Synchronization Control

| Field | Business Purpose |
| ----- | ---------------- |

Do not include database schema or technical field definitions.

---

# 12. Validation Rules

List important business rules using:

* V-01
* V-02
* V-03

Examples:

* Duplicate master records must not be created.
* Required master data must exist before a transaction can be processed.
* Failed transactions must not be treated as successfully synchronized.
* A successfully synchronized transaction must not be submitted again unless explicitly resubmitted.
* Only eligible records should be synchronized.

---

# 13. Dependencies & Assumptions

## Dependencies

Document required:

* Sage X3 configuration
* Sage X3 integration access
* iVend Next configuration
* Required master data
* Required business rules
* Required mappings
* Customer-provided information

## Assumptions

Document agreed assumptions such as:

* Sage X3 is the source of truth for defined master data.
* iVend Next is the source of truth for defined retail transactions.
* Only agreed objects will be synchronized.
* Customer will provide required mappings.
* Customer will provide required Sage X3 access and credentials.

---

# 14. Synchronization Frequency

For each object define:

| Object | Direction | Frequency | Trigger |
| ------ | --------- | --------- | ------- |

Possible frequencies:

* Real-time
* Scheduled
* Periodic
* On demand

Only use values agreed in the BRD.

---

# 15. UAT Scenarios

Create a table:

| Test Case ID | Scenario | Steps | Expected Result |
| ------------ | -------- | ----- | --------------- |

Cover:

* New master data
* Updated master data
* Duplicate master data
* Missing mandatory data
* Successful transactions
* Failed transactions
* Retry
* Resubmission
* Duplicate prevention
* Synchronization status
* Data mapping

After the last test case add exactly:

"These are the UAT scenarios required to be validated by the customer.

If the customer has any additional UAT scenarios, they must be communicated and agreed upon before SOW finalization."

---

# 16. Estimation

Provide a high-level estimate:

| Component                     | Estimated Hours |
| ----------------------------- | --------------: |
| Business Analysis             |                 |
| Functional Design             |                 |
| Master Data Synchronization   |                 |
| Transaction Synchronization   |                 |
| iVend Next User Interface     |                 |
| Validation and Error Handling |                 |
| Testing                       |                 |
| UAT Support                   |                 |
| Documentation                 |                 |
| Deployment                    |                 |
| **Total**                     |                 |

Add:

"Any scope changes after sign-off will be estimated separately."

---

# STRICT PRD RULES

1. This is a **business requirements document**, not a technical design.
2. Do not include:

   * Python code
   * 4GL code
   * SOAP request XML
   * REST request examples
   * API URLs
   * Database schemas
   * SQL
   * Class names
   * Method names
   * File paths
   * Technical architecture
3. Do not mention third-party middleware.
4. Do not introduce external integration applications.
5. Clearly identify the direction of every integration.
6. Clearly identify the system of record for every business object.
7. Keep language simple and customer-friendly.
8. Do not invent requirements that are not present in the BRD.
9. If information is missing, mark it as **TBD / Customer Confirmation Required**.
10. The final document must be suitable for customer review and sign-off.

---

# 3. Process + Technical Design Document

For this project, I recommend adding a document **between PRD and Implementation Plan**.

This is particularly important because the integration has two fundamentally different mechanisms:

**Sage X3 → iVend Next**

`Sage X3 REST → iVend Next Internal Connector`

**iVend Next → Sage X3**

`iVend Next Internal Connector → Sage X3 SOAP`

The following prompt can generate that document.

# PROCESS + TECHNICAL DESIGN DOCUMENT

You are a **Senior Solution Architect and Sage X3 / Frappe / iVend Next Integration Specialist**.

Create a detailed **Process and Technical Design Document** for the approved Sage X3 ↔ iVend Next integration.

Read the following documents:

1. BRD:
   `docs/[feature_folder]/1. BRD-[FeatureName].md`

2. PRD:
   `docs/[feature_folder]/2. PRD-[FeatureName]-iVendNext.md`

3. Project CLAUDE.md:
   `CLAUDE.md`

Save the document as:

`docs/[feature_folder]/3. TECH-[FeatureName]-SageX3-iVendNext.md`

---

# 1. Document Header

| Field            | Value                            |
| ---------------- | -------------------------------- |
| Customer         | [Customer Name]                  |
| Project          | [Project Code / Project Name]    |
| Source System    | Sage X3                          |
| Target System    | iVend Next                       |
| Platform         | iVend Next / Frappe              |
| Integration Type | Internal Connector / Bridge      |
| Prepared by      | CitiXsys Custom Development Team |
| Document Date    | [Today's Date]                   |
| Version          | 1.0                              |
| Status           | Draft / Approved                 |

---

# 2. Purpose

Explain:

* Why the connector is required.
* What business data is exchanged.
* Which system owns which data.
* How synchronization is controlled.
* How failed records are handled.
* How the connector operates inside iVend Next.

---

# 3. Architecture Overview

Describe the solution architecture.

The mandatory architecture is:

```text
                 MASTER DATA
        ┌─────────────────────────┐
        │        Sage X3          │
        │      REST Services      │
        └────────────┬────────────┘
                     │
                     │ Outbound
                     ▼
        ┌─────────────────────────┐
        │       iVend Next        │
        │                         │
        │   Internal Connector    │
        │                         │
        │   Frappe DocTypes       │
        │   Python Processing     │
        │   Background Jobs       │
        │   Mapping & Validation  │
        │   Sync Logs             │
        └─────────────────────────┘
                     │
                     │
                     │ TRANSACTIONS
                     ▼
        ┌─────────────────────────┐
        │        Sage X3          │
        │      SOAP Services      │
        └─────────────────────────┘
```

State explicitly:

### iVend Next

The connector must be implemented **inside iVend Next/Frappe**.

Do not introduce:

* Middleware
* External integration servers
* Third-party integration platforms
* Separate connector applications
* External API gateway
* External database

Use:

* Frappe DocTypes
* Python
* Frappe background jobs
* Frappe scheduler
* Internal documents
* Internal configuration
* Internal logging
* Internal retry mechanisms

---

# 4. Integration Direction

Create two separate integration pipelines.

## 4.1 Master Data — Sage X3 → iVend Next

Sage X3 is the source system.

Use Sage X3 REST services for outbound data retrieval.

Describe:

1. How records are identified.
2. How changes are detected.
3. How records are retrieved.
4. How records are mapped.
5. How records are validated.
6. How iVend Next records are created.
7. How existing records are updated.
8. How duplicates are prevented.
9. How failures are recorded.
10. How retry works.

---

## 4.2 Transaction Data — iVend Next → Sage X3

iVend Next is the source system.

Use Sage X3 SOAP services for inbound transaction processing.

Describe:

1. How eligible transactions are identified.
2. How transaction status is controlled.
3. How transaction data is prepared.
4. How business validation is performed.
5. How transaction data is mapped.
6. How the Sage X3 SOAP service is called.
7. How the response is interpreted.
8. How success is recorded.
9. How failure is recorded.
10. How retry/resubmission works.
11. How duplicate submission is prevented.

---

# 5. End-to-End Process

Create detailed process flows for:

### Process 1 — Master Data Synchronization

```text
Sage X3
  ↓
Identify eligible records
  ↓
Retrieve records
  ↓
Receive in iVend Next
  ↓
Validate
  ↓
Map
  ↓
Create / Update iVend Next document
  ↓
Record synchronization result
```

### Process 2 — Transaction Synchronization

```text
iVend Next Transaction
  ↓
Identify transaction for synchronization
  ↓
Validate
  ↓
Create synchronization record
  ↓
Prepare Sage X3 transaction
  ↓
Call Sage X3 SOAP service
  ↓
Process response
  ↓
Success / Failure
  ↓
Update synchronization status
```

---

# 6. Integration Object Catalogue

Create a complete table:

| ID | Business Object | Direction | Source | Target | Integration Method | Frequency |
| -- | --------------- | --------- | ------ | ------ | ------------------ | --------- |

Separate:

### Master Data

Sage X3 → iVend Next

### Transaction Data

iVend Next → Sage X3

Only include objects defined in the approved PRD.

---

# 7. Field-Level Mapping

Create mapping tables for every integration object.

Example:

| # | Sage X3 Field | Business Meaning | iVend Next Field | Business Meaning | Transformation | Mandatory |
| - | ------------- | ---------------- | ---------------- | ---------------- | -------------- | --------- |

Document:

* Direct mappings
* Default values
* Conditional mappings
* Code conversions
* Unit conversions
* Currency conversions
* Date conversions
* Status mappings
* Tax mappings
* Store mappings
* Warehouse mappings

Do not guess mappings.

If the mapping is unknown:

`TBD — Customer / Functional Confirmation Required`

---

# 8. iVend Next Internal Connector Design

Describe the internal connector components.

The connector should use Frappe-native implementation.

Potential DocTypes include:

### Integration Settings

Stores:

* Sage X3 connection configuration
* Company/folder information
* Synchronization settings
* Enabled integration objects
* Scheduling configuration

### Integration Object Configuration

Stores:

* Object name
* Direction
* Enabled/Disabled
* Synchronization frequency
* Last successful synchronization
* Next synchronization

### Master Data Sync Log

Stores:

* Source object
* Source identifier
* Target identifier
* Synchronization status
* Attempt count
* Error message
* Last attempt
* Completed date

### Transaction Sync Log

Stores:

* Transaction type
* Source document
* Sage X3 reference
* Status
* Attempt count
* Error details
* Response reference
* Submission date
* Completion date

Do not create these DocTypes automatically unless they are required by the approved design.

---

# 9. Processing Logic

Describe the Python processing logic at design level.

For each process specify:

### Input

What starts the process?

### Validation

What must be checked?

### Transformation

What data needs to be changed?

### Processing

What document is created or updated?

### Success

What status is recorded?

### Failure

What status and error information are recorded?

### Retry

When can the record be retried?

---

# 10. Master Data Processing Rules

For every master object document:

1. Source identification.
2. Change detection.
3. Record retrieval.
4. Field mapping.
5. Validation.
6. Duplicate detection.
7. Create logic.
8. Update logic.
9. Deactivation logic, if applicable.
10. Error handling.
11. Retry handling.

---

# 11. Transaction Processing Rules

For every transaction object document:

1. Source document.
2. Eligibility criteria.
3. Validation.
4. Mapping.
5. Payload construction.
6. Sage X3 SOAP submission.
7. Response handling.
8. Success handling.
9. Failure handling.
10. Retry.
11. Duplicate prevention.

---

# 12. Sage X3 REST Integration

Document the REST integration required for outbound master data.

For each REST operation specify:

| Object | Operation | Endpoint | Method | Request Parameters | Response | Pagination | Filtering |
| ------ | --------- | -------- | ------ | ------------------ | -------- | ---------- | --------- |

Document:

* Authentication
* Connection settings
* Timeout
* Pagination
* Filtering
* Incremental synchronization
* Response handling
* HTTP error handling
* Authentication failures
* Rate/connection failures

Do not invent endpoint names.

Use:

`TBD — Sage X3 endpoint confirmation required`

where required.

---

# 13. Sage X3 SOAP Integration

Document the SOAP integration required for inbound transactions.

For each operation specify:

| Object | SOAP Service | Operation | Request | Response | Success Criteria |
| ------ | ------------ | --------- | ------- | -------- | ---------------- |

Document:

* Authentication
* Service configuration
* Request construction
* Response processing
* Business errors
* Technical errors
* Timeout
* Retry
* Duplicate prevention

Do not invent SOAP service names or XML structures.

---

# 14. Synchronization Status Model

Define a common status lifecycle.

Example:

```text
Pending
   ↓
Processing
   ↓
Success
```

Failure:

```text
Pending
   ↓
Processing
   ↓
Failed
   ↓
Retry
   ↓
Processing
   ↓
Success
```

Define:

| Status | Meaning | Can Retry? |
| ------ | ------- | ---------- |

---

# 15. Error Handling

Define categories:

### Validation Error

Business data is invalid.

### Mapping Error

Required mapping is unavailable.

### Connection Error

Sage X3 cannot be reached.

### Authentication Error

Credentials/access are invalid.

### Sage X3 Business Error

Sage X3 rejects the transaction.

### Unexpected Error

Unexpected processing failure.

For each category specify:

* Where it is recorded.
* User visibility.
* Retry behaviour.
* Whether manual correction is required.

---

# 16. Retry and Resubmission

Define:

* Automatic retry conditions.
* Maximum retry attempts.
* Retry interval.
* Manual retry.
* Manual resubmission.
* When retry must be blocked.
* How duplicate submissions are prevented.

Do not invent retry values if they are not approved.

Use TBD where required.

---

# 17. Duplicate Prevention

Define duplicate prevention separately for:

### Master Data

Use a business-level unique identifier.

### Transactions

Use the source transaction/reference identifier.

Explain:

* How an existing record is identified.
* What happens when the same record is received again.
* How already successful transactions are prevented from being submitted again.

---

# 18. Scheduling and Background Processing

Describe how synchronization is scheduled inside iVend Next.

Use Frappe-native mechanisms.

Document:

* Scheduled jobs
* Background processing
* Object-specific schedules
* Processing batches
* Failure recovery
* Monitoring

Do not introduce external schedulers.

---

# 19. Security

Document:

### Sage X3 REST

* Authentication
* Credential storage
* HTTPS
* Access restrictions

### Sage X3 SOAP

* Authentication
* HTTPS
* Credential storage

### iVend Next

* Role permissions
* Integration configuration permissions
* Log access
* Retry/resubmission permissions

Never expose credentials in logs.

---

# 20. Logging and Monitoring

Define what must be logged.

Minimum information:

* Integration object
* Direction
* Source reference
* Target reference
* Processing status
* Timestamp
* Attempt count
* Error message
* Response/reference information
* Processing duration

Define what users can see and what is available for troubleshooting.

---

# 21. Performance Considerations

Define:

* Batch processing
* Pagination
* Incremental synchronization
* Large transaction handling
* Large master-data volumes
* Background processing
* Failure isolation

Do not prescribe implementation values unless approved.

---

# 22. Data Reconciliation

Define how the customer can verify:

* Number of records sent
* Number successfully processed
* Number failed
* Number pending
* Number retried
* Source/target references

Provide reconciliation scenarios for both directions.

---

# 23. Deployment Design

Describe:

1. Development
2. QA/UAT
3. Production

Include:

* Configuration migration
* Required master data
* Sage X3 service configuration
* iVend Next configuration
* Scheduler activation
* Permission configuration
* Initial synchronization

---

# 24. Rollback Considerations

Describe how to safely disable synchronization.

Include:

* Disable scheduled processing.
* Stop new synchronization.
* Identify pending records.
* Identify failed records.
* Preserve synchronization history.
* Correct configuration/data.
* Resume processing.

Do not delete synchronization history during rollback unless explicitly approved.

---

# 25. Open Questions

Create:

| ID | Question | Owner | Status |
| -- | -------- | ----- | ------ |

Any unknown API, mapping, business rule, or Sage X3 configuration must be recorded here instead of being guessed.

---

# 26. Technical Assumptions

Clearly identify assumptions concerning:

* Sage X3 REST availability
* Sage X3 SOAP availability
* Authentication
* Endpoint availability
* Required Sage X3 objects/services
* iVend Next permissions
* Scheduler availability
* Required mappings
* Data ownership

---

# 27. Technical Acceptance Criteria

Create criteria for:

* Master data synchronization
* Transaction synchronization
* Error handling
* Retry
* Duplicate prevention
* Logging
* Monitoring
* Security
* Performance
* Reconciliation

---

# STRICT ARCHITECTURE RULES

The following rules are mandatory:

1. **No third-party middleware.**
2. **No external connector application.**
3. **No separate integration server.**
4. **No external database for integration control data.**
5. The connector must be implemented inside **iVend Next/Frappe**.
6. Use **Frappe DocTypes** for integration configuration, control information, mappings, logs, and synchronization records where required.
7. Use **Python** for integration processing.
8. Use **Frappe background jobs/scheduler** for asynchronous processing.
9. Use **Sage X3 REST APIs for Sage X3 outbound master-data retrieval.**
10. Use **Sage X3 SOAP APIs for iVend Next transactional inbound processing into Sage X3.**
11. Do not invent Sage X3 endpoints, services, fields, or payloads.
12. Clearly distinguish between:

    * Business Process
    * Integration Process
    * Technical Implementation
13. Do not bypass Sage X3 services by directly accessing the Sage X3 database.
14. Do not bypass iVend Next/Frappe business documents by directly manipulating database tables.
15. All unknown technical information must be marked **TBD**.
16. The design must be detailed enough for a developer to implement without making architectural assumptions.

---

# 4. IMPL — Implementation Plan

I would also change your original implementation prompt significantly. The developer now needs to implement an **internal Frappe connector**, rather than a generic iVend add-on.

# IMPL — Implementation Plan

You are a **Senior Frappe/Python Developer and Sage X3 Integration Developer**.

Read:

1. BRD:
   `docs/[feature_folder]/1. BRD-[FeatureName].md`

2. PRD:
   `docs/[feature_folder]/2. PRD-[FeatureName]-iVendNext.md`

3. Process + Technical Design:
   `docs/[feature_folder]/3. TECH-[FeatureName]-SageX3-iVendNext.md`

4. Project conventions:
   `CLAUDE.md`

Create an implementation plan that a developer can follow step-by-step.

Save it as:

`docs/[feature_folder]/4. IMPL-[FeatureName]-SageX3-iVendNext.md`

---

# 1. Implementation Overview

Describe:

* What will be developed.
* Which integration objects are included.
* Master-data direction.
* Transaction direction.
* Frappe components involved.

---

# 2. Implementation Architecture

Use:

```text
Sage X3 REST
      ↓
Frappe Python Connector
      ↓
iVend Next DocTypes
```

and:

```text
iVend Next Transaction
      ↓
Frappe Python Connector
      ↓
Sage X3 SOAP
```

No external middleware is allowed.

---

# 3. Phase 1 — Frappe App Structure

Specify exact:

* App paths
* Module paths
* Python module names
* DocType directories
* Configuration files
* Scheduler configuration

For every file specify:

| File | Action | Purpose |
| ---- | ------ | ------- |

---

# 4. Phase 2 — Integration Configuration

Define the required DocTypes.

For each DocType specify:

* DocType name
* Purpose
* Fields
* Field types
* Mandatory fields
* Default values
* Permissions
* Naming strategy
* Validation

Example:

### Integration Settings

| Field | Type | Mandatory | Purpose |
| ----- | ---- | --------- | ------- |

Do not create unnecessary DocTypes.

---

# 5. Phase 3 — Master Data Connector

Implement Sage X3 → iVend Next.

For every master-data object provide:

### Object

`[Object Name]`

### Python File

`[exact/path.py]`

### Classes

`[ClassName]`

### Methods

`[method_name()]`

### Processing Flow

1. Load configuration.
2. Determine synchronization window.
3. Call Sage X3 REST service.
4. Handle pagination.
5. Validate response.
6. Map fields.
7. Validate business rules.
8. Find existing iVend Next record.
9. Create/update record.
10. Commit safely.
11. Record synchronization result.
12. Continue processing remaining records.

---

# 6. Phase 4 — Transaction Connector

Implement iVend Next → Sage X3.

For every transaction specify:

### Trigger

What causes the transaction to become eligible?

### Source DocType

`[DocType]`

### Connector File

`[exact/path.py]`

### Class

`[ClassName]`

### Methods

`[method_name()]`

### Processing

1. Identify eligible transaction.
2. Validate transaction.
3. Create synchronization record.
4. Build transaction data.
5. Map fields.
6. Validate required values.
7. Construct SOAP request.
8. Call Sage X3.
9. Process response.
10. Update source transaction status.
11. Record success/failure.
12. Prevent duplicate submission.

---

# 7. Phase 5 — Field Mapping

Create implementation-level mapping tables.

| Source Field | Target Field | Python Mapping | Transformation | Validation |
| ------------ | ------------ | -------------- | -------------- | ---------- |

Document every transformation explicitly.

Do not guess missing mappings.

---

# 8. Phase 6 — REST Client

Implement the Sage X3 REST client.

Define:

* File path
* Class
* Methods
* Authentication
* Session handling
* Timeout
* Pagination
* Filtering
* Response validation
* HTTP error handling
* Retry strategy

Example structure:

```text
sage_x3/
    clients/
        rest_client.py
        soap_client.py
    mappers/
    services/
    validators/
```

Adapt the structure to the existing project conventions in `CLAUDE.md`.

Do not blindly use this example structure if the project already has an established architecture.

---

# 9. Phase 7 — SOAP Client

Implement the Sage X3 SOAP client.

Specify:

* File path
* Class name
* Methods
* Authentication
* Request construction
* XML generation
* Response parsing
* Business error handling
* Technical error handling
* Timeout
* Retry
* Duplicate protection

Use the actual Sage X3 SOAP definition provided by the project.

If unavailable, mark the required information as:

`TBD — Sage X3 SOAP service definition required`

---

# 10. Phase 8 — Mapping and Validation

Create reusable mapping and validation components.

Define:

* Required-field validation
* Code conversion
* Status conversion
* Date conversion
* Currency handling
* Unit handling
* Store/warehouse mapping
* Tax mapping
* Customer/product mapping

Every validation must have a clear error message.

---

# 11. Phase 9 — Synchronization Status

Implement status management.

Minimum statuses:

```text
Pending
Processing
Success
Failed
Retry
```

Define:

* Allowed transitions
* Who can retry
* Automatic retry
* Manual retry
* Maximum attempts
* Duplicate prevention

---

# 12. Phase 10 — Error Handling

Create centralized error handling.

Define error constants.

Example:

```python
ERR_SAGE_CONNECTION_FAILED
ERR_SAGE_AUTHENTICATION_FAILED
ERR_INVALID_RESPONSE
ERR_REQUIRED_FIELD_MISSING
ERR_MAPPING_NOT_FOUND
ERR_DUPLICATE_TRANSACTION
ERR_SAGE_BUSINESS_ERROR
```

Use project coding conventions from `CLAUDE.md`.

Do not duplicate error-message text throughout the code.

---

# 13. Phase 11 — Background Jobs

Implement Frappe-native background processing.

Define:

* Scheduler events
* Job functions
* Queue selection
* Batch processing
* Failure handling
* Retry handling

Do not introduce external schedulers.

---

# 14. Phase 12 — Logging

Implement integration logging using Frappe DocTypes.

Record:

* Object
* Direction
* Source ID
* Target ID
* Status
* Attempt
* Timestamp
* Error
* Response reference
* Processing duration

Do not log:

* Passwords
* Authentication tokens
* Sensitive credentials

---

# 15. Phase 13 — Desk UI

If the PRD requires user interfaces, define:

* DocTypes
* List views
* Filters
* Buttons
* Actions
* Permissions
* Retry actions
* Resubmit actions
* Error display
* Synchronization dashboards

---

# 16. Phase 14 — Automated Tests

Create automated tests for:

* Mapping
* Validation
* Duplicate detection
* Status transitions
* REST response processing
* SOAP response processing
* Error handling
* Retry logic

---

# 17. Testing

## Group A — Automated Unit Tests

Create:

| Test ID | Component | Test | Expected Result |
| ------- | --------- | ---- | --------------- |

Use IDs:

`UT-01`, `UT-02`, etc.

---

## Group B — Sage X3 REST Tests

Manual integration tests.

Provide curl examples only using confirmed endpoints.

Example structure:

```bash
curl -X GET "<CONFIRMED_SAGE_X3_REST_ENDPOINT>"
```

Never invent an endpoint.

Test:

* Authentication
* Successful retrieval
* Pagination
* Filtering
* Empty response
* Invalid response
* Authentication failure
* Connection failure

---

## Group C — Desk UI Tests

Test:

* Configuration
* Sync monitoring
* Failed records
* Retry
* Resubmission
* Permissions
* Filters

---

## Group D — Integration Tests

Test:

* Sage X3 → iVend Next master data
* iVend Next → Sage X3 transactions
* Create
* Update
* Failure
* Retry
* Duplicate prevention
* Reconciliation

---

## Group E — Offline Tests

Test connector behaviour when Sage X3 is unavailable.

Verify:

* Transactions remain safe.
* Failed records are retained.
* No data is lost.
* Processing resumes after connectivity returns.

---

## Group F — Edge Cases

Create:

| ID | Scenario | Expected Result |
| -- | -------- | --------------- |

Use IDs:

`EC-01`, `EC-02`, etc.

Include:

* Duplicate master data
* Duplicate transaction
* Missing mandatory mapping
* Invalid product
* Invalid customer
* Invalid currency
* Invalid tax
* Partial response
* Empty response
* Large batch
* Network interruption
* Timeout
* Authentication failure
* Sage X3 business rejection
* Retry exhaustion

---

# 18. Deployment Checklist

Create a checklist covering:

### Development

* [ ] Code completed
* [ ] Unit tests passed
* [ ] Code review completed

### QA

* [ ] Configuration created
* [ ] Sage X3 connectivity verified
* [ ] Master data tested
* [ ] Transactions tested
* [ ] Error handling tested
* [ ] Retry tested
* [ ] UAT completed

### Production

* [ ] Backup completed
* [ ] App deployed
* [ ] Configuration verified
* [ ] Sage X3 credentials configured securely
* [ ] Scheduler enabled
* [ ] Permissions configured
* [ ] Initial synchronization validated
* [ ] Monitoring enabled

---

# 19. Rollback Plan

Define:

1. Disable integration scheduling.
2. Stop new synchronization jobs.
3. Allow active jobs to finish safely where possible.
4. Identify pending and failed records.
5. Preserve synchronization logs.
6. Revert code/configuration if required.
7. Validate iVend Next.
8. Validate Sage X3.
9. Re-enable synchronization only after approval.

Do not delete synchronization records as part of rollback.

---

# 20. Developer Acceptance Criteria

The implementation is complete only when:

* All approved master objects synchronize from Sage X3 to iVend Next.
* All approved transaction objects synchronize from iVend Next to Sage X3.
* REST integration works for approved Sage X3 outbound services.
* SOAP integration works for approved Sage X3 inbound services.
* No external middleware is used.
* Integration processing is implemented inside iVend Next/Frappe.
* Configuration is managed through iVend Next.
* Synchronization status is traceable.
* Errors are recorded.
* Retry works according to the approved design.
* Duplicate transactions are prevented.
* Unit and integration tests pass.
* UAT is completed.
* Deployment checklist is completed.

---

## Recommended final documentation structure

For your **Sage X3 ↔ iVend Next Connector**, I would use this structure:

```text
docs/
└── sage-x3-ivendnext-integration/
    │
    ├── 1. BRD-SageX3-iVendNext.md
    │
    ├── 2. PRD-SageX3-iVendNext.md
    │
    ├── 3. TECH-SageX3-iVendNext.md
    │
    └── 4. IMPL-SageX3-iVendNext.md
```

And the responsibility of each document should be very clear:

| Document | Main Question                                      |
| -------- | -------------------------------------------------- |
| **BRD**  | **What does the customer need?**                   |
| **PRD**  | **What should the product do?**                    |
| **TECH** | **How will Sage X3 and iVend Next work together?** |
| **IMPL** | **Exactly how will the developer build it?**       |

### Most important architectural distinction

For this particular project, I would **not** describe it as an "iVend Next API integration."

Instead, describe it as an **Internal Frappe Integration Connector**:

```text
                 ┌─────────────────────┐
                 │       Sage X3        │
                 │                     │
                 │ REST       SOAP     │
                 └───┬───────────▲─────┘
                     │           │
             Master  │           │ Transactions
              Data   │           │
                     ▼           │
        ┌───────────────────────────────┐
        │          iVend Next           │
        │            Frappe             │
        │                               │
        │  ┌─────────────────────────┐  │
        │  │ Internal X3 Connector   │  │
        │  │                         │  │
        │  │ Python Services         │  │
        │  │ REST Client             │  │
        │  │ SOAP Client             │  │
        │  │ Mappers                 │  │
        │  │ Validators              │  │
        │  │ Retry Manager           │  │
        │  └────────────┬────────────┘  │
        │               │               │
        │  ┌────────────▼────────────┐  │
        │  │ Frappe DocTypes         │  │
        │  │                         │  │
        │  │ Configuration           │  │
        │  │ Mapping                 │  │
        │  │ Sync Logs               │  │
        │  │ Transaction Queue       │  │
        │  └─────────────────────────┘  │
        │                               │
        │  Scheduler / Background Jobs  │
        └───────────────────────────────┘
```

This gives you a clean separation between **customer requirements, functional behaviour, integration architecture, and actual Frappe implementation**, which will be particularly useful when you later generate the field-level Sage X3 ↔ iVend Next mapping workbook and the developer tasks.
