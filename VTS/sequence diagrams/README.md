# Manage Time Use Case - Sequence Diagrams

## Overview
This directory contains three comprehensive sequence diagrams that illustrate the "Manage Time" use case from the Vacation Tracking System (VTS). These diagrams detail the interactions between employees, managers, the VTS application, the database, and the email service.

---

## 1. Employee Request Flow (`Manage-Time-EmployeeRequestFlow.puml`)

**Purpose:** Illustrates the process of an employee creating and submitting a vacation request.

### Key Participants:
- **Employee** - Initiates the request
- **Intranet Portal** - Entry point to VTS
- **VTS Application** - Core business logic
- **VTS Database** - Persistent data storage
- **Email Service** - Notification delivery
- **Manager** - Receives approval notification

### Main Interactions:
1. Employee accesses VTS from intranet portal using existing credentials
2. System retrieves and displays vacation history (6 months back, 18 months forward)
3. Employee selects vacation type with positive balance
4. System provides interactive calendar interface
5. Employee selects dates, hours, title, and description
6. VTS validates request data
   - **If validation fails:** Display errors and allow correction
   - **If validation succeeds:** Proceed to database storage
7. Request is created with PENDING_APPROVAL status
8. Vacation balance is updated (deducted)
9. Based on approval policy:
   - **Auto-approved requests:** Status changed to APPROVED immediately
   - **Requests requiring approval:** Email notification sent to manager
10. Employee returns to home page with confirmation

### Decision Points:
- **Validation Check:** Ensures all required fields and business rules are met
- **Auto-approval Policy:** Determines if manager approval is required

---

## 2. Manager Approval Flow (`Manage-Time-ManagerApprovalFlow.puml`)

**Purpose:** Illustrates how managers review, approve, or reject pending vacation requests.

### Key Participants:
- **Manager** - Reviews and approves/rejects requests
- **Email Notification** - Approval request delivery
- **Intranet Portal** - Access point
- **VTS Application** - Business logic
- **VTS Database** - Data storage and updates
- **Email Service** - Notification dispatch
- **Employee** - Receives decision notification

### Main Interactions:
1. Manager receives approval notification via email (or navigates manually)
2. Manager accesses VTS portal
   - May need to provide authentication credentials
3. VTS displays home page with:
   - Manager's own vacation requests and balances
   - Section for pending subordinate requests
4. Manager selects a pending request to review
5. System retrieves and displays complete request details
6. Manager makes decision:
   - **Approve:** Request status changes to APPROVED
   - **Reject:** Manager must provide rejection reason
7. Database is updated with decision and metadata
8. If request is rejected: Employee's vacation balance is restored
9. Approval/rejection notification is sent to employee
10. Manager returns to home page and may process additional requests

### Decision Points:
- **Authentication:** Manager may need to authenticate if session expired
- **Approval Decision:** Manager can approve or reject each request
- **Rejection Reason:** Required when request is rejected

---

## 3. Complete End-to-End Flow (`Manage-Time-CompleteFlow.puml`)

**Purpose:** Provides a comprehensive overview of the entire use case from employee request to manager approval/rejection and final notification.

### Phases:

#### Phase 1: Employee Request Phase
- Steps 1-13 mirror the Employee Request Flow
- Includes validation logic and conditional branching
- Ends with request creation and conditional manager notification

#### Phase 2: Manager Approval Phase
- Steps 14-26 mirror the Manager Approval Flow
- Manager receives notification and navigates to VTS
- Handles authentication if needed
- Manager reviews and makes approval/rejection decision
- Final notification sent to employee

### Complete Flow Sequence:
1. **Portal Access** → Employee initiates request
2. **Data Retrieval** → System fetches vacation history and balances
3. **Request Creation** → Employee submits vacation request details
4. **Validation** → System validates request (with error handling)
5. **Database Storage** → Request and balance updates recorded
6. **Conditional Notification** → Manager notified (if approval required)
7. **Manager Access** → Manager accesses VTS (with optional authentication)
8. **Request Review** → Manager reviews pending requests
9. **Decision Making** → Manager approves or rejects
10. **Database Update** → Status updated (with balance restoration if rejected)
11. **Result Notification** → Employee receives decision
12. **Next Actions** → Manager can process additional requests

---

## Data Flow Summary

### Key Database Operations:

**When Request is Submitted:**
- Create `VacationRequest` record with status = PENDING_APPROVAL
- Deduct hours from `UserVacationBalance`
- Record audit fields (createdBy, createdAt, etc.)

**When Request is Approved:**
- Update `VacationRequest` status to APPROVED
- Set `approvedBy` to manager's employee ID
- Set `approvedAt` timestamp
- Vacation balance remains deducted

**When Request is Rejected:**
- Update `VacationRequest` status to REJECTED
- Set `rejectionReason` with manager's explanation
- Restore deducted hours to `UserVacationBalance`

---

## Key Features Illustrated

### 1. Authentication & Authorization
- Employee authenticated by portal framework
- Manager may need to re-authenticate for VTS access
- Role-based access (manager approval capabilities)

### 2. Validation & Error Handling
- Request data validation
- Balance verification (sufficient vacation time available)
- Error messaging with highlighted fields
- Correction and resubmission capability

### 3. Conditional Logic
- Auto-approval based on company policy
- Conditional email notifications
- Different balance handling for approval vs. rejection

### 4. Audit Trail
- Request creation tracking (createdBy, createdAt)
- Approval/rejection recording (approvedBy, approvedAt)
- Rejection reason documentation
- Balance update tracking

### 5. Notification System
- Email notification to manager for approval
- Email notification to employee for decision
- Embedded approval links for manager convenience

---

## State Transitions

```
PENDING_APPROVAL
    ├─→ APPROVED (manager approval or auto-approval)
    └─→ REJECTED (manager rejection)
```

### Balance Changes:
- **Submission:** Balance decreases (request deducted)
- **Approval:** Balance remains decreased (time is committed)
- **Rejection:** Balance increases (deduction reversed)

---

## Use Case Mapping

These sequence diagrams directly implement all flows described in the Manage Time Use Case:

| Use Case Step | Sequence Diagram | Phase |
|---|---|---|
| 1-2 | Employee Request Flow | Steps 1-4 |
| 3-5 | Employee Request Flow | Steps 5-8 |
| 6-7 | Employee Request Flow | Validation Alt |
| 8-9 | Employee Request Flow/Complete Flow | Steps 12-13 |
| 10-11 | Manager Approval Flow/Complete Flow | Steps 14-16 |
| 12-13 | Manager Approval Flow/Complete Flow | Steps 17-22 |
| 14 | Manager Approval Flow/Complete Flow | Step 23-24 |

---

## Notes for Development

1. **Email Service:** Consider implementing email queuing for reliability
2. **Calendar UI:** The calendar selection interface is critical for UX
3. **Validation Rules:** Clearly document all validation rules for request data
4. **Balance Calculations:** Implement decimal precision for partial-day requests
5. **Audit Logging:** Ensure comprehensive audit trail for compliance
6. **Error Handling:** Implement graceful error messages and recovery options
7. **Auto-approval Policy:** Make this configurable at the company level
8. **Manager Assignment:** Ensure proper manager-employee relationships are maintained
