# Hospital Management System (HMS)
## Software Requirements Specification (SRS)

**Document Version**: 1.0  
**Project Type**: University Software Engineering Project  
**Technology Stack**: Python 3.14 / Flask 3.1 / SQLAlchemy / SQLite & MySQL / Bootstrap 5 / Chart.js  
**Document Status**: Approved Final Draft  
**Standard**: IEEE Std 830-1998 Recommended Practice for Software Requirements Specifications  

---

## 1. Introduction

### 1.1 Purpose
The purpose of this Software Requirements Specification (SRS) is to provide a complete, formal, and authoritative definition of the **Hospital Management System (HMS)**. This document specifies both functional and non-functional requirements, system architecture, role-based access boundaries, clinical workflows, and verification procedures for evaluators, software engineers, and university project supervisors.

### 1.2 Scope
The Hospital Management System is an enterprise-grade medical informatics web platform engineered to automate outpatient clinic operations, hospital admissions, multidisciplinary clinical consultations, diagnostic laboratory workflows, pharmaceutical prescriptions, and consolidated patient billing.

**Key capabilities include:**
- Automated patient intake and electronic medical records (EMR).
- Conflict-free doctor appointment scheduling with algorithmic double-booking prevention.
- Clinical consultation documentation with vital signs monitoring.
- Dynamic multi-item prescription generation with official print layouts.
- Laboratory diagnostic assay ordering, sample tracking, and technologist result entry.
- Consolidated billing calculating consultation, laboratory, and pharmacy charges with payment processing.
- Multi-role dashboard analytics and immutable security audit logging.

### 1.3 Intended Audience
- **Academic Supervisors & Examiners**: For grading against software engineering standards, requirement traceability, and operational completeness.
- **Developers & System Architects**: For system maintenance, database schema migration, and API integration.
- **Hospital End-Users**: System Administrators, Physicians, Receptionists, Laboratory Technologists, Patients, and Executive Management.

### 1.4 Definitions, Acronyms, and Abbreviations
- **HMS**: Hospital Management System
- **SRS**: Software Requirements Specification
- **EMR**: Electronic Medical Record
- **MRN**: Medical Record Number (Unique Patient Identification)
- **RBAC**: Role-Based Access Control
- **PBKDF2**: Password-Based Key Derivation Function 2
- **CBC**: Complete Blood Count
- **UAT**: User Acceptance Testing
- **KPI**: Key Performance Indicator

### 1.5 Document Conventions
- Requirements are classified as **FR** (Functional Requirement) or **NFR** (Non-Functional Requirement).
- Each requirement has a unique alphanumeric ID (e.g., `FR-AUTH-001`, `FR-APT-002`).
- Priorities follow standard MoSCoW notation (Must have, Should have, Could have).

### 1.6 References
- IEEE Std 830-1998: IEEE Recommended Practice for Software Requirements Specifications.
- Health Insurance Portability and Accountability Act (HIPAA) Security and Privacy Guidelines.
- Flask 3.1.x & SQLAlchemy 2.0 Documentation.

---

## 2. Overall Description

### 2.1 Product Perspective
The HMS is a self-contained, multi-tenant capable, web-based healthcare management application. It operates under a Model-View-Controller (MVC) architectural pattern:
- **Model**: SQLAlchemy ORM entities mapping to relational database storage (SQLite / MySQL / PostgreSQL).
- **View**: Jinja2 responsive templates styled with Bootstrap 5, FontAwesome, and Chart.js.
- **Controller**: Flask Blueprints providing RESTful endpoint routing, session management, and RBAC enforcement.

### 2.2 Product Functions
1. **Identity & Access Management**: Secure credential authentication, role gating, and session lifecycle control.
2. **Outpatient Intake**: Demographic registration, contact information, and emergency contact profiling.
3. **Appointment Scheduling**: Physician availability lookup, slot generation, and real-time conflict prevention.
4. **Clinical Consultation**: Physician assessment, vital sign capture, and diagnostic decision recording.
5. **Pharmaceutical Management**: Dynamic prescription builder with dosage, frequency, duration, and instructions.
6. **Diagnostic Laboratory Workstation**: Requisition queue, specimen management, and reference range validation.
7. **Consolidated Billing & Revenue Management**: Automated invoice consolidation, co-pay calculation, and receipts.
8. **Executive Analytics**: Real-time KPI dashboards, departmental loads, and revenue recovery visualization.
9. **Forensic Audit Logging**: Continuous tracking of all sensitive transactions and access attempts.

