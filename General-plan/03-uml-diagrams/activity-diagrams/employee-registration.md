# Activity Diagram - Employee Registration

## 📋 Overview

Dokumen ini berisi Activity Diagram untuk proses registrasi pegawai dalam sistem SIMPEG (Sistem Informasi Manajemen Pegawai) Universitas Islam Negeri Sultan Maulana Hasanuddin.

## 🎯 Activity Diagram: Employee Registration Process

### Complete Employee Registration Flow

```mermaid
flowchart TD
    Start([Start]) --> Login{User Login}
    Login -->|Not Logged In| LoginPage[Login Page]
    LoginPage --> Login
    Login -->|Logged In| CheckPermission{Check Permission}

    CheckPermission -->|No Permission| AccessDenied[Access Denied]
    AccessDenied --> End([End])

    CheckPermission -->|Has Permission| AccessForm[Access Employee Registration Form]
    AccessForm --> LoadRefData[Load Reference Data]
    LoadRefData --> DisplayForm[Display Registration Form]

    DisplayForm --> FillForm[Fill Employee Data]
    FillForm --> ValidateInput{Validate Input Data}

    ValidateInput -->|Invalid| ShowErrors[Show Validation Errors]
    ShowErrors --> FillForm

    ValidateInput -->|Valid| CheckUniqueness{Check NIK & Email Uniqueness}

    CheckUniqueness -->|NIK Exists| NIKError[NIK Already Exists Error]
    NIKError --> FillForm

    CheckUniqueness -->|Email Exists| EmailError[Email Already Exists Error]
    EmailError --> FillForm

    CheckUniqueness -->|Unique| SaveEmployee[Save Employee Data]
    SaveEmployee --> GenerateID[Generate Employee ID]
    GenerateID --> CreateAuditLog[Create Audit Log Entry]
    CreateAuditLog --> UpdateCache[Update Cache]
    UpdateCache --> SendNotification[Send Registration Notification]

    SendNotification --> SuccessMessage[Display Success Message]
    SuccessMessage --> End

    %% Error Handling
    SaveEmployee -->|Database Error| DBError[Database Error]
    DBError --> ErrorMessage[Display Error Message]
    ErrorMessage --> FillForm

    SendNotification -->|Notification Error| NotifError[Notification Error]
    NotifError --> LogError[Log Error]
    LogError --> SuccessMessage

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef error fill:#ffebee

    class Start,End startEnd
    class AccessForm,LoadRefData,DisplayForm,FillForm,SaveEmployee,GenerateID,CreateAuditLog,UpdateCache,SendNotification,SuccessMessage,LoginPage process
    class Login,CheckPermission,ValidateInput,CheckUniqueness decision
    class AccessDenied,ShowErrors,NIKError,EmailError,DBError,ErrorMessage,NotifError,LogError error
```

## 🔄 Activity Diagram: Employee Registration with Approval Workflow

### Employee Registration with Manager Approval

```mermaid
flowchart TD
    Start([Start]) --> Login{User Login}
    Login -->|Not Logged In| LoginPage[Login Page]
    LoginPage --> Login
    Login -->|Logged In| CheckRole{Check User Role}

    CheckRole -->|HR Staff| StaffRegistration[HR Staff Registration Process]
    CheckRole -->|HR Admin| AdminRegistration[HR Admin Registration Process]

    %% HR Staff Process
    StaffRegistration --> FillFormStaff[Fill Employee Form]
    FillFormStaff --> ValidateStaff[Validate Data]
    ValidateStaff -->|Invalid| ShowErrorsStaff[Show Errors]
    ShowErrorsStaff --> FillFormStaff
    ValidateStaff -->|Valid| SubmitForApproval[Submit for Manager Approval]
    SubmitForApproval --> NotifyManager[Notify Department Manager]
    NotifyManager --> WaitApproval[Wait for Approval]

    WaitApproval --> ManagerReview{Manager Review}
    ManagerReview -->|Approve| Approved[Approved by Manager]
    ManagerReview -->|Reject| Rejected[Rejected by Manager]
    ManagerReview -->|Request Changes| RequestChanges[Request Changes]

    Rejected --> NotifyRejection[Notify HR Staff of Rejection]
    NotifyRejection --> FillFormStaff

    RequestChanges --> NotifyChanges[Notify HR Staff of Changes]
    NotifyChanges --> FillFormStaff

    Approved --> SaveEmployeeStaff[Save Employee Data]
    SaveEmployeeStaff --> CreateAuditStaff[Create Audit Log]
    CreateAuditStaff --> NotifyCompletion[Notify Registration Complete]

    %% HR Admin Process
    AdminRegistration --> FillFormAdmin[Fill Employee Form]
    FillFormAdmin --> ValidateAdmin[Validate Data]
    ValidateAdmin -->|Invalid| ShowErrorsAdmin[Show Errors]
    ShowErrorsAdmin --> FillFormAdmin
    ValidateAdmin -->|Valid| SaveEmployeeAdmin[Save Employee Data Directly]
    SaveEmployeeAdmin --> CreateAuditAdmin[Create Audit Log]
    CreateAuditAdmin --> NotifyCompletionAdmin[Notify Registration Complete]

    %% Common End
    NotifyCompletion --> End([End])
    NotifyCompletionAdmin --> End

    %% Error Handling
    SaveEmployeeStaff -->|Error| ErrorStaff[Handle Error]
    SaveEmployeeAdmin -->|Error| ErrorAdmin[Handle Error]
    ErrorStaff --> FillFormStaff
    ErrorAdmin --> FillFormAdmin

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef approval fill:#e8f5e8
    classDef error fill:#ffebee

    class Start,End startEnd
    class StaffRegistration,AdminRegistration,FillFormStaff,FillFormAdmin,ValidateStaff,ValidateAdmin,SubmitForApproval,NotifyManager,WaitApproval,Approved,SaveEmployeeStaff,SaveEmployeeAdmin,CreateAuditStaff,CreateAuditAdmin,NotifyCompletion,NotifyCompletionAdmin,LoginPage process
    class Login,CheckRole,ManagerReview decision
    class Rejected,RequestChanges,NotifyRejection,NotifyChanges approval
    class ShowErrorsStaff,ShowErrorsAdmin,ErrorStaff,ErrorAdmin error
```

