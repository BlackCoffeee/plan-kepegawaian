# Sequence Diagram - Employee Management

## 📋 Overview

Dokumen ini berisi sequence diagrams untuk alur manajemen pegawai dalam sistem SIMPEG (Sistem Informasi Manajemen Pegawai) Universitas Islam Negeri Sultan Maulana Hasanuddin.

## 🔄 Employee Registration Flow

### Sequence Diagram: Employee Registration

```mermaid
sequenceDiagram
    participant User as HR User
    participant UI as Web Interface
    participant API as API Controller
    participant Auth as Auth Service
    participant Emp as Employee Service
    participant Val as Validation Service
    participant DB as Database
    participant Cache as Cache Service
    participant Notif as Notification Service

    User->>UI: Access Employee Registration
    UI->>API: GET /employees/create
    API->>Auth: Validate User Session
    Auth-->>API: User Authenticated

    API->>Cache: Get Reference Data
    Cache-->>API: Return Religions, Educations, etc.
    API-->>UI: Return Form Data
    UI-->>User: Display Registration Form

    User->>UI: Fill Employee Data
    User->>UI: Submit Form
    UI->>API: POST /employees

    API->>Auth: Validate User Permission
    Auth-->>API: Permission Granted

    API->>Val: Validate Employee Data
    Val->>DB: Check NIK Uniqueness
    DB-->>Val: NIK Available
    Val->>DB: Check Email Uniqueness
    DB-->>Val: Email Available
    Val-->>API: Validation Passed

    API->>Emp: Create Employee
    Emp->>DB: Insert Employee Record
    DB-->>Emp: Employee Created (ID: EMP001)

    Emp->>Cache: Invalidate Employee Cache
    Emp->>Notif: Send Registration Notification
    Notif-->>Emp: Notification Sent

    Emp-->>API: Employee Created Successfully
    API-->>UI: Return Success Response
    UI-->>User: Display Success Message

    Note over User, Notif: Employee Registration Complete
```

### Sequence Diagram: Employee Update

```mermaid
sequenceDiagram
    participant User as HR User
    participant UI as Web Interface
    participant API as API Controller
    participant Auth as Auth Service
    participant Emp as Employee Service
    participant Val as Validation Service
    participant DB as Database
    participant Audit as Audit Service
    participant Cache as Cache Service

    User->>UI: Search Employee
    UI->>API: GET /employees?search=john
    API->>Auth: Validate User Session
    Auth-->>API: User Authenticated

    API->>Emp: Search Employees
    Emp->>DB: Query Employees
    DB-->>Emp: Return Employee List
    Emp-->>API: Employee List
    API-->>UI: Return Search Results
    UI-->>User: Display Employee List

    User->>UI: Select Employee
    UI->>API: GET /employees/EMP001
    API->>Emp: Get Employee Details
    Emp->>DB: Get Employee Data
    DB-->>Emp: Employee Data
    Emp-->>API: Employee Details
    API-->>UI: Return Employee Data
    UI-->>User: Display Employee Form

    User->>UI: Edit Employee Data
    User->>UI: Submit Changes
    UI->>API: PUT /employees/EMP001

    API->>Auth: Validate User Permission
    Auth-->>API: Permission Granted

    API->>Val: Validate Updated Data
    Val->>DB: Check Data Integrity
    DB-->>Val: Data Valid
    Val-->>API: Validation Passed

    API->>Emp: Update Employee
    Emp->>DB: Get Current Employee Data
    DB-->>Emp: Current Data

    Emp->>Audit: Log Changes
    Audit->>DB: Insert Audit Record
    DB-->>Audit: Audit Logged

    Emp->>DB: Update Employee Record
    DB-->>Emp: Employee Updated

    Emp->>Cache: Invalidate Employee Cache
    Emp-->>API: Employee Updated Successfully
    API-->>UI: Return Success Response
    UI-->>User: Display Success Message

    Note over User, Cache: Employee Update Complete
```

### Sequence Diagram: Employee Search and Filter

