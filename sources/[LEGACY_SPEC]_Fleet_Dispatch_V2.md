# Fleet Dispatch V2

## Legacy System Specification

**Document Type:** Legacy System Technical Specification
**System Version:** 2.4
**Original Development:** 2017–2019
**Current Status:** Production
**Primary Purpose:** Fleet dispatch and field-service coordination
**Document Classification:** Fictional portfolio project

---

# 1. System Overview

Fleet Dispatch V2 is a web-based fleet dispatch and field-service management application used by transportation and field-service companies to coordinate vehicles, drivers, dispatchers, customers, and service jobs.

The system was originally developed between 2017 and 2019. Several features have been added since the original release, but the core architecture has remained largely unchanged.

The system is currently deployed for approximately 120 vehicles and 180 active users.

The application supports:

* vehicle management
* driver management
* customer management
* job management
* dispatching
* route assignment
* job status tracking
* driver communication
* basic GPS tracking
* invoicing data export
* operational reporting

The system is considered business-critical because dispatchers use it throughout the working day to assign and monitor field jobs.

The company wants to modernize the system because the existing architecture makes new functionality increasingly difficult to implement.

---

# 2. Current Business Environment

The company operates a mixed fleet of service vehicles.

Typical workflow:

1. Customer contacts the company.
2. A dispatcher creates a service job.
3. The dispatcher assigns a vehicle and driver.
4. The driver receives the job.
5. The driver travels to the customer location.
6. The driver updates the job status.
7. The dispatcher monitors progress.
8. The driver completes the job.
9. Job information is sent to the invoicing system.
10. Management reviews operational reports.

Approximately 1,500–2,000 jobs are created each month.

The system operates Monday through Saturday.

Normal operating hours are 06:00–22:00.

The company expects the number of vehicles and jobs to increase significantly during the next three years.

---

# 3. Current Architecture

Fleet Dispatch V2 uses a traditional three-tier architecture.

## Components

### Web Application

Technology:

* PHP 7.4
* Laravel 6
* JavaScript
* jQuery
* Bootstrap

The web application is used primarily by:

* dispatchers
* fleet managers
* administrators
* customer-service staff

The application runs on a single application server.

---

### Mobile Application

The driver application is an Android application.

Technology:

* Java
* Android SDK
* SQLite

The Android application communicates with the backend through REST APIs.

The mobile application was developed independently from the web application.

Some business rules are duplicated between the mobile application and the backend.

---

### Database

Database technology:

* MySQL 5.7

The database contains the primary operational data.

Major tables include:

* users
* roles
* drivers
* vehicles
* customers
* addresses
* jobs
* job_status_history
* assignments
* routes
* GPS_positions
* invoices
* notifications
* audit_log

The database is hosted on the same physical infrastructure as the application.

The database contains approximately:

* 8 million GPS position records
* 1.5 million job records
* 180 user records
* 120 vehicle records
* 600 driver records
* 30,000 customer records

Historical GPS data continues to grow rapidly.

---

# 4. Infrastructure

The current production environment consists of:

* one physical application server
* one physical database server
* one backup server

Operating system:

* Ubuntu Linux

The application server and database server are located in the company's primary office data center.

There is no cloud infrastructure.

There is no automated infrastructure provisioning.

Servers are maintained manually by the internal IT team.

---

# 5. Application Architecture

The web application follows a traditional MVC structure.

The architecture contains:

* controllers
* models
* views
* service classes
* database queries

However, business logic is not consistently separated from presentation logic.

Several controllers contain:

* database queries
* business rules
* validation
* notification logic
* integration code

Some older modules access the database directly.

The application has approximately 250,000 lines of PHP code.

---

# 6. API Architecture

The backend provides REST APIs for the Android application.

API version:

**v1**

Authentication uses username/password authentication combined with long-lived API tokens.

The API is not consistently versioned at the endpoint level.

Example endpoints:

```text
GET /api/jobs
GET /api/jobs/{id}
POST /api/jobs
PUT /api/jobs/{id}
GET /api/drivers
GET /api/vehicles
POST /api/assignments
POST /api/gps
GET /api/routes
```

API responses are primarily JSON.

There is no formal API gateway.

Rate limiting is implemented only on a small number of endpoints.

API documentation is incomplete.

Some endpoints are used by external integrations even though they were originally designed only for the mobile application.

---

# 7. Driver Mobile Application

The Android application allows drivers to:

* log in
* view assigned jobs
* view customer information
* view addresses
* start a job
* pause a job
* complete a job
* send status updates
* send GPS information
* add notes
* upload photographs

The application stores some data locally using SQLite.

The application can continue working for a limited period when network connectivity is unavailable.

