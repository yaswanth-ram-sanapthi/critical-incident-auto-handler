# Setup Guide — Critical Incident Auto-Handler

## 1. Overview

This guide describes how to set up and configure the Critical Incident Auto-Handler application in a ServiceNow Personal Developer Instance (PDI).

The application automates critical incident routing, manager notifications, assignment monitoring, timeout-based escalation, incident updates, and audit logging through Flow Designer.

## 2. Prerequisites

Before starting, ensure you have:

- Access to a ServiceNow development instance.
- Permission to create scoped applications and custom tables.
- Access to Flow Designer.
- Permission to configure users, groups, and email notifications.
- A valid email address for testing notifications.

The available permissions and features may vary depending on the instance and configuration.

## 3. Create the Scoped Application

1. Sign in to your ServiceNow instance.
2. Open the application development area.
3. Create a scoped application named `Critical Incident Auto-Handler`.
4. Confirm that the application scope is selected when creating its components.
5. Save the application and verify that you can access its tables and Flow Designer configuration.

## 4. Create the Main Incident Table

Create the custom table with the label:

`Critical Incident Management`

The existing project table uses the technical name:

`x_2107379_critic_0_critical_incident_management`

Configure the table to extend the Task table, matching the existing implementation.

### 4.1 Configure Custom Fields

Create and configure these fields:

| Field Label       | Field Name            | Configuration                         |
| ----------------- | --------------------- | ------------------------------------- |
| Caller            | `caller`              | Reference to User (`sys_user`)        |
| Assignment group  | `assignment_group`    | Reference to Group (`sys_user_group`) |
| Category          | `u_category`          | Choice field                          |
| Incident Type     | `u_incident_type`     | Choice field                          |
| Escalation Status | `u_escalation_status` | Choice field                          |

Configure the category choices:

- Network
- Hardware
- Software
- Database
- Security

Configure the incident type choices:

- Service outage
- Application issue
- Access issue
- Hardware issue

Configure the escalation status choices:

- Not Escalated — default
- Escalated

The Task extension provides inherited fields such as Number, Priority, Assigned to, State, Short description, Description, and Work notes.

## 5. Create the Audit Table

Create the custom table:

`Critical Incident Flow Audit`

Configure fields for the following information:

- Auto-generated audit number
- Reference to the related incident
- Workflow action
- Outcome status
- Execution details
- Execution timestamp

The project uses audit statuses including Completed, Escalated, and Failed.

Use the existing project's field definitions as the source of truth when recreating this table. Verify field names, data types, reference targets, and choice values in the ServiceNow dictionary rather than assuming that labels and internal names are identical.

## 6. Configure Support Groups

Create or verify the following support groups:

| Incident Category | Support Group    |
| ----------------- | ---------------- |
| Network           | Network Support  |
| Hardware          | Hardware Support |
| Software          | Software Support |
| Database          | Database Support |
| Security          | Security Support |

Ensure that the relevant manager information is configured and that the notification recipient has a valid email address.

Use test users and sample data when possible.

## 7. Configure Flow Designer

Create a flow named:

`Critical Incident Auto-Handler`

Configure the flow to match the following logic.

### 7.1 Trigger

- Trigger type: Record Created
- Table: Critical Incident Management
- Condition: Priority is `1 - Critical`

### 7.2 Category-Based Routing

Add conditional branches for the supported incident categories.

Each branch should update the incident's Assignment group to the corresponding support group.

Verify that each Update Record action receives the correct incident record data pill.

### 7.3 Initial Notification

Add a Send Email action to notify the relevant support group manager after routing.

Verify the recipient configuration and test email behavior in the instance.

### 7.4 Assignment Wait and Timeout

Configure a Wait For Condition action to wait for Assigned to to become populated, with a timeout enabled.

The intended response window is 30 minutes. A shorter timeout can be used during development testing.

### 7.5 Post-Wait Condition

After the wait, evaluate the latest incident state to determine whether Assigned to is empty.

Ensure that the condition uses current record data rather than relying solely on the original trigger values.

### 7.6 Escalation Path

If the incident remains unassigned after the timeout:

1. Send an escalation notification.
2. Set Escalation Status to `Escalated`.
3. Add a suitable work note.
4. Create an audit record with the appropriate action, status, details, and execution time.

### 7.7 Normal Completion Path

If the incident is assigned within the response window, avoid the escalation actions and follow the normal completion path.

Verify that the correct audit outcome is recorded according to the configured flow.

## 8. Activate and Test the Flow

1. Review the trigger, routing branches, email actions, wait condition, and audit actions.
2. Save and activate the flow.
3. Create a test incident with Priority `1 - Critical`.
4. Select a category and verify the assignment group.
5. Check the Flow Designer execution details.
6. Verify the initial notification.
7. Test both assignment-before-timeout and unassigned-timeout scenarios.
8. Inspect the final incident record and audit table.

Do not consider the setup fully validated until both paths have been tested successfully.

## 9. Verification Checklist

- [ ] The scoped application and required tables exist.
- [ ] Incident fields and choice values are configured.
- [ ] The audit table references the correct incident table.
- [ ] All five support groups are configured.
- [ ] Manager email recipients are valid.
- [ ] The flow triggers for Priority 1 – Critical incidents.
- [ ] Category-based routing works for all supported categories.
- [ ] Initial notification behavior is verified.
- [ ] Assignment before timeout avoids escalation.
- [ ] An unassigned incident follows the escalation path after timeout.
- [ ] Escalation status and work notes are updated.
- [ ] Audit records are created with the expected values.

## 10. Screenshot References

The following screenshots from the repository can help verify the setup.

### Application Setup

- Incident table: `Screenshots/application-setup/Incident-Table.png`
- Incident form: `Screenshots/application-setup/Incident-Form.png`
- Audit table: `Screenshots/application-setup/Audit-Table.png`
- Audit form: `Screenshots/application-setup/Audit-Form.png`

### Flow Design and Testing

- Workflow design: `Screenshots/flow-design-testing/Work-Flow-1.png`
- Workflow logic: `Screenshots/flow-design-testing/Work-Flow-2.png`
- Flow testing: `Screenshots/flow-design-testing/Flow-Testing-1.png`
- Escalation testing: `Screenshots/flow-design-testing/Flow-Testing-2.png`

### Notifications and Results

- Initial incident email: `Screenshots/results-notifications/Incident-Email.png`
- Escalation email: `Screenshots/results-notifications/Escalation-Email.png`
- Escalated incident: `Screenshots/results-notifications/Escalated-Incident.png`
- Escalation audit record: `Screenshots/results-notifications/Escalation-Audit-Record.png`

## 11. Deployment Considerations

This guide describes manual recreation in a development instance.

For migration to another instance, use a supported ServiceNow application or update-set export process where appropriate. Confirm that the required tables, fields, flow configuration, reference data, users, groups, and notification settings are available and correctly configured in the target instance.

Never include passwords, access tokens, or private instance credentials in the repository.

## 12. Summary

Following this guide helps recreate the Critical Incident Auto-Handler and validate its main workflow components. Successful setup should be confirmed by executing the test scenarios documented in `docs/test-cases.md`.