## 📝 Activity Diagram: Employee Registration Data Validation

### Detailed Data Validation Process

```mermaid
flowchart TD
    Start([Start Validation]) --> ValidateNIK{Validate NIK}
    ValidateNIK -->|Invalid Format| NIKFormatError[NIK Format Error]
    ValidateNIK -->|Invalid Length| NIKLengthError[NIK Length Error]
    ValidateNIK -->|Valid Format| CheckNIKExists{Check NIK Exists}

    CheckNIKExists -->|Exists| NIKExistsError[NIK Already Exists]
    CheckNIKExists -->|Not Exists| ValidateEmail{Validate Email}

    ValidateEmail -->|Invalid Format| EmailFormatError[Email Format Error]
    ValidateEmail -->|Valid Format| CheckEmailExists{Check Email Exists}

    CheckEmailExists -->|Exists| EmailExistsError[Email Already Exists]
    CheckEmailExists -->|Not Exists| ValidatePersonalData{Validate Personal Data}

    ValidatePersonalData -->|Invalid Name| NameError[Name Validation Error]
    ValidatePersonalData -->|Invalid Birth Date| BirthDateError[Birth Date Error]
    ValidatePersonalData -->|Invalid Gender| GenderError[Gender Validation Error]
    ValidatePersonalData -->|Valid| ValidateReferences{Validate Reference Data}

    ValidateReferences -->|Invalid Religion| ReligionError[Religion Not Found]
    ValidateReferences -->|Invalid Education| EducationError[Education Not Found]
    ValidateReferences -->|Invalid Field of Study| FieldError[Field of Study Not Found]
    ValidateReferences -->|Invalid Status| StatusError[Employment Status Not Found]
    ValidateReferences -->|Valid| ValidateAddress{Validate Address}

    ValidateAddress -->|Invalid Address| AddressError[Address Validation Error]
    ValidateAddress -->|Valid| ValidatePhone{Validate Phone Number}

    ValidatePhone -->|Invalid Format| PhoneError[Phone Format Error]
    ValidatePhone -->|Valid| ValidationComplete[All Validations Passed]

    %% Error Handling
    NIKFormatError --> CollectErrors[Collect All Errors]
    NIKLengthError --> CollectErrors
    NIKExistsError --> CollectErrors
    EmailFormatError --> CollectErrors
    EmailExistsError --> CollectErrors
    NameError --> CollectErrors
    BirthDateError --> CollectErrors
    GenderError --> CollectErrors
    ReligionError --> CollectErrors
    EducationError --> CollectErrors
    FieldError --> CollectErrors
    StatusError --> CollectErrors
    AddressError --> CollectErrors
    PhoneError --> CollectErrors

    CollectErrors --> ShowAllErrors[Show All Validation Errors]
    ShowAllErrors --> End([End - Validation Failed])

    ValidationComplete --> EndSuccess([End - Validation Success])

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef error fill:#ffebee
    classDef success fill:#e8f5e8

    class Start,End,EndSuccess startEnd
    class NIKFormatError,NIKLengthError,NIKExistsError,EmailFormatError,EmailExistsError,NameError,BirthDateError,GenderError,ReligionError,EducationError,FieldError,StatusError,AddressError,PhoneError,CollectErrors,ShowAllErrors process
    class ValidateNIK,CheckNIKExists,ValidateEmail,CheckEmailExists,ValidatePersonalData,ValidateReferences,ValidateAddress,ValidatePhone decision
    class ValidationComplete success
```

