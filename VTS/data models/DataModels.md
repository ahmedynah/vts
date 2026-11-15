# VTS (Vacation Time System) - Data Models

## Entity Definitions

### 1. Employee
Represents users in the system who can request vacation and manage approvals.

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| id | UUID/INT (PK) | PRIMARY KEY | Unique identifier for employee |
| firstName | VARCHAR(100) | NOT NULL | Employee's first name |
| lastName | VARCHAR(100) | NOT NULL | Employee's last name |
| email | VARCHAR(255) | NOT NULL, UNIQUE | Employee's email address |
| managerId | INT/UUID (FK) | FOREIGN KEY (Employee.id), NULLABLE | Reference to direct manager (self-referencing) |
| empTypeId | INT/UUID (FK) | FOREIGN KEY (EmployeeType.id), NOT NULL | Employee's role type (Employee, Manager, Clerk, SysAdmin) |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Last record update timestamp |

**Relationships:**
- Self-reference: Manager (Employee.managerId → Employee.id)
- Child of EmployeeType (N:1)
- Parent to VacationRequest (1:N)
- Parent to UserVacationBalance (1:N)

---

### 2. EmployeeType
Defines employee role types in the system for access control and workflow rules.

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| id | UUID/INT (PK) | PRIMARY KEY | Unique identifier for employee type |
| type | ENUM | NOT NULL, UNIQUE, VALUES: ('Employee', 'Manager', 'Clerk', 'SysAdmin') | Role type of the employee |
| description | TEXT | NULLABLE | Description of the role |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Last record update timestamp |

**Relationships:**
- Parent to Employee (1:N)
- Parent to AllowedNextStatus (1:N)

---

### 3. Vacation
Defines types of vacation available in the system.

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| id | UUID/INT (PK) | PRIMARY KEY | Unique identifier for vacation type |
| type | ENUM | NOT NULL, VALUES: ('Annual', 'Sick', 'Unpaid', ...) | Type of vacation |
| numberOfDays | INT | NOT NULL | Default number of days for this vacation type |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Last record update timestamp |

**Relationships:**
- Parent to UserVacationBalance (1:N)
- Parent to VacationRequest (1:N)

---

### 4. UserVacationBalance
Tracks vacation balance per employee per vacation type.

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| id | UUID/INT (PK) | PRIMARY KEY | Unique identifier |
| employeeId | INT/UUID (FK) | FOREIGN KEY (Employee.id), NOT NULL | Reference to employee |
| vacationId | INT/UUID (FK) | FOREIGN KEY (Vacation.id), NOT NULL | Reference to vacation type |
| vacationType | VARCHAR(50) | NOT NULL | Type of vacation (denormalized from Vacation.type) |
| numberOfRemainingDays | DECIMAL(5,2) | NOT NULL | Days remaining in balance |
| validUntil | DATE | NOT NULL | Expiration date for the balance |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Last record update timestamp |

**Constraints:**
- UNIQUE (employeeId, vacationId, validUntil)

**Relationships:**
- Child of Employee (N:1)
- Child of Vacation (N:1)
- Parent to VacationRequest (1:N)

---

### 5. VacationRequest
Records individual vacation requests from employees.

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| id | UUID/INT (PK) | PRIMARY KEY | Unique identifier |
| employeeId | INT/UUID (FK) | FOREIGN KEY (Employee.id), NOT NULL | Employee requesting vacation |
| vacationId | INT/UUID (FK) | FOREIGN KEY (Vacation.id), NOT NULL | Type of vacation requested |
| vacationBalanceId | INT/UUID (FK) | FOREIGN KEY (UserVacationBalance.id), NOT NULL | Balance being used |
| managerId | INT/UUID (FK) | FOREIGN KEY (Employee.id), NOT NULL | Manager to approve request |
| approvalStatusId | INT/UUID (FK) | FOREIGN KEY (ApprovalStatus.id), NOT NULL | Current approval status |
| title | VARCHAR(255) | NOT NULL | Title/subject of request |
| description | TEXT | NULLABLE | Additional details/notes |
| startDate | TIMESTAMP | NOT NULL | Vacation start date and time |
| endDate | TIMESTAMP | NOT NULL | Vacation end date and time |
| isAutoApproved | BOOLEAN | DEFAULT FALSE | Whether request was auto-approved |
| rejectionReason | TEXT | NULLABLE | Reason for rejection if rejected |
| approvedBy | INT/UUID (FK) | FOREIGN KEY (Employee.id), NULLABLE | Employee who approved the request |
| submittedBy | INT/UUID (FK) | FOREIGN KEY (Employee.id), NOT NULL | Employee who submitted the request |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Request creation timestamp |
| createdBy | INT/UUID (FK) | FOREIGN KEY (Employee.id), NOT NULL | User who created the record |
| updatedAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Last update timestamp |
| updatedBy | INT/UUID (FK) | FOREIGN KEY (Employee.id), NOT NULL | User who last updated the record |
| approvedAt | TIMESTAMP | NULLABLE | Timestamp when request was approved |

