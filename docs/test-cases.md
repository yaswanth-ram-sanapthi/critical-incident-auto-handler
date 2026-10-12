# Test Cases and Evidence — Critical Incident Auto-Handler

## 1. Overview

This document describes the test scenarios used to validate the Critical Incident Auto-Handler ServiceNow application. Testing focuses on priority-based triggering, category-based routing, manager notifications, assignment monitoring, timeout-based escalation, incident updates, and audit logging.

Test statuses reflect the evidence observed so far. Scenarios that have not been executed and verified are marked as **Pending**.

## 2. Test Environment

- **Platform:** ServiceNow Personal Developer Instance (PDI)
- **Application:** Critical Incident Auto-Handler
- **Automation:** Flow Designer
- **Main Table:** Critical Incident Management
- **Audit Table:** Critical Incident Flow Audit
- **Development Timeout:** A short timeout was used for escalation testing.
- **Intended Response Window:** 30 minutes, subject to final configuration verification.

## 3. Test Results Summary

| Test ID | Test Scenario | Expected Result | Status |
|---|---|---|---|
| TC-01 | Critical Hardware incident routing | Incident is routed to Hardware Support | Passed for observed execution |
| TC-02 | Critical Network incident routing | Incident is routed to Network Support | Pending |
| TC-03 | Category-based routing | Each supported category maps to the correct group | Pending full verification |
| TC-04 | Initial manager notification | Relevant support group manager receives an email | Partially verified |
| TC-05 | Assignment before timeout | Incident avoids escalation and follows normal completion | Pending |
| TC-06 | Incident remains unassigned | Escalation path executes after timeout | Passed for observed execution |
| TC-07 | Escalation email | Escalation notification is generated for the intended recipient | Partially verified |
| TC-08 | Incident escalation update | Escalation status and work notes are updated | Passed for observed incident |
| TC-09 | Escalation audit logging | An audit record captures the escalation outcome | Passed for observed incident |
| TC-10 | Non-critical incident | Critical-incident flow does not trigger | Pending |
| TC-11 | Flow execution inspection | Execution details show the expected branch and action results | Passed for observed escalation execution |

## 4. Detailed Test Results

### TC-01: Critical Hardware Incident Routing

**Objective:** Verify that a critical Hardware incident is routed to Hardware Support.

**Test steps:**
1. Open the Critical Incident Management table.
2. Create a new incident.
3. Set Priority to `1 - Critical`.
4. Set Category to `Hardware`.
5. Save the record.
6. Inspect the assignment group and Flow Designer execution details.

**Expected result:** The flow triggers and sets the assignment group to Hardware Support.

**Observed result:** The Hardware branch and Update Record action completed in the tested execution.

**Status:** Passed for the observed execution.

**Evidence:**
- `Screenshots/flow-design-testing/Flow-Testing-1.png`
- `Screenshots/application-setup/Incident-Form.png`

### TC-02: Critical Network Incident Routing

**Objective:** Verify that a critical Network incident is routed to Network Support.

**Test steps:**
1. Create a new incident.
2. Set Priority to `1 - Critical`.
3. Set Category to `Network`.
4. Save the record.
5. Inspect the assignment group and flow execution.

**Expected result:** The incident is routed to Network Support.

**Observed result:** Not yet confirmed.

**Status:** Pending.

**Evidence to capture after testing:**
- `Screenshots/flow-design-testing/Flow-Testing-1.png` — replace or supplement with evidence of the Network branch if necessary.
- `Screenshots/application-setup/Incident-Form.png` — capture the resulting Network incident assignment.

### TC-03: Category-Based Routing

**Objective:** Verify that all supported categories map to their configured support groups.

| Category | Expected Assignment Group |
|---|---|
| Network | Network Support |
| Hardware | Hardware Support |
| Software | Software Support |
| Database | Database Support |
| Security | Security Support |

**Test steps:**
1. Create a Priority 1 – Critical incident for each category.
2. Save each record.
3. Inspect the resulting assignment group.
4. Review the flow execution to confirm the appropriate routing branch.