## 🔔 Activity Diagram: Employee Registration Notification Workflow

### Notification Process for Employee Registration

```mermaid
flowchart TD
    Start([Registration Complete]) --> DetermineRecipients{Determine Notification Recipients}

    DetermineRecipients --> NotifyHR[Notify HR Team]
    DetermineRecipients --> NotifyManager[Notify Department Manager]
    DetermineRecipients --> NotifyEmployee[Notify New Employee]
    DetermineRecipients --> NotifyIT[Notify IT Department]

    %% HR Team Notification
    NotifyHR --> SendHREmail[Send Email to HR Team]
    SendHREmail --> SendHRSMS[Send SMS to HR Team]
    SendHRSMS --> LogHRNotification[Log HR Notification]

    %% Manager Notification
    NotifyManager --> CheckManagerEmail{Manager Email Available?}
    CheckManagerEmail -->|Yes| SendManagerEmail[Send Email to Manager]
    CheckManagerEmail -->|No| SkipManagerEmail[Skip Manager Email]
    SendManagerEmail --> LogManagerNotification[Log Manager Notification]
    SkipManagerEmail --> LogManagerNotification

    %% Employee Notification
    NotifyEmployee --> CheckEmployeeContact{Employee Contact Available?}
    CheckEmployeeContact -->|Email Available| SendEmployeeEmail[Send Welcome Email]
    CheckEmployeeContact -->|Phone Available| SendEmployeeSMS[Send Welcome SMS]
    CheckEmployeeContact -->|No Contact| SkipEmployeeNotification[Skip Employee Notification]
    SendEmployeeEmail --> LogEmployeeNotification[Log Employee Notification]
    SendEmployeeSMS --> LogEmployeeNotification
    SkipEmployeeNotification --> LogEmployeeNotification

    %% IT Department Notification
    NotifyIT --> SendITEmail[Send Email to IT Department]
    SendITEmail --> CreateUserAccount[Create System User Account]
    CreateUserAccount --> SendCredentials[Send Login Credentials]
    SendCredentials --> LogITNotification[Log IT Notification]

    %% Consolidate Logs
    LogHRNotification --> ConsolidateLogs[Consolidate Notification Logs]
    LogManagerNotification --> ConsolidateLogs
    LogEmployeeNotification --> ConsolidateLogs
    LogITNotification --> ConsolidateLogs

    ConsolidateLogs --> UpdateNotificationStatus[Update Notification Status]
    UpdateNotificationStatus --> End([Notification Complete])

    %% Error Handling
    SendHREmail -->|Error| HandleHRError[Handle HR Notification Error]
    SendManagerEmail -->|Error| HandleManagerError[Handle Manager Notification Error]
    SendEmployeeEmail -->|Error| HandleEmployeeError[Handle Employee Notification Error]
    SendEmployeeSMS -->|Error| HandleEmployeeError
    SendITEmail -->|Error| HandleITError[Handle IT Notification Error]
    CreateUserAccount -->|Error| HandleAccountError[Handle Account Creation Error]

    HandleHRError --> LogError[Log Notification Error]
    HandleManagerError --> LogError
    HandleEmployeeError --> LogError
    HandleITError --> LogError
    HandleAccountError --> LogError
    LogError --> ConsolidateLogs

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef notification fill:#e3f2fd
    classDef error fill:#ffebee

    class Start,End startEnd
    class NotifyHR,NotifyManager,NotifyEmployee,NotifyIT,SendHREmail,SendHRSMS,LogHRNotification,SendManagerEmail,SkipManagerEmail,LogManagerNotification,SendEmployeeEmail,SendEmployeeSMS,SkipEmployeeNotification,LogEmployeeNotification,SendITEmail,CreateUserAccount,SendCredentials,LogITNotification,ConsolidateLogs,UpdateNotificationStatus process
    class DetermineRecipients,CheckManagerEmail,CheckEmployeeContact decision
    class HandleHRError,HandleManagerError,HandleEmployeeError,HandleITError,HandleAccountError,LogError error
```

## 📊 Activity Diagram: Employee Registration Reporting

### Registration Statistics and Reporting