```mermaid
sequenceDiagram
    participant User as HR User
    participant UI as Web Interface
    participant API as API Controller
    participant Auth as Auth Service
    participant Emp as Employee Service
    participant DB as Database
    participant Cache as Cache Service

    User->>UI: Access Employee List
    UI->>API: GET /employees
    API->>Auth: Validate User Session
    Auth-->>API: User Authenticated

    API->>Cache: Check Employee List Cache
    Cache-->>API: Cache Miss

    API->>Emp: Get Employees List
    Emp->>DB: Query Employees with Pagination
    DB-->>Emp: Employee List + Total Count
    Emp-->>API: Paginated Employee List

    API->>Cache: Cache Employee List
    API-->>UI: Return Employee List
    UI-->>User: Display Employee List

    User->>UI: Apply Filters
    User->>UI: Search by Name/NIK
    UI->>API: GET /employees?search=john&department=1&status=active

    API->>Emp: Search Employees with Filters
    Emp->>DB: Query with Search Criteria
    DB-->>Emp: Filtered Results
    Emp-->>API: Filtered Employee List
    API-->>UI: Return Filtered Results
    UI-->>User: Display Filtered List

    User->>UI: Sort Results
    UI->>API: GET /employees?sort=name&order=asc
    API->>Emp: Sort Employees
    Emp->>DB: Query with Sort Criteria
    DB-->>Emp: Sorted Results
    Emp-->>API: Sorted Employee List
    API-->>UI: Return Sorted Results
    UI-->>User: Display Sorted List

    Note over User, Cache: Employee Search Complete
```

### Sequence Diagram: Employee Deactivation

```mermaid
sequenceDiagram
    participant User as HR User
    participant UI as Web Interface
    participant API as API Controller
    participant Auth as Auth Service
    participant Emp as Employee Service
    participant Val as Validation Service
    participant DB as Database
    participant Audit as Audit Service
    participant Notif as Notification Service

    User->>UI: Select Employee to Deactivate
    UI->>API: GET /employees/EMP001
    API->>Emp: Get Employee Details
    Emp->>DB: Get Employee Data
    DB-->>Emp: Employee Data
    Emp-->>API: Employee Details
    API-->>UI: Return Employee Data
    UI-->>User: Display Employee Details

    User->>UI: Click Deactivate Button
    UI->>User: Show Confirmation Dialog
    User->>UI: Confirm Deactivation
    UI->>API: DELETE /employees/EMP001

    API->>Auth: Validate User Permission
    Auth-->>API: Permission Granted

    API->>Val: Validate Deactivation
    Val->>DB: Check Employee Dependencies
    DB-->>Val: No Active Dependencies
    Val-->>API: Validation Passed

    API->>Emp: Deactivate Employee
    Emp->>DB: Get Current Employee Data
    DB-->>Emp: Current Data

    Emp->>Audit: Log Deactivation
    Audit->>DB: Insert Audit Record
    DB-->>Audit: Audit Logged

    Emp->>DB: Soft Delete Employee (deleted_at = now())
    DB-->>Emp: Employee Deactivated

    Emp->>Notif: Send Deactivation Notification
    Notif-->>Emp: Notification Sent

    Emp-->>API: Employee Deactivated Successfully
    API-->>UI: Return Success Response
    UI-->>User: Display Success Message

    Note over User, Notif: Employee Deactivation Complete
```

## 🔍 Employee Detail View Flow

### Sequence Diagram: View Employee Details

```mermaid
sequenceDiagram
    participant User as HR User
    participant UI as Web Interface
    participant API as API Controller
    participant Auth as Auth Service
    participant Emp as Employee Service
    participant DB as Database
    participant Cache as Cache Service
    participant Audit as Audit Service

    User->>UI: Click Employee Name
    UI->>API: GET /employees/EMP001
    API->>Auth: Validate User Session
    Auth-->>API: User Authenticated

    API->>Cache: Check Employee Cache
    Cache-->>API: Cache Miss

    API->>Emp: Get Employee Details
    Emp->>DB: Get Employee with Relations
    DB-->>Emp: Employee + Department + Position Data
    Emp-->>API: Complete Employee Data

    API->>Cache: Cache Employee Data
    API-->>UI: Return Employee Details
    UI-->>User: Display Employee Profile

    User->>UI: View Employee History
    UI->>API: GET /employees/EMP001/history
    API->>Audit: Get Employee History
    Audit->>DB: Query Employee Histories
    DB-->>Audit: History Records
    Audit-->>API: Employee History
    API-->>UI: Return History Data
    UI-->>User: Display Employee History

    User->>UI: View Structural History
    UI->>API: GET /employees/EMP001/structural-history
    API->>Audit: Get Structural History
    Audit->>DB: Query Structural Histories
    DB-->>Audit: Structural History Records
    Audit-->>API: Structural History
    API-->>UI: Return Structural History
    UI-->>User: Display Structural History

    Note over User, Audit: Employee Detail View Complete
```

