# Vacation Tracking System ( VTS )

---

## Contents

- [Domain](#domain)
- [Vision](#vision)
- [Features](#features)
- [Functional Requirements](#functional-requirements)
- [Non-functional Requirements](#non-functional-requirements)
- [Constraints](#constraints)
- [Assumptions](#assumptions)
- [Actors](#actors)
- [Use Cases](#use-cases)

## Domain

- The system is centered around on how to efficiently manage, approve, cancel, and track the leave balance.

## Vision

- Develop and deploy a flexible vacation time management
  application for managers and employees alike to use to manage their
  vacation time.
- A Vacation Tracking System (VTS) will provide individual employees with the
  capability to manage their own vacation time, sick leave, and personal time off,
  without having to be an expert in company policy or the local facility’s leave
  policies.

## Features

- Implements a flexible rules-based system for validating and verifying leave
  time requests
- Enables manager approval (optional)
- Provides access to requests for the previous calendar year, and allows
  requests to be made up to a **year and a half** in the future
- Uses e-mail notification to request manager approval and notify employees
  of request status changes
- Uses existing hardware and middleware
- Is implemented as an extension to the existing intranet portal system, and
  uses the portal’s single-sign-on mechanisms for all authentication
- Keeps activity logs for all transactions
- Enables the HR and system administration personnel to override all actions
  restricted by rules, with logging of those overrides
- Allows managers to directly award personal leave time (with system-set
  limits).
- Provides a Web service interface for other internal systems to query any
  given employee’s vacation request summary
- Interfaces with the HR department legacy systems to retrieve required
  employee information and changes

## Functional Requirements

- Employees can manage their own vacation time, sick leave, and personal time off without requiring expertise in company or facility policies
- The system displays leave balances and the status of requests covering the previous 6 months and up to 18 months into the future
  Employees can select a leave category that has a positive balance
- The system provides access to a visual calendar to pick and compare dates
- Employees specify chosen dates and hours per day (supporting partial/half days)
- Employees can input a title and short description (up to one paragraph) to justify the request

## Non-functional Requirements

- The system should be easy to use
- Employees and managers share a common portal entry and home screen (VTSHome) showing personal summaries and pending subordinate approvals in unified sections
- Central Authentication Service

## Constraints

- The system must integrate with existing HR and intranet portal systems
- This desired
  functionality must be considered in the context of any existing systems
  (and perhaps even with other proposed systems)
- HTML 3.2 capable browser
- Enterprise Java Beans (EJB) 2.x (specifically, Container Managed Persistence [CMP] beans for entities, and session beans for controllers), and Service Data Objects (SDO)
- Must be tested and designed for low-bandwidth connections and weak client machines by avoiding large downloads and heavy client scripts

## Assumptions

- Employees will have unique identifiers within the system.
- Managers will have the authority to approve or reject leave requests for their immediate subordinates.
- HR clerks will have access to all employee records for administrative purposes.
- The system will be used in an environment with reliable network connectivity.
- Users will have basic knowledge of how to interact with web-based applications.
- The system will comply with organizational policies and legal requirements regarding leave management.
- All employees work eight-hour days.
- Each employee’s vacation time requests are subject to the restrictions of
each employee’s primary work location in addition to overall company policies and restrictions.
- Vacation time request validation rules are defined and owned by the HR
  department

## Actors ( 4 Actors):

- **Employee**: The main user of this system. An employee uses this system to
  manage his or her vacation time.
- **Manager**: An employee who has all the abilities and goals of a regular
  employee, but with the added responsibility of approving vacation requests
  for immediate subordinates. A manager may award subordinates comp
  time, subject to certain limits set in the system.
- **Clerk**: A member of the HR department who has sufficient rights to view
  employees’ personal data and is responsible for ensuring that employees’
  information in all HR systems is up to date and correct. An HR clerk can
  add or remove nearly any record in the system. In the real world, HR clerks
  may or may not be employees; however, if they are employees, they use two
  separate login IDs to manage these two different roles.
- **System Admin**: A role responsible for the smooth running of the system’s technical resources (e.g., Web server, database) and for collecting and
  archiving all log files.

## Use Cases (8 Use Cases):

- Manage Time: Describes how employees request and view vacation time
  requests.
- Approve Request: Describes how a manager responds to a subordinate’s request for vacation time.
- Award Time: Describes how a manager can award a subordinate extra
  leave time (comp time).
- Edit Employee Record: Describes how an HR clerk edits an employee’s
  information in the system. This includes setting all the leave time allowances and the maximum time that can be awarded by the manager.
- Manage Locations: Describes how an HR clerk manages location
  records and their rules.
- Manage Leave Categories: Describes how an HR clerk manages
  leave categories and their rules.
- Override Leave Records: Describes how an HR clerk may override
  any rejection of leave time requests made by the rules in the system.
- Back Up System Logs: Describes how the system administrator backs
  up the system’s logs.
