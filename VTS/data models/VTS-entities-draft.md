Employee
 - id
 - firstName
 - lastName
 - email (unique)
 - managerId (fk - employee)
 - empTypeId (fk - employee)
 - createdAt
 - updatedAt

EmployeeType
- id
- type (enum - e.g., Employee, Manager, Clerk, SysAdmin)

vacation
- id
- type (enum - e.g., Annual, Sick, Unpaid)
- numberOfDays

user_vacation_balance
- id
- employeeId (fk - employee)
- vacationId (fk - vacation)
- vacationType
- numberOfRemainingDays
- validUntil
- createdAt
- updatedAt

vacationRequest
- id
- employeeId (fk - employee)
- vacationId (fk - vacation)
- vacationBalanceId (fk - user_vacation_balance)
- managerId (fk - employee)
- title
- description
- startDate (timestamp)
- endDate (timestamp)
- status (Pending, Canceled, Approved, Rejected)
- isAutoApproved (boolean)
- rejectionReason
- approvedBy (fk - employee)
- submittedBy (fk - employee)
- createdAt
- createdBy (fk - employee)
- updatedAt
- updatedBy (fk - employee)
- approvedAt (timestamp)

ApprovalStatus
- id
- statusName (Pending, Canceled, Approved, Rejected)

AllowedNextStatus (composite PK)
- statusId (fk/pk - ApprovalStatus) 
- allowedNextStatusId (fk/pk - ApprovalStatus)
- permittedEmpTypeId (fk - employeeType)
- createdBy (fk - employee)
- createdAt
- updatedBy (fk - employee)
- updatedAt