## 📊 Bulk Operations Flow

### Sequence Diagram: Bulk Employee Import

```mermaid
sequenceDiagram
    participant User as HR User
    participant UI as Web Interface
    participant API as API Controller
    participant Auth as Auth Service
    participant Emp as Employee Service
    participant Val as Validation Service
    participant DB as Database
    participant Queue as Queue Service
    participant Notif as Notification Service

    User->>UI: Upload Employee File
    UI->>API: POST /employees/bulk-import
    API->>Auth: Validate User Permission
    Auth-->>API: Permission Granted

    API->>Val: Validate File Format
    Val-->>API: File Valid

    API->>Queue: Queue Bulk Import Job
    Queue-->>API: Job Queued (JobID: JOB001)
    API-->>UI: Return Job ID
    UI-->>User: Display Processing Message

    Note over Queue, Notif: Background Processing

    Queue->>Emp: Process Bulk Import
    Emp->>Val: Validate Each Employee
    Val->>DB: Check Data Integrity
    DB-->>Val: Validation Results

    loop For Each Employee
        Emp->>DB: Insert Employee Record
        DB-->>Emp: Employee Created
    end

    Emp->>Notif: Send Completion Notification
    Notif-->>User: Bulk Import Complete

    User->>UI: Check Job Status
    UI->>API: GET /jobs/JOB001/status
    API->>Queue: Get Job Status
    Queue-->>API: Job Status
    API-->>UI: Return Status
    UI-->>User: Display Results

    Note over User, Notif: Bulk Import Complete
```

## 🔧 Error Handling Flows

### Sequence Diagram: Employee Validation Error

```mermaid
sequenceDiagram
    participant User as HR User
    participant UI as Web Interface
    participant API as API Controller
    participant Val as Validation Service
    participant DB as Database

    User->>UI: Submit Invalid Employee Data
    UI->>API: POST /employees

    API->>Val: Validate Employee Data
    Val->>DB: Check NIK Uniqueness
    DB-->>Val: NIK Already Exists
    Val-->>API: Validation Failed (NIK duplicate)

    API-->>UI: Return Validation Error
    UI-->>User: Display Error Message

    Note over User, DB: Validation Error Handled
```

### Sequence Diagram: Employee Not Found Error

```mermaid
sequenceDiagram
    participant User as HR User
    participant UI as Web Interface
    participant API as API Controller
    participant Emp as Employee Service
    participant DB as Database

    User->>UI: Access Non-existent Employee
    UI->>API: GET /employees/INVALID_ID

    API->>Emp: Get Employee Details
    Emp->>DB: Query Employee
    DB-->>Emp: Employee Not Found
    Emp-->>API: Employee Not Found

    API-->>UI: Return 404 Error
    UI-->>User: Display Not Found Message

    Note over User, DB: Not Found Error Handled
```

## 🎯 Key Interactions

### 1. Authentication Flow

- Setiap request memerlukan validasi session
- Permission check untuk operasi yang memerlukan authorization
- JWT token validation untuk API requests

### 2. Data Validation Flow

- Input validation sebelum processing
- Database constraint validation
- Business rule validation
- Reference data validation

### 3. Audit Trail Flow

- Log semua perubahan data
- Capture old dan new values
- Track user yang melakukan perubahan
- Timestamp semua activities

### 4. Cache Management Flow

- Cache employee data untuk performance
- Invalidate cache saat data berubah
- Cache reference data
- Cache search results

### 5. Notification Flow

- Send notification untuk important events
- Email notification untuk user
- System notification untuk administrators
- Real-time notification untuk critical events

## 📋 Sequence Diagram Best Practices

### 1. Message Naming

- Use clear, descriptive message names
- Include HTTP methods untuk API calls
- Specify data types dan parameters
- Use consistent naming conventions

### 2. Error Handling

- Include error scenarios dalam diagrams
- Show error messages dan responses
- Display error recovery flows
- Handle timeout scenarios

### 3. Performance Considerations

- Show caching mechanisms
- Include pagination untuk large datasets
- Display background processing
- Show optimization techniques

### 4. Security Considerations

- Include authentication flows
- Show authorization checks
- Display permission validations
- Include audit logging

---

**Dokumen ini dibuat**: 2024-01-01  
**Versi**: v1.0.0  
**Status**: Complete