When connectivity returns, locally stored information is synchronized with the backend.

The synchronization mechanism is custom-built.

There have been occasional synchronization conflicts when the same job is updated by both the driver and dispatcher while the device is offline.

---

# 8. GPS Tracking

Vehicles send GPS coordinates approximately every 30 seconds while a driver is active.

GPS information is stored directly in the MySQL database.

The web application queries the database to display recent vehicle locations.

The system currently processes approximately:

**250,000–300,000 GPS records per day.**

GPS data is retained indefinitely.

There is no separate time-series database.

There is no automated archival process.

Historical GPS queries can become slow when dispatchers request information covering several months.

---

# 9. Dispatch Module

The dispatch module is the most frequently used part of the application.

Dispatchers can:

* view available vehicles
* view available drivers
* view customer jobs
* assign jobs
* change assignments
* change job priorities
* update job status
* monitor active jobs
* view vehicle locations

The dispatch dashboard refreshes data every 15 seconds using AJAX requests.

The dashboard does not use WebSockets or another real-time event mechanism.

When the number of active jobs increases significantly, database load increases because multiple users repeatedly request updated information.

---

# 10. Job Lifecycle

A job normally follows this lifecycle:

```text
New
  ↓
Scheduled
  ↓
Assigned
  ↓
Accepted
  ↓
En Route
  ↓
Arrived
  ↓
In Progress
  ↓
Completed
```

Other states include:

* Cancelled
* Rejected
* Failed
* Rescheduled

Job status changes are recorded in `job_status_history`.

The status history is stored in the primary MySQL database.

---

# 11. Notifications

The system sends notifications for:

* new job assignments
* changed assignments
* cancelled jobs
* delayed jobs
* completed jobs

Notifications are primarily delivered through:

* Android push notifications
* email

The notification mechanism is tightly coupled to the main application.

If a notification provider becomes unavailable, some application operations may be delayed because notification processing occurs synchronously in parts of the application.

---

# 12. External Integrations

Fleet Dispatch V2 integrates with several external systems.

## GPS Provider

Vehicle tracking devices send information through an external GPS provider.

The provider sends data to the Fleet Dispatch API.

---

## Accounting System

Completed job information is exported to the company's accounting system.

The integration uses scheduled CSV exports.

Exports are generated once per day.

There is no real-time integration.

---

## Mapping Provider

The web application uses a third-party mapping API for:

* geocoding
* address lookup
* route visualization
* map display

Mapping requests are made directly from the application.

There is no centralized map service.

---

## Email Provider

The application uses SMTP for email notifications.

SMTP credentials are stored in environment configuration on the application server.

---

# 13. Security

The system uses:

* username/password authentication
* role-based access control
* HTTPS
* application-level audit logging
* password hashing

Roles include:

* Administrator
* Dispatcher
* Fleet Manager
* Driver
* Customer Service
* Reporting User

Security limitations have been identified.

These include:

* long-lived API tokens
* inconsistent API authorization checks
* limited multi-factor authentication
* application and database servers located within the same network
* manual security patching
* limited centralized security monitoring

The system does not currently use a centralized identity provider.

---

# 14. Data Management

The MySQL database is the central source of truth.

Most application modules directly access the same database.

There are limited database constraints in several older tables.

Some historical tables contain duplicated information.

Several reports execute complex SQL queries directly against production tables.

There is no dedicated analytical database.

There is no data warehouse.

Business intelligence is performed using exported CSV files.

---

# 15. Reporting

The application provides reports including:

* jobs completed
* jobs cancelled
* jobs by driver
* jobs by vehicle
* average completion time
* vehicle utilization
* driver performance
* customer activity

Most reports are generated synchronously by querying the production database.

Some reports can take several minutes when large date ranges are selected.

Management has requested:

* real-time operational dashboards
* historical analytics
* fleet utilization analysis
* predictive maintenance information
* driver performance analytics

The current architecture was not designed for these requirements.

---

# 16. Backup and Disaster Recovery

Database backups are performed nightly.

Backups are copied to a separate backup server.

The company performs manual restoration tests.

The documented recovery target is:

**RPO:** 24 hours

**RTO:** 12 hours

There is no automated disaster recovery environment.

The company does not currently operate a secondary production environment.

---

# 17. Deployment Process

Production deployments are performed manually.

Typical deployment process:

1. Developer prepares release.
2. Code is copied to the production server.
3. Database migration scripts are executed manually.
4. Application cache is cleared.
5. Application is restarted if necessary.
6. Basic smoke testing is performed.

There is no automated CI/CD pipeline.

There are separate development and production environments, but there is no dedicated staging environment.

---

# 18. Development Process

