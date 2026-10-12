# System Architecture — Critical Incident Auto-Handler

## 1. Overview

The Critical Incident Auto-Handler is a scoped ServiceNow application that automates the initial handling of critical incidents. It uses Flow Designer to route incidents to support groups, notify the responsible manager, monitor assignment status, and escalate incidents that remain unassigned after a configured timeout.

## 2. Architecture Components

The application consists of the following components:

- **Critical Incident Management:** Custom table that stores incident information and workflow status.
- **Flow Designer:** Executes the automated routing, notification, waiting, escalation, and audit steps.
- **Support Groups:** Provide category-specific incident ownership.
- **Manager Notifications:** Email actions notify the relevant support group manager.
- **Critical Incident Flow Audit:** Custom table that records workflow outcomes and execution details.

## 3. Workflow Trigger

The flow starts when a record is created in the Critical Incident Management table with Priority set to **1 – Critical**.

Non-critical incidents do not meet this trigger condition and therefore should not start this flow.

## 4. Category-Based Routing

The flow evaluates the incident category and selects the corresponding assignment group.

| Incident Category | Assignment Group |
|---|---|
| Network | Network Support |
| Hardware | Hardware Support |
| Software | Software Support |
| Database | Database Support |
| Security | Security Support |

The selected assignment group is written to the incident record.

## 5. Manager Notification

After routing, the flow sends an email notification to the relevant support group manager. The notification informs the manager about the critical incident and supports timely response.

Successful delivery depends on the recipient being configured correctly and having a valid email address.

## 6. Assignment Monitoring and Timeout

The flow waits for the incident to be assigned to an individual or for the configured timeout to expire.

The timeout is configurable in Flow Designer. The intended production response window is 30 minutes; a shorter timeout may be used during testing.

After the wait step, the flow must evaluate the **latest incident state** to determine whether the Assigned to field is empty.

## 7. Escalation Handling

If the incident remains unassigned when the timeout expires, the escalation path:

1. Sends an escalation notification.
2. Updates Escalation Status to `Escalated`.
3. Adds a work note explaining the escalation.
4. Creates an audit record describing the escalation outcome.

If the incident has been assigned within the response window, the escalation path should not execute, and the workflow should record the appropriate completion outcome.

## 8. Audit Logging

The Critical Incident Flow Audit table captures workflow outcomes and supporting details.

The audit record includes the related incident, action, status, execution details, and execution time. This provides a record of the workflow's handling and escalation activity.

## 9. Architecture Diagram

The logical workflow is:

```text
Critical Incident Created
          |
          v
Priority = 1 - Critical?
          |
          v
Route by Incident Category
          |
          v
Set Assignment Group
          |
          v
Notify Support Group Manager
          |
          v
Wait for Assignment or Timeout
          |
          v
Check Latest Incident State
          |
          +-----------------------+
          |                       |
    Assigned to set         Unassigned
          |                       |
          v                       v
   Normal Completion       Send Escalation Email
          |                       |
          v                       v
   Create Audit Record     Update Incident
                                  |
                                  v
                           Create Audit Record
```

**Note:** This diagram represents the intended workflow. Confirm the latest-record check and the assigned-before-timeout path against actual Flow Designer execution results before treating both paths as verified.

## 10. Screenshots

The following repository screenshots support this architecture:

- `Screenshots/flow-design-testing/Work-Flow-1.png`
- `Screenshots/flow-design-testing/Work-Flow-2.png`
- `Screenshots/flow-design-testing/Flow-Testing-1.png`
- `Screenshots/flow-design-testing/Flow-Testing-2.png`

These images show the workflow configuration and execution evidence.

## 11. Summary

The application combines category-based routing, manager notifications, timeout-based escalation, incident updates, and audit logging to automate the initial response to critical incidents in ServiceNow.