```mermaid
flowchart TD
    Start([Registration Complete]) --> UpdateStats[Update Registration Statistics]
    UpdateStats --> CheckReportingPeriod{Check Reporting Period}

    CheckReportingPeriod -->|Daily| GenerateDailyReport[Generate Daily Registration Report]
    CheckReportingPeriod -->|Weekly| GenerateWeeklyReport[Generate Weekly Registration Report]
    CheckReportingPeriod -->|Monthly| GenerateMonthlyReport[Generate Monthly Registration Report]
    CheckReportingPeriod -->|No Reporting| SkipReporting[Skip Reporting]

    %% Daily Report
    GenerateDailyReport --> CollectDailyData[Collect Daily Registration Data]
    CollectDailyData --> FormatDailyReport[Format Daily Report]
    FormatDailyReport --> SendDailyReport[Send Daily Report to HR Manager]
    SendDailyReport --> LogDailyReport[Log Daily Report Sent]

    %% Weekly Report
    GenerateWeeklyReport --> CollectWeeklyData[Collect Weekly Registration Data]
    CollectWeeklyData --> FormatWeeklyReport[Format Weekly Report]
    FormatWeeklyReport --> SendWeeklyReport[Send Weekly Report to HR Director]
    SendWeeklyReport --> LogWeeklyReport[Log Weekly Report Sent]

    %% Monthly Report
    GenerateMonthlyReport --> CollectMonthlyData[Collect Monthly Registration Data]
    CollectMonthlyData --> FormatMonthlyReport[Format Monthly Report]
    FormatMonthlyReport --> SendMonthlyReport[Send Monthly Report to Management]
    SendMonthlyReport --> LogMonthlyReport[Log Monthly Report Sent]

    %% Skip Reporting
    SkipReporting --> UpdateMetrics[Update System Metrics]

    %% Consolidate Logs
    LogDailyReport --> UpdateMetrics
    LogWeeklyReport --> UpdateMetrics
    LogMonthlyReport --> UpdateMetrics

    UpdateMetrics --> UpdateDashboard[Update Registration Dashboard]
    UpdateDashboard --> End([Reporting Complete])

    %% Error Handling
    CollectDailyData -->|Error| HandleDailyError[Handle Daily Report Error]
    CollectWeeklyData -->|Error| HandleWeeklyError[Handle Weekly Report Error]
    CollectMonthlyData -->|Error| HandleMonthlyError[Handle Monthly Report Error]
    SendDailyReport -->|Error| HandleSendError[Handle Send Error]
    SendWeeklyReport -->|Error| HandleSendError
    SendMonthlyReport -->|Error| HandleSendError

    HandleDailyError --> LogReportError[Log Report Error]
    HandleWeeklyError --> LogReportError
    HandleMonthlyError --> LogReportError
    HandleSendError --> LogReportError
    LogReportError --> UpdateMetrics

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef report fill:#f1f8e9
    classDef error fill:#ffebee

    class Start,End startEnd
    class UpdateStats,CollectDailyData,FormatDailyReport,SendDailyReport,LogDailyReport,CollectWeeklyData,FormatWeeklyReport,SendWeeklyReport,LogWeeklyReport,CollectMonthlyData,FormatMonthlyReport,SendMonthlyReport,LogMonthlyReport,SkipReporting,UpdateMetrics,UpdateDashboard process
    class CheckReportingPeriod decision
    class GenerateDailyReport,GenerateWeeklyReport,GenerateMonthlyReport report
    class HandleDailyError,HandleWeeklyError,HandleMonthlyError,HandleSendError,LogReportError error
```

## 🎯 Key Process Steps

### 1. **Authentication & Authorization**

- User login validation
- Permission checking
- Role-based access control

### 2. **Data Validation**

- Input format validation
- Business rule validation
- Reference data validation
- Uniqueness checking

### 3. **Data Processing**

- Employee data saving
- ID generation
- Audit trail creation
- Cache management

### 4. **Notification System**

- Multi-channel notifications
- Error handling
- Status tracking
- Logging

### 5. **Reporting & Analytics**

- Statistics update
- Report generation
- Dashboard updates
- Error logging

## 🔧 Process Optimization

### 1. **Parallel Processing**

- Reference data loading
- Multiple validation checks
- Notification sending
- Report generation

### 2. **Error Handling**

- Graceful error recovery
- User-friendly error messages
- Comprehensive error logging
- Retry mechanisms

### 3. **Performance Optimization**

- Caching strategies
- Database optimization
- Async processing
- Resource management

### 4. **User Experience**

- Clear validation messages
- Progress indicators
- Confirmation dialogs
- Success feedback

---

**Dokumen ini dibuat**: 2024-01-01  
**Versi**: v1.0.0  
**Status**: Complete
