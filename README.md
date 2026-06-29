# Software Installation Request Automation — ServiceNow

## Project Overview

A ServiceNow-based automation system that streamlines the end-to-end lifecycle 
of software installation requests within an organization. Built using ServiceNow 
Personal Developer Instance (PDI) as part of the Skill Wallet Program.


## Problem Statement

Employees in organizations request software through manual emails and phone calls, 
causing:
- Delays in approval and fulfillment
- No visibility into request status
- Non-compliance with software licensing policies
- Increased manual workload for IT teams


## Solution

An automated Software Installation Request system built on ServiceNow using:
- **Service Catalog** — Standardized request form
- **Flow Designer** — Automated approval and task creation
- **Business Rules** — Exception handling for license issues
- **Email Notifications** — Real-time updates at every stage
- **Service Portal** — Employee-facing submission interface


## Features

- One-click software request submission via Service Portal (/sp)
- 4 catalog variables: Software Name, Version, License Justification, Urgency
- Automated approval routing to Manager / IT Admin group
- Auto-created fulfillment task (sc_task) for Software Support Team
- Email notifications on submission, approval, rejection and completion
- Business Rule auto-raises incident when task is On Hold (license unavailable)
- Update Set export and deployment to another PDI instance


## Technology Stack

| Component | Technology |
|---|---|
| Platform | ServiceNow PDI |
| User Interface | ServiceNow Service Portal (/sp) |
| Workflow Automation | Flow Designer (Workflow Studio) |
| Exception Handling | Business Rules (GlideRecord Script) |
| Request Management | Service Catalog (sc_cat_item) |
| Database Tables | sc_request, sc_req_item, sc_task, sysapproval_approver |
| Deployment | ServiceNow Update Set (XML) |
| Notifications | ServiceNow Email Notification Engine |


## ServiceNow Tables Used

| Table | Purpose |
|---|---|
| sc_cat_item | Stores the catalog item definition |
| sc_request | Created when employee submits the request |
| sc_req_item | Links catalog item to the request |
| sysapproval_approver | Stores approval records |
| sc_task | Fulfillment task assigned to IT Support Team |
| incident | Auto-created when task is On Hold |


## Project Setup

### Prerequisites
- ServiceNow Personal Developer Instance (PDI)
- Admin access to the PDI instance
- Flow Designer enabled on the instance

### Installation Steps

**Step 1 — Import Update Set**
1. Download the SoftwareInstallationAutomation.xml file from this repository
2. Login to your ServiceNow PDI instance
3. Go to **All → Search "Retrieved Update Sets"**
4. Click **"Import Update Set from XML"**
5. Upload the downloaded XML file
6. Click **"Preview Update Set"**
7. Click **"Commit Update Set"**

**Step 2 — Verify Catalog Item**
1. Go to **All → Maintain Items**
2. Search for **"Software Installation Request"**
3. Confirm 4 variables are present:
   - Software Name
   - Version Required
   - License Justification
   - Urgency

**Step 3 — Verify Workflow**
1. Go to **All → Flow Designer**
2. Open **"Software Request Workflow"**
3. Confirm flow is Active with:
   - Service Catalog Trigger
   - Ask for Approval action
   - Create Task action

**Step 4 — Test via Service Portal**
1. Open your PDI URL with /sp suffix
   - Example: https://dev378029.service-now.com/sp
2. Search for **"Software Installation"**
3. Fill in all variables and click **"Order Now"**
4. Verify sc_request and sc_req_item records are created


## Catalog Item Variables

| Variable | Type | Question |
|---|---|---|
| Software Name | Single Line Text | What software do you need? |
| Version Required | Single Line Text | Specify version (if required) |
| License Justification | Multi Line Text | Why do you need this software? |
| Urgency | Choice (Normal, High, Critical) | Select urgency level |


## Business Rule — Source Code

**Table:** sc_task  
**Condition:** State changes to On Hold  
**Script:**

javascript
if (current.state == 3) { // On Hold
  var inc = new GlideRecord('incident');
  inc.initialize();
  inc.short_description = 'License unavailable for ' 
                          + current.request_item;
  inc.description = 'Software request could not be fulfilled 
                     due to license shortage.';
  inc.caller_id = current.request.requested_for;
  inc.assignment_group.setDisplayValue('Software Support');
  inc.priority = 3;
  inc.insert();
}


## Workflow Flow

Employee Submits Request (Service Portal)

↓

Service Catalog Item (sc_cat_item)

↓

Request Created (sc_request)

↓

Requested Item Created (sc_req_item)

↓

Flow Designer Workflow Triggered

↓

Ask for Approval Action

(sysapproval_approver table)

↓

┌───────┴───────┐

Approved          Rejected

↓                  ↓

Create sc_task    Email Notification

↓                   to Employee

Software Support

Team Fulfills

↓

Task Completed

↓

Request Closed +

Email Confirmation

---

## Test Cases Summary

| Test Case | Scenario | Result |
|---|---|---|
| TC-001 | Submit request via Service Portal | Pass |
| TC-002 | Email notification on submission | Pass |
| TC-003 | Approval routing via Flow Designer | Pass |
| TC-004 | Approve request and create sc_task | Pass |
| TC-005 | Reject request and notify employee | Pass |
| TC-006 | Task fulfillment and request closure | Pass |
| TC-007 | Business Rule — On Hold incident | Pass |
| TC-008 | Service Portal meta tag search | Pass |
| TC-009 | Update Set export and deployment | Pass |

**Total Test Cases: 9 | Passed: 9 | Failed: 0**



## Project Structure

Software-Installation-Request-Automation/

│

├── README.md

├── Software Installation Request Automation report

├── BusinessRule - script

---

## Demo

> 📹 Demo Video: https://drive.google.com/file/d/1OBp7v-5HIK7VHwIniDEabe5UDKld-8Yy/view

---

## Author

**Name:** Rooba B  
**Program:** Skill Wallet — ServiceNow Global Certification  
**Project:** Software Installation Request Automation  
**Instance:** https://dev378029.service-now.com  
**Date:** June 2026



## License

This project is built for educational purposes as part of the 
Skill Wallet certification program.