**Expected result:** Each category routes to its corresponding support group.

**Observed result:** Hardware routing was observed in a tested execution. The remaining category mappings require verification.

**Status:** Pending full verification.

**Evidence:**
- `Screenshots/flow-design-testing/Work-Flow-1.png`
- `Screenshots/flow-design-testing/Work-Flow-2.png`

Capture additional execution evidence if the current screenshots do not show each category's successful routing.

### TC-04: Initial Manager Notification

**Objective:** Verify that the relevant support group manager is notified after routing.

**Test steps:**
1. Create a critical incident with a valid category.
2. Confirm that routing completes.
3. Inspect the Send Email action in Flow Designer execution details.
4. Verify the intended recipient and inspect the available email evidence.

**Expected result:** The responsible support group manager receives an incident notification.

**Observed result:** The email action completed in the tested execution. Recipient and end-to-end delivery should be verified separately.

**Status:** Partially verified.

**Evidence:**
- `Screenshots/results-notifications/Incident-Email.png`
- `Screenshots/flow-design-testing/Flow-Testing-1.png`

### TC-05: Assignment Before Timeout

**Objective:** Verify that an incident assigned within the response window avoids escalation.

**Test steps:**
1. Create a Priority 1 – Critical incident.
2. Allow the flow to complete its routing and initial notification steps.
3. Assign the incident to a user before the timeout expires.
4. Allow the flow to continue.
5. Inspect the final incident record, branch outcome, and audit record.

**Expected result:** The flow recognizes the assignment, avoids the escalation path, and follows the normal completion path.

**Observed result:** This scenario has not yet been verified.

**Status:** Pending.

**Evidence to capture after testing:**
- Flow execution showing the normal completion branch.
- Incident form showing the Assigned to field populated.
- Relevant audit record, if the normal completion path creates one.

### TC-06: Timeout-Based Escalation

**Objective:** Verify that an incident left unassigned follows the escalation path after the configured timeout.

**Test steps:**
1. Create a Priority 1 – Critical incident.
2. Confirm that the category-based assignment group is set.
3. Leave the Assigned to field empty.
4. Wait for the configured timeout.
5. Inspect the flow execution and resulting incident record.

**Expected result:** The flow follows the escalation path when the incident remains unassigned after timeout.

**Observed result:** The tested execution entered the escalation branch and completed the escalation actions.

**Status:** Passed for the observed execution.

**Evidence:**
- `Screenshots/flow-design-testing/Flow-Testing-2.png`
- `Screenshots/flow-design-testing/Work-Flow-2.png`

### TC-07: Escalation Notification

**Objective:** Verify that the escalation path executes the escalation email action.

**Test steps:**
1. Run a test that leaves a critical incident unassigned until timeout.
2. Inspect the escalation Send Email action in the flow execution details.
3. Verify the intended recipient.
4. Check the generated email evidence where available.

**Expected result:** An escalation notification is sent to the configured recipient.

**Observed result:** The escalation email action completed in the tested execution. End-to-end email delivery must be confirmed separately.

**Status:** Partially verified.

**Evidence:**
- `Screenshots/results-notifications/Escalation-Email.png`
- `Screenshots/flow-design-testing/Flow-Testing-2.png`

### TC-08: Incident Escalation Update

**Objective:** Verify that the escalation path updates the incident record.

**Test steps:**
1. Execute the escalation scenario.
2. Open the resulting incident record.
3. Inspect Escalation Status and Work notes.

**Expected result:**
- Escalation Status is set to `Escalated`.
- A work note explains the escalation.

**Observed result:** The tested incident showed Escalation Status as Escalated.

**Status:** Passed for the observed incident.

**Evidence:**
- `Screenshots/results-notifications/Escalated-Incident.png`

### TC-09: Escalation Audit Logging

**Objective:** Verify that the escalation outcome is recorded in the audit table.