The development team consists of:

* 4 backend developers
* 2 Android developers
* 1 QA engineer
* 1 system administrator

The application uses Git for source control.

Automated unit-test coverage is limited.

Integration testing is mostly manual.

There are no automated performance tests.

There are no automated infrastructure tests.

---

# 19. Current Performance Problems

The company has identified several performance problems.

### Problem 1 — Dispatch Dashboard

The dispatch dashboard becomes slower when many dispatchers are working simultaneously.

### Problem 2 — GPS Queries

Historical GPS queries can take several seconds or longer.

### Problem 3 — Reporting

Large reports can significantly increase database load.

### Problem 4 — API Traffic

The API receives increasing traffic from mobile devices and external integrations.

### Problem 5 — Database Growth

The database is growing continuously, especially because of GPS data.

---

# 20. Current Scalability Limitations

The current architecture has several scalability limitations:

* single application server
* single primary database
* manually managed infrastructure
* tightly coupled application modules
* synchronous processing
* shared production database
* no automated horizontal scaling
* limited caching
* no event-driven architecture
* no dedicated analytics platform

Increasing the number of vehicles and jobs is expected to increase infrastructure pressure.

---

# 21. Known Technical Debt

The following technical debt has been documented:

1. Legacy PHP framework version
2. Older MySQL version
3. Duplicated business logic
4. Limited automated testing
5. Manual deployment
6. Incomplete API documentation
7. Long-lived API authentication tokens
8. Direct reporting against production database
9. No centralized identity management
10. No centralized monitoring platform
11. Large historical GPS dataset in operational database
12. Tight coupling between notifications and core application
13. Custom mobile synchronization mechanism
14. Limited disaster recovery
15. Limited infrastructure automation

---

# 22. Business Problems Caused by the Current Architecture

The technical limitations have created several business problems.

### Slow feature development

New functionality often requires changes in several tightly coupled modules.

### Operational risk

The company relies heavily on a small number of production servers.

### Limited growth

Increasing fleet size and job volume may require significant infrastructure changes.

### Reporting limitations

Management cannot easily perform large-scale historical analysis.

### Integration limitations

Adding new external systems requires custom development inside the existing application.

### Mobile limitations

The custom synchronization mechanism creates occasional data conflicts.

### Security limitations

The existing authentication and infrastructure model makes implementation of modern security controls more difficult.

---

# 23. Future Business Direction

The company expects the fleet to grow from approximately:

**120 vehicles → 500+ vehicles**

over the next three to five years.

Monthly jobs may increase from:

**1,500–2,000 → 10,000+**

The company also expects:

* more mobile users
* additional GPS providers
* additional external integrations
* international operations
* higher reporting requirements
* real-time operational monitoring
* predictive analytics
* improved disaster recovery
* stronger security controls

The existing system is expected to remain operational during the modernization process.

A complete "big bang" replacement is considered risky because Fleet Dispatch is business-critical.

---

# 24. Modernization Constraint

The company cannot simply shut down Fleet Dispatch V2 and replace it immediately.

The modernization must therefore consider:

* gradual migration
* backward compatibility
* coexistence of old and new components
* data migration
* API compatibility
* mobile application compatibility
* operational continuity
* rollback capability

The modernization team must determine which components should be:

* retained
* rehosted
* replatformed
* refactored
* rearchitected
* replaced
* retired

---

# 25. Key Questions for Modernization

The modernization analysis should eventually answer:

1. Which components represent the highest technical risk?
2. Which components should be modernized first?
3. Which components can remain unchanged?
4. Which components should move to managed cloud services?
5. Should the monolithic application be retained, modularized, or replaced?
6. How should the GPS data architecture change?
7. How should reporting and analytics be separated from operational workloads?
8. How should authentication and authorization be modernized?
9. How should APIs be redesigned?
10. How should asynchronous processing be introduced?
11. How should mobile synchronization be improved?
12. How should CI/CD be introduced?
13. How should monitoring and observability be implemented?
14. How should disaster recovery be improved?
15. What migration strategy minimizes operational risk?
16. What should the target architecture look like?
17. What should be migrated first?
18. What dependencies could block modernization?
19. What risks exist during coexistence of legacy and modern components?
20. What modernization roadmap should the company follow?

---

# 26. Important Note

This document describes a **fictional legacy system created for an AI/NotebookLM portfolio project**.

The architecture, technologies, numbers, limitations and business requirements are intentionally realistic but do not represent a real company's confidential system.

The purpose of the project is to demonstrate:

* technical documentation analysis
* source-grounded AI research
* requirements analysis
* architecture gap analysis
* modernization planning
* risk identification
* structured use of NotebookLM