### 2.3 User Classes and Characteristics
The system defines six mutually exclusive user classes:
1. **System Administrator**: Full administrative privileges, user provisioning, security audit monitoring.
2. **Doctor**: Licensed physician conducting consultations, establishing diagnoses, issuing prescriptions, ordering lab tests.
3. **Receptionist**: Front-desk outpatient intake, appointment scheduling, and patient invoice payment collection.
4. **Laboratory Staff**: Certified medical technologist processing diagnostic tests and logging assay findings.
5. **Patient**: Registered healthcare consumer viewing personal appointments, medical records, prescriptions, and bills.
6. **Hospital Management**: Executive director monitoring financial health, department efficiency, and hospital volume.

### 2.4 Operating Environment
- **Server OS**: Cross-platform (Windows 10/11, Linux Ubuntu/Debian, macOS).
- **Runtime**: Python 3.10+ (tested on Python 3.14).
- **Database**: SQLite 3 (default for local zero-configuration deployment); MySQL 8.0+ or PostgreSQL 14+ supported via standard connection strings.
- **Client**: Any modern web browser (Google Chrome, Mozilla Firefox, Microsoft Edge, Safari) on desktop or tablet.

### 2.5 Design and Implementation Constraints
- Python Werkzeug cryptographic hashing required for all stored credentials.
- Zero client-side framework requirement (vanilla JavaScript with standard Bootstrap 5) to guarantee lightweight footprint and high reliability.
- Database access must strictly utilize SQLAlchemy ORM with parameterized queries to prevent SQL injection vulnerabilities.

### 2.6 Assumptions and Dependencies
- Network connectivity between client browser and Flask web application server.
- Hospital staff have basic computer and browser literacy.

---

## 3. System Features and Functional Requirements

### 3.1 Authentication & Access Control (FR-AUTH)
- **`FR-AUTH-001` User Authentication**: The system shall provide a secure authentication mechanism allowing registered users to access the system using valid username/email and password credentials.
- **`FR-AUTH-002` Role-Based Access Control**: The system shall restrict access to system functions according to the authenticated user's assigned role. Unauthorized requests shall be blocked with HTTP 403 Forbidden and logged to the security audit trail.
- **`FR-AUTH-003` Session Management**: The system shall maintain authenticated user state via secure HTTP-only session cookies and support explicit logout terminating all session data.

### 3.2 Patient Management (FR-PAT)
- **`FR-PAT-001` Patient Registration**: The system shall allow authorized staff members (Receptionist, Admin) to register a new patient by entering personal (name, DOB, gender, blood group), contact (phone, email, residential address), and emergency contact (name, phone) information.
- **`FR-PAT-002` Unique Patient Identification**: The system shall automatically generate a unique, non-repeating Medical Record Number (MRN) format `PAT-YYYY-XXXX`.
- **`FR-PAT-003` Patient Dossier View**: The system shall provide a 360-degree patient file displaying demographics, medical background, visit history, prescriptions, lab reports, and billing ledger.

### 3.3 Appointment Scheduling (FR-APT)
- **`FR-APT-001` Appointment Scheduling**: The system shall allow authorized users to schedule an appointment by selecting an available doctor, calendar date, and time slot.
- **`FR-APT-002` Appointment Conflict Prevention**: The system shall prevent the creation of overlapping appointments for the same doctor and time slot. If an overlapping booking is attempted, the system shall reject the request and prompt the user to select an alternative slot.
- **`FR-APT-003` Dynamic Slot Generation**: The system shall dynamically compute open 30-minute consultation slots based on the doctor's working days, start time, end time, and existing bookings.

### 3.4 Medical Records & Consultations (FR-MED)
- **`FR-MED-001` Consultation Documentation**: The system shall allow the attending doctor to document vital signs (blood pressure, pulse rate, temperature, weight), presenting complaints, definitive diagnosis, and treatment plan.
- **`FR-MED-002` Physician Exclusivity**: Only authorized doctors and administrators shall have permission to create or modify clinical medical records.

### 3.5 Prescription Management (FR-RX)
- **`FR-RX-001` Multi-Item Prescription**: The system shall allow the attending doctor to issue prescriptions containing multiple medication line items (medicine name, dosage, frequency, duration, special instructions).
- **`FR-RX-002` Printable Prescription**: The system shall generate an official printable prescription sheet conforming to clinical standards with hospital header and doctor's signature line.

