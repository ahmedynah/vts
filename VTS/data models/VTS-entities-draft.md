Employee
 - id
 - firstName
 - lastName
 - email (unique)
 - managerId (fk - employee)
 - createdAt
 - updatedAt

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
