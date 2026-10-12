# Critical Incident Auto-Handler

A ServiceNow application that automates critical incident routing, manager notifications, timeout-based escalation, and audit logging using Flow Designer and custom tables.

## 📌 Project Overview

Critical incidents require rapid assignment and timely action. Manual routing and follow-up can delay incident resolution and make it difficult to track escalation history.

The **Critical Incident Auto-Handler** automates the initial response process by routing critical incidents to the appropriate support group, notifying the responsible manager, monitoring assignment status, and escalating incidents that remain unassigned after a configured timeout.

## 🎯 Objectives

- Automate routing of critical incidents by category.
- Notify the relevant support group manager.
- Monitor whether an incident is assigned within the response window.
- Escalate incidents that remain unassigned.
- Update incident escalation status and work notes.
- Maintain audit records of workflow outcomes.

## 🛠️ Technology Stack

- **Platform:** ServiceNow
- **Automation:** Flow Designer
- **Data Management:** Custom ServiceNow tables
- **Notifications:** Email actions
- **Logic:** Conditional routing, wait conditions, record lookups, and record updates

## ✨ Key Features

### 1. Priority-Based Trigger
The flow starts when a new Critical Incident Management record is created with Priority 1 – Critical.

### 2. Automatic Assignment Group Routing
The incident category determines the appropriate support group:

| Category | Support Group |
|---|---|
| Network | Network Support |
| Hardware | Hardware Support |
| Software | Software Support |
| Database | Database Support |
| Security | Security Support |

### 3. Manager Notifications
An email notification informs the relevant support group manager when a critical incident is routed.

### 4. Timeout-Based Escalation
The flow waits for the configured response window and checks whether the incident has been assigned to a person. If the incident remains unassigned, the escalation path sends a notification and updates the incident record.

### 5. Incident Updates
The escalation path sets the Escalation Status to `Escalated` and adds a work note explaining why the incident was escalated.

### 6. Audit Logging
The Critical Incident Flow Audit table records workflow outcomes, including completed handling and escalated incidents.

## 🏗️ Application Components

### Critical Incident Management

The main custom table stores incident details and workflow information.

Custom fields include:

- Caller
- Assignment group
- Category
- Incident Type
- Escalation Status

The table also uses inherited task fields, including Number, Priority, Assigned to, Short description, Description, State, and Work notes.

### Critical Incident Flow Audit

The audit table stores details about workflow actions and their outcomes, including the related incident, action, status, execution details, and execution time.

## 🔄 Workflow

1. A critical incident is created.
2. The flow checks whether Priority is 1 – Critical.
3. The incident category determines the assignment group.
4. The responsible manager receives an email notification.
5. The flow waits for assignment or the configured timeout.
6. The latest incident state is checked.
7. If Assigned to is empty, an escalation email is sent and the incident is updated.
8. An audit record is created to capture the outcome.

## 🧪 Testing

The workflow was tested in a ServiceNow Personal Developer Instance.

| Test Scenario | Expected Result |
|---|---|
| Critical Hardware incident | Routed to Hardware Support |
| Critical Network incident | Routed to Network Support |
| Critical incident remains unassigned | Escalation path executes after timeout |
| Escalation path executes | Status and work notes are updated |
| Workflow completes | Audit record is created |
| Non-critical incident | Critical-incident flow does not trigger |

**Note:** The final test evidence should be checked against actual execution logs and record values before treating every scenario as verified.

## 📸 Screenshots

Project screenshots will be added to the `screenshots/` directory.

Examples:

- Flow Designer overview
- Incident form
- Category-based routing
- Escalation email
- Updated incident record
- Audit record
- Successful flow execution

## 🚀 Setup and Usage

This project was developed in a ServiceNow Personal Developer Instance.

To reproduce it:

1. Set up a ServiceNow development instance.
2. Create a scoped application named `Critical Incident Auto-Handler`.
3. Create the Critical Incident Management and Critical Incident Flow Audit tables.
4. Configure the required fields and support groups.
5. Build the Flow Designer trigger, routing branches, notifications, wait condition, escalation logic, and audit actions.
6. Test using sample critical incidents and review the execution details.

Where supported, use an appropriate ServiceNow application or update-set export to move the configuration into another instance. Import compatibility depends on the exported components and target instance.

## 🔐 Security

- Use sample data when demonstrating the application.
- Do not commit credentials, access tokens, or private instance information.
- Remove personal information from screenshots before publishing.

## 👨‍💻 Author

**Sanapathi Yaswanth Ram**

Computer Science and System Engineering Student

## 📄 License

This project is available for educational and portfolio demonstration purposes. Add a formal open-source license if you intend to grant specific reuse and distribution rights.