### 3.6 Laboratory Management (FR-LAB)
- **`FR-LAB-001` Diagnostic Requisition**: The system shall allow physicians to order diagnostic laboratory tests from a standardized catalog.
- **`FR-LAB-002` Result Documentation**: Laboratory staff shall be authorized to enter observed laboratory results, compare findings with standard reference ranges, add interpretative remarks, and finalize the report.
- **`FR-LAB-003` Diagnosis Protection**: Laboratory staff shall not have permission to alter or overwrite the consulting doctor's medical diagnosis.

### 3.7 Billing & Payments (FR-BIL)
- **`FR-BIL-001` Consolidated Invoicing**: The system shall consolidate physician consultation fees, diagnostic laboratory charges, and pharmacy dispensary items into a single itemized patient invoice.
- **`FR-BIL-002` Payment Recording**: The system shall record payment transactions against invoices with support for multiple tender methods (Cash, Credit Card, Debit Card, Health Insurance, Online Transfer).
- **`FR-BIL-003` Balance Tracking**: The system shall dynamically compute paid totals and outstanding balance due, automatically updating invoice status (Unpaid, Partially Paid, Paid).

### 3.8 Reporting, Analytics & Administration (FR-ADM)
- **`FR-ADM-001` Executive Dashboard**: The system shall provide executive management with live KPI counters (total patients, doctors, visits, revenue billed, revenue collected).
- **`FR-ADM-002` Visual Reporting**: The system shall render interactive charts depicting appointment outcomes, departmental utilization, and financial recovery ratios.
- **`FR-ADM-003` Security Audit Trail**: The system shall log all sensitive transactions, authentication events, conflicts, and unauthorized access attempts to an immutable audit ledger.

---

## 4. External Interface Requirements

### 4.1 User Interface Requirements
- Modern, clean, hospital-themed responsive interface built on Bootstrap 5.3.
- Distinct color-coded badges for roles and statuses.
- Fully formatted `@media print` stylesheets for official prescriptions, lab reports, and billing invoices.

### 4.2 Software Interface Requirements
- Database: SQLAlchemy ORM layer interfacing with SQLite 3 (or MySQL via PyMySQL driver).
- Frontend libraries: FontAwesome 6 (icons), Chart.js (analytics).

### 4.3 Communication Interface Requirements
- Secure HTTP/HTTPS client-server communication.
- RESTful JSON endpoints for asynchronous client interactions (`/appointments/api/available-slots`, `/analytics/api/stats`).

---

## 5. Non-Functional Requirements (NFR)

- **`NFR-SEC-001` Data Security**: Passwords stored using PBKDF2 with SHA-256 hash algorithms. Patient medical records accessible only by attending medical staff and the individual patient.
- **`NFR-PER-001` Performance**: Standard database read/write queries and page rendering shall complete within 200 milliseconds under normal load conditions.
- **`NFR-USA-001` Usability**: Intuitive user interface enabling hospital receptionists and clinic nurses with basic computer knowledge to register patients and book appointments with zero training.
- **`NFR-REL-001` Reliability & Integrity**: ACID transactional guarantees enforced by SQLAlchemy database sessions with automatic rollbacks on unhandled errors.
- **`NFR-MAI-001` Maintainability**: Modular blueprint architecture separating routing, business logic, data models, and presentation templates.

---

## 6. Formal User Roles Matrix

| System Role | Portal Route | Permissions |
| :--- | :--- | :--- |
| **System Administrator** | `/admin/` | Full system access, user creation, role assignment, audit logs |
| **Doctor** | `/dashboard` (Doctor Hub) | Consultation, diagnosis, prescription, lab orders, doctor profile |
| **Receptionist** | `/dashboard` (Desk) | Patient intake, appointment booking, invoice generation, payment cashier |
| **Laboratory Staff** | `/dashboard` (Lab Station) | View test queue, enter diagnostic findings, finalize lab reports |
| **Patient** | `/dashboard` (Health Portal) | View personal visits, prescriptions, lab results, and invoices |
| **Hospital Management**| `/dashboard` (Executive) | High-level analytics, revenue figures, department volume metrics |

---

## 7. System Workflow (SRS Section 10)

The core clinical outpatient lifecycle proceeds through the following sequential and parallel stages:

