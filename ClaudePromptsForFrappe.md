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

---

## Prompt 2: Create a New Frappe Application

```
You are a Frappe Framework expert developer.

Create a new Frappe application named "Retail Integration Suite".

Requirements:
1. Create a custom Frappe app using bench commands.
2. Explain the folder structure of the generated app.
3. Configure hooks.py.
4. Create a module named "Retail Integration".
5. Add the app to an existing Frappe site.
6. Explain how to install, migrate, and verify the app.
7. Follow Frappe coding standards.

Provide:
- Commands to create the app
- Folder structure explanation
- Important files and their purpose
- Sample hooks.py configuration
- Installation steps
```

---

# Prompt 3: Create Custom DocType

```
You are a senior Frappe ERPNext developer.

Create a custom DocType called "Integration Configuration".

Requirements:

DocType:
Name:
Integration Configuration

Fields:
1. Integration Name
   - Type: Data
   - Mandatory: Yes

2. Integration Type
   - Type: Select
   - Options:
     - REST API
     - SOAP API
     - Database
     - File

3. API URL
   - Type: Data

4. Username
   - Type: Data

5. Password
   - Type: Password

6. Active
   - Type: Check

7. Last Sync Date
   - Type: Datetime


Functional Requirements:
- Make Integration Name unique.
- Add validation to ensure API URL is mandatory when Integration Type is REST API.
- Add a button "Test Connection".
- Store connection logs.

Create:
1. DocType JSON definition.
2. Python controller file.
3. Client Script.
4. Validation logic.
5. Test Connection button implementation.
6. Explain each file location.
```

---

# Prompt 4: Create Child Table DocType

```
Create a Frappe Child Table DocType named:

"Integration Field Mapping"

Purpose:
Store field mapping between external system and Frappe.


Fields:

External Field Name
- Data
- Mandatory

Frappe Field Name
- Data
- Mandatory

Data Type
- Select
  Options:
    String
    Integer
    Date
    Boolean

Default Value
- Data

Transformation Rule
- Small Text


Create:
1. Child DocType definition.
2. Add this child table into "Integration Configuration".
3. Provide JSON configuration.
4. Explain how child tables work in Frappe.
```

---

# Prompt 5: Frappe Client Events

```
You are a Frappe JavaScript developer.

Create Client Script events for DocType:

Integration Configuration


Implement:

1. refresh event:
   - Add custom button "Test Connection".

2. validate event:
   - Check API URL format.

3. API call:
   - Call server-side method when button is clicked.

4. Display success/failure messages using frappe.msgprint.


Provide:
- Complete client script.
- Explanation of each event.
- Best practices for frappe.ui.form.
```

---

# Prompt 6: Frappe Server Events

```
You are a Frappe backend developer.

Implement Server Side Events for:

DocType:
Integration Configuration


Events required:

1. before_insert
   - Generate Integration ID automatically.

2. validate
   - Validate mandatory fields.

3. before_save
   - Encrypt sensitive information.

4. after_insert
   - Create Integration Log record.

5. on_update
   - Track configuration changes.


Provide:
- Python controller code.
- hooks.py configuration.
- Explanation of lifecycle events.
```

---

# Prompt 7: Override Existing DocType Events

```
You are an expert Frappe Framework developer.

Explain and implement event overriding.

Scenario:

ERPNext has an existing DocType:

Customer


Requirement:

Whenever Customer is saved:

1. Validate customer mobile number.
2. Call an external CRM API.
3. Create synchronization log.


Do not modify ERPNext core files.


Implement using:

hooks.py

doc_events


Provide:

1. hooks.py configuration.
2. Python override file.
3. Validation function.
4. API calling example.
5. Error handling.
6. Logging mechanism.


Explain why overriding through hooks is preferred over modifying core code.
```

---

# Prompt 8: Override Standard Frappe Method

```
You are a Frappe Framework expert.

Explain how to override standard Python methods.

Scenario:

Override Sales Invoice submit process.


Requirement:

Before submitting Sales Invoice:

- Check customer credit limit.
- Prevent submission if limit exceeds.
- Send notification.


Implement using:

override_whitelisted_methods


Provide:
1. hooks.py configuration.
2. Custom Python method.
3. Original method calling pattern.
4. Error handling.
5. Best practices.
```

---

# Prompt 9: Create Integration App Workflow

```
Design a complete Frappe application workflow.

Application:
Retail Integration Suite


Business Process:

1. Create Integration Configuration.
2. Define field mapping.
3. Schedule automatic synchronization.
4. Fetch data from external ERP.
5. Transform data.
6. Create Frappe documents.
7. Maintain integration logs.


Create:

- DocTypes required.
- Workflow diagram.
- Server scripts.
- Scheduled jobs.
- API endpoints.
- Error handling approach.

Use Frappe Framework best practices.
```

---

# Prompt 10: Scheduled Event / Background Job

```
Create a Frappe scheduled job.

Requirement:

Every 15 minutes:

1. Read active Integration Configuration.
2. Call external API.
3. Process response.
4. Update synchronization status.
5. Create Integration Log.


Implement:

hooks.py

scheduler_events

Python method

Background job using frappe.enqueue()


Explain:
- Scheduler flow.
- Queue processing.
- Error handling.
```

---

# Prompt 11: Complete Enterprise-Level Frappe App

```
Act as a senior Frappe ERP architect.

Create an enterprise application called:

"ERP Integration Hub"


Modules:

1. Integration Configuration
2. API Credentials
3. Field Mapping
4. Sync Queue
5. Integration Logs
6. Error Monitoring


Features:

- Role based permissions
- Workflow approval
- REST API integration
- Scheduled synchronization
- Background jobs
- Event overriding
- Audit tracking


Generate:

1. Application architecture.
2. DocType design.
3. Python backend.
4. JavaScript frontend.
5. Hooks configuration.
6. API design.
7. Deployment steps.
8. Testing strategy.


Follow production-level Frappe standards.
```

---

These prompts can be used as a **2–3 hour Frappe AI training exercise** because they progress from **App → DocType → Events → Overrides → Integration Architecture**.
