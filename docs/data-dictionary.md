# Data Dictionary — Critical Incident Auto-Handler

## 1. Overview

The Critical Incident Auto-Handler uses two custom ServiceNow tables:

1. **Critical Incident Management** — stores incident details, assignment information, and escalation status.
2. **Critical Incident Flow Audit** — stores workflow actions and their outcomes.

The main incident table extends the Task table and uses inherited fields.

## 2. Critical Incident Management

**Table label:** Critical Incident Management

**Technical name:** `x_2107379_critic_0_critical_incident_management`

This table stores incident information and supports automated routing and escalation.

### 2.1 Custom Fields

| Field Label | Field Name | Type / Configuration | Purpose |
|---|---|---|---|
| Caller | `caller` | Reference — User (`sys_user`) | Identifies the person reporting the incident |
| Assignment group | `assignment_group` | Reference — Group (`sys_user_group`) | Identifies the responsible support group |
| Category | `u_category` | Choice | Determines category-based routing |
| Incident Type | `u_incident_type` | Choice | Classifies the incident |
| Escalation Status | `u_escalation_status` | Choice | Tracks escalation status |

### 2.2 Category Choices

| Choice | Purpose |
|---|---|
| Network | Network-related incidents |
| Hardware | Hardware-related incidents |
| Software | Software-related incidents |
| Database | Database-related incidents |
| Security | Security-related incidents |

### 2.3 Incident Type Choices

| Choice | Purpose |
|---|---|
| Service outage | A service is unavailable |
| Application issue | An application is malfunctioning |
| Access issue | A user has an access-related problem |
| Hardware issue | A hardware component has a problem |

### 2.4 Escalation Status Choices

| Choice | Purpose |
|---|---|
| Not Escalated | Default status before escalation |
| Escalated | Indicates the escalation path updated the incident |

### 2.5 Inherited Task Fields

The table extends Task and uses inherited fields, including:

| Field | Purpose |
|---|---|
| Number | Identifies the incident record |
| Priority | Determines whether the critical-incident flow is triggered |
| Assigned to | Identifies the individual handling the incident |
| Configuration item | References the affected configuration item |
| Active | Indicates whether the record is active |
| State | Tracks the record's current state |
| Short description | Provides a brief incident summary |
| Description | Stores detailed incident information |
| Work notes | Records internal updates and escalation details |
| Parent | Links to a parent task when applicable |

## 3. Critical Incident Flow Audit

The Critical Incident Flow Audit table records workflow actions and outcomes for traceability.

**Table label:** Critical Incident Flow Audit

### 3.1 Fields

| Field Label | Field Name | Type / Configuration | Purpose |
|---|---|---|---|
| Number | Verify in ServiceNow | Auto-number | Identifies the audit record |
| Incident | Verify in ServiceNow | Reference to incident table | Links the audit record to an incident |
| Action | Verify in ServiceNow | Verify actual field type | Describes the workflow action |
| Status | Verify in ServiceNow | Choice | Records the workflow outcome |
| Details | Verify in ServiceNow | Verify actual field type | Explains the outcome or reason |
| Executed At | Verify in ServiceNow | Date/Time | Records when the action was logged |

**Note:** Confirm the internal field names and field types using the ServiceNow dictionary before replacing these placeholders. Field labels and internal names may differ.

### 3.2 Audit Status Choices

| Status | Meaning |
|---|---|
| Completed | The recorded workflow handling completed successfully |
| Escalated | The incident followed the escalation path |
| Failed | An action failed, where failure logging is configured |

### 3.3 Example Audit Entry

| Field | Example |
|---|---|
| Action | Critical Incident Escalated |
| Status | Escalated |
| Details | Incident remained unassigned timeout |
| Executed At | Timestamp recorded by the application |

This example represents an escalation record. Actual values depend on the incident and flow execution.

## 4. Table Relationships

The application uses the following relationships:

- **Incident → Assignment group:** The incident references a ServiceNow support group.
- **Incident → Assigned to:** The incident references the individual assigned to handle it.
- **Audit → Incident:** The audit record references the incident associated with the workflow action.

These references connect incident handling with its recorded workflow outcomes.

## 5. Data Flow

1. A critical incident is created.
2. The flow evaluates its priority and category.
3. The flow updates the assignment group.
4. The flow monitors the incident's assignment status.
5. If the incident remains unassigned after the timeout, the escalation path updates the incident.
6. An audit record captures the workflow outcome.

## 6. Screenshot References

Screenshots are maintained in the repository's `Screenshots/` directory rather than embedded in this document.

- Incident table structure: `Screenshots/application-setup/Incident-Table.png`
- Incident form: `Screenshots/application-setup/Incident-Form.png`
- Audit table structure: `Screenshots/application-setup/Audit-Table.png`
- Audit form: `Screenshots/application-setup/Audit-Form.png`

## 7. Summary

The data model separates incident management from workflow audit history. Reference fields connect related records, while custom choice fields support category-based routing and escalation tracking.