```
[ 1. Patient Registration ]
            │
            ▼
[ 2. Appointment Booking (Conflict Prevention FR-APT-002) ]
            │
            ▼
[ 3. Doctor Consultation (Vitals & Clinical Assessment) ]
            │
            ▼
[ 4. Medical Record Creation (Definitive Diagnosis) ]
            │
      ┌─────┴─────────────────────────┐
      ▼                               ▼
[ 5a. Prescription (Rx) ]     [ 5b. Diagnostic Lab Order ]
      │                               │
      │                               ▼
      │                       [ 6. Lab Specimen & Results ]
      │                               │
      └──────────────┬────────────────┘
                     ▼
[ 7. Consolidated Patient Invoicing ]
                     │
                     ▼
[ 8. Payment Processing & Official Receipt ]
                     │
                     ▼
[ 9. Hospital Analytics & Audit Log Archive ]
```

---

## 8. Use Case Specifications (SRS Section 8)

### Use Case UC-APT-001: Book Appointment
- **Primary Actor**: Patient / Receptionist
- **Precondition**: Patient is registered in the system; selected doctor is active.
- **Main Flow**:
  1. User navigates to Appointment Booking module.
  2. User selects patient, doctor, and date.
  3. System dynamically fetches and displays doctor's available 30-minute time slots.
  4. User selects preferred time slot and enters visit reason.
  5. User submits booking form.
  6. System validates slot availability (`FR-APT-002`).
  7. System creates appointment record with unique appointment number.
  8. System displays confirmation message and updates doctor's schedule.
- **Alternative Flow (Conflict Detected)**:
  - If the doctor is booked for the selected slot, system displays conflict warning and prevents commit. User selects alternate slot.

### Use Case UC-MED-001: Doctor Consultation
- **Primary Actor**: Doctor
- **Precondition**: Patient has a scheduled appointment.
- **Main Flow**:
  1. Doctor selects patient from daily queue.
  2. Doctor records vital signs (blood pressure, pulse, temperature, weight).
  3. Doctor enters symptoms, physical exam findings, and diagnosis.
  4. System records consultation and marks appointment completed.
  5. System unlocks immediate options to issue prescription or order lab tests.

### Use Case UC-LAB-001: Laboratory Analysis
- **Primary Actor**: Laboratory Staff
- **Precondition**: Physician has ordered a diagnostic test.
- **Main Flow**:
  1. Technologist views active test orders queue.
  2. Technologist collects specimen and updates status to Sample Collected.
  3. Technologist performs analysis and enters observed finding and reference range.
  4. System finalizes report, stamps technologist credentials, and alerts physician/patient.

### Use Case UC-BIL-001: Consolidated Billing
- **Primary Actor**: Receptionist / Billing Clerk
- **Precondition**: Patient consultation or lab tests completed.
- **Main Flow**:
  1. Staff opens invoice generator for patient appointment.
  2. System pre-populates doctor consultation fee and lab test charges.
  3. Staff applies any discount or tax and generates invoice.
  4. Patient tenders payment (Cash/Card); staff records transaction.
  5. System updates balance due to $0.00, marks Paid, and issues printable receipt.

---

## 9. Business Rules Compliance Matrix (SRS Section 11)

| Rule # | Business Rule Specification | System Implementation & Verification |
| :--- | :--- | :--- |
| **BR-1** | A doctor cannot have two appointments at the same time. | Enforced in `routes/appointment_routes.py` and tested in `test_appointment_scheduling_and_conflict_prevention`. |
| **BR-2** | Only authorized doctors can create or modify medical records. | Gated by `@roles_required('Doctor', 'Admin')` in `routes/medical_record_routes.py`. |
| **BR-3** | Patients can view their own medical information only. | Enforced in `patient_routes.py`, `medical_record_routes.py`, `billing_routes.py` with ownership checks. Tested in `test_business_rules_patient_privacy`. |
| **BR-4** | Laboratory staff can update laboratory results but cannot modify a doctor's diagnosis. | Technologist interface permits editing only `test_result`, `reference_range`, and `comments`. Doctor diagnosis field is strictly read-only and immutable in lab routes. |
| **BR-5** | Only authorized staff can generate or modify billing records. | Gated by `@roles_required('Admin', 'Receptionist', 'Hospital Management')` in `routes/billing_routes.py`. |
| **BR-6** | Deleted records should follow defined data-retention rules. | Soft deactivation flag `is_active=False` preserves historical medical data and audit continuity. |

---

## 10. Acceptance Criteria (SRS Section 14)

The Hospital Management System project is officially accepted when:
1. All critical modules (Authentication, Patients, Doctors, Departments, Appointments, Medical Records, Prescriptions, Laboratory, Billing, Analytics, Administration) are fully functional.
2. The complete patient-to-billing workflow executes seamlessly from registration to payment.
3. Unauthorized access attempts are blocked and logged.
4. The automated test suite executes with 100% pass rate.