**Test steps:**
1. Execute the escalation scenario.
2. Open the Critical Incident Flow Audit table.
3. Locate the audit record associated with the test incident.
4. Inspect its action, status, details, and execution time.

**Expected result:** An audit record captures the escalation action and outcome.

**Observed result:** An Escalated audit record was observed with the action `Critical Incident Escalated` and details indicating that the incident remained unassigned.

**Status:** Passed for the observed incident.

**Evidence:**
- `Screenshots/results-notifications/Escalation-Audit-Record.png`
- `Screenshots/application-setup/Audit-Form.png`

### TC-10: Non-Critical Incident

**Objective:** Verify that a non-critical incident does not start the critical-incident flow.

**Test steps:**
1. Create an incident with a priority other than `1 - Critical`.
2. Save the record.
3. Inspect Flow Designer execution details for that record.

**Expected result:** The critical-incident flow does not trigger for the non-critical incident.

**Observed result:** Not yet confirmed.

**Status:** Pending.

**Evidence to capture after testing:**
- Incident form showing a non-critical priority.
- Flow execution evidence confirming that the critical-incident flow did not run for that record.

### TC-11: Flow Execution Inspection

**Objective:** Verify that the flow execution details provide evidence of the actions and branch outcomes.

**Test steps:**
1. Open the execution details for a tested critical incident.
2. Inspect the routing, email, wait, lookup, condition, update, and audit actions.
3. Confirm the final branch outcome.

**Expected result:** Execution details show which actions completed and which conditional branch ran.

**Observed result:** The tested escalation execution showed the escalation branch and its actions completing.

**Status:** Passed for the observed escalation execution.

**Evidence:**
- `Screenshots/flow-design-testing/Flow-Testing-1.png`
- `Screenshots/flow-design-testing/Flow-Testing-2.png`

## 5. Screenshot Evidence Index

The following screenshots are stored in the repository.

### Application Setup

| File Path | Purpose |
|---|---|
| `Screenshots/application-setup/Incident-Table.png` | Incident table structure |
| `Screenshots/application-setup/Incident-Form.png` | Incident record form |
| `Screenshots/application-setup/Audit-Table.png` | Audit table structure |
| `Screenshots/application-setup/Audit-Form.png` | Audit record form |

### Flow Design and Testing

| File Path | Purpose |
|---|---|
| `Screenshots/flow-design-testing/Work-Flow-1.png` | Workflow design overview |
| `Screenshots/flow-design-testing/Work-Flow-2.png` | Additional workflow logic |
| `Screenshots/flow-design-testing/Flow-Testing-1.png` | Flow execution evidence |
| `Screenshots/flow-design-testing/Flow-Testing-2.png` | Escalation execution evidence |

### Results and Notifications

| File Path | Purpose |
|---|---|
| `Screenshots/results-notifications/Incident-Email.png` | Initial incident notification evidence |
| `Screenshots/results-notifications/Escalation-Email.png` | Escalation notification evidence |
| `Screenshots/results-notifications/Escalated-Incident.png` | Updated incident record |
| `Screenshots/results-notifications/Escalation-Audit-Record.png` | Escalation audit entry |

## 6. Testing Notes and Limitations

- A completed Send Email action does not, by itself, prove successful delivery to the intended recipient.
- The assignment-before-timeout scenario must be tested separately from the escalation scenario.
- Verify that the post-wait condition reads the latest incident state before relying on it to distinguish assigned and unassigned incidents.
- A short timeout may be used during development testing. Confirm the final timeout configuration before describing the response window as 30 minutes.
- Verify every category mapping before marking the complete routing feature as passed.
- Keep pending scenarios marked as pending until actual execution results are available.

## 7. Conclusion

The observed escalation execution demonstrated category routing for the tested Hardware incident, execution of the email actions, updating the incident's escalation status, and creation of an escalation audit record.

Further testing is required to verify the assignment-before-timeout path, all category mappings, non-critical incident behavior, and end-to-end email delivery. This distinction keeps the project's test documentation accurate and reproducible.