**Relationships:**
- Child of Employee (requestor) (N:1)
- Child of Employee (manager) (N:1)
- Child of Vacation (N:1)
- Child of UserVacationBalance (N:1)
- Child of ApprovalStatus (N:1)
- Child of Employee (approvedBy) (N:1)
- Child of Employee (submittedBy) (N:1)
- Child of Employee (createdBy) (N:1)
- Child of Employee (updatedBy) (N:1)

---

### 6. ApprovalStatus
Defines possible approval statuses in the system.

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| id | UUID/INT (PK) | PRIMARY KEY | Unique identifier |
| statusName | VARCHAR(50) | NOT NULL, UNIQUE | Status name (Pending, Approved, Rejected, Canceled) |
| description | TEXT | NULLABLE | Description of the status |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Last record update timestamp |

**Relationships:**
- Parent to AllowedNextStatus (1:N) - as source status
- Parent to AllowedNextStatus (1:N) - as target status

---

### 7. AllowedNextStatus
Defines valid status transitions (workflow state machine) with role-based constraints.

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| statusId | INT/UUID (FK/PK) | FOREIGN KEY (ApprovalStatus.id), PRIMARY KEY | Current status |
| allowedNextStatusId | INT/UUID (FK/PK) | FOREIGN KEY (ApprovalStatus.id), PRIMARY KEY | Next allowed status |
| permittedEmpTypeId | INT/UUID (FK) | FOREIGN KEY (EmployeeType.id), NOT NULL | Employee type permitted to make this transition |
| createdBy | INT/UUID (FK) | FOREIGN KEY (Employee.id), NOT NULL | User who created this rule |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |
| updatedBy | INT/UUID (FK) | FOREIGN KEY (Employee.id), NOT NULL | User who last updated this rule |
| updatedAt | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Last record update timestamp |

**Constraints:**
- PRIMARY KEY (statusId, allowedNextStatusId, permittedEmpTypeId)

**Relationships:**
- Child of ApprovalStatus (N:1) - statusId
- Child of ApprovalStatus (N:1) - allowedNextStatusId
- Child of EmployeeType (N:1) - permittedEmpTypeId
- Child of Employee (N:1) - createdBy
- Child of Employee (N:1) - updatedBy

---

## Entity Relationships Summary

```
EmployeeType (1) ──→ (N) Employee
EmployeeType (1) ──→ (N) AllowedNextStatus

Employee (1) ──→ (N) VacationRequest (as requestor)
Employee (1) ──→ (N) VacationRequest (as manager)
Employee (1) ──→ (N) VacationRequest (as approvedBy)
Employee (1) ──→ (N) VacationRequest (as submittedBy)
Employee (1) ──→ (N) VacationRequest (as createdBy)
Employee (1) ──→ (N) VacationRequest (as updatedBy)
Employee (1) ──→ (N) AllowedNextStatus (as createdBy)
Employee (1) ──→ (N) AllowedNextStatus (as updatedBy)
Employee (1) ──→ (N) UserVacationBalance
Employee (1) ──→ (0,1) Employee (Manager self-reference)

Vacation (1) ──→ (N) UserVacationBalance
Vacation (1) ──→ (N) VacationRequest

UserVacationBalance (1) ──→ (N) VacationRequest

ApprovalStatus (1) ──→ (N) AllowedNextStatus (as source)
ApprovalStatus (1) ──→ (N) AllowedNextStatus (as target)
```

---

## Key Constraints & Business Rules

1. **Employee Email Uniqueness**: No two employees can have the same email
2. **Employee Type**: Each employee must have a valid role type (Employee, Manager, Clerk, SysAdmin)
3. **Manager Self-Reference**: An employee's manager is another employee (or NULL for top-level)
4. **Vacation Balance Uniqueness**: Each employee has one balance per vacation type per validity period
5. **Role-Based Status Transitions**: Status transitions are permitted only for specific employee types via AllowedNextStatus
6. **Status Workflow**: Status transitions must follow rules defined in AllowedNextStatus with employee type validation
7. **Date Validation**: startDate must be before endDate in VacationRequest
8. **Auto-Approval**: isAutoApproved flag indicates system-level approval without manager intervention
9. **Audit Trail**: All entities have createdBy/updatedBy/createdAt/updatedAt for full traceability

