# Activity Diagram - Organizational Change

## 📋 Overview

Dokumen ini berisi Activity Diagram untuk proses perubahan struktur organisasi dalam sistem SIMPEG (Sistem Informasi Manajemen Pegawai) Universitas Islam Negeri Sultan Maulana Hasanuddin.

## 🎯 Activity Diagram: Organizational Structure Change Process

### Complete Organizational Change Flow

```mermaid
flowchart TD
    Start([Start]) --> Login{User Login}
    Login -->|Not Logged In| LoginPage[Login Page]
    LoginPage --> Login
    Login -->|Logged In| CheckPermission{Check Permission}

    CheckPermission -->|No Permission| AccessDenied[Access Denied]
    AccessDenied --> End([End])

    CheckPermission -->|Has Permission| SelectChangeType{Select Change Type}

    %% Department Changes
    SelectChangeType -->|Department| DepartmentChange[Department Change Process]
    DepartmentChange --> ValidateDepartment{Validate Department Change}
    ValidateDepartment -->|Invalid| DepartmentError[Department Validation Error]
    DepartmentError --> DepartmentChange
    ValidateDepartment -->|Valid| ProcessDepartment[Process Department Change]

    %% Division Changes
    SelectChangeType -->|Division| DivisionChange[Division Change Process]
    DivisionChange --> ValidateDivision{Validate Division Change}
    ValidateDivision -->|Invalid| DivisionError[Division Validation Error]
    DivisionError --> DivisionChange
    ValidateDivision -->|Valid| ProcessDivision[Process Division Change]

    %% Position Changes
    SelectChangeType -->|Position| PositionChange[Position Change Process]
    PositionChange --> ValidatePosition{Validate Position Change}
    ValidatePosition -->|Invalid| PositionError[Position Validation Error]
    PositionError --> PositionChange
    ValidatePosition -->|Valid| ProcessPosition[Process Position Change]

    %% Employee Assignment Changes
    SelectChangeType -->|Employee Assignment| AssignmentChange[Employee Assignment Change Process]
    AssignmentChange --> ValidateAssignment{Validate Assignment Change}
    ValidateAssignment -->|Invalid| AssignmentError[Assignment Validation Error]
    AssignmentError --> AssignmentChange
    ValidateAssignment -->|Valid| ProcessAssignment[Process Assignment Change]

    %% Common Processing
    ProcessDepartment --> CreatePeriod{Create New Period?}
    ProcessDivision --> CreatePeriod
    ProcessPosition --> CreatePeriod
    ProcessAssignment --> CreatePeriod

    CreatePeriod -->|Yes| CreateNewPeriod[Create New Structural Period]
    CreateNewPeriod --> ValidatePeriod{Validate Period}
    ValidatePeriod -->|Invalid| PeriodError[Period Validation Error]
    PeriodError --> CreateNewPeriod
    ValidatePeriod -->|Valid| SavePeriod[Save New Period]

    CreatePeriod -->|No| UseExistingPeriod[Use Existing Period]
    UseExistingPeriod --> SavePeriod

    SavePeriod --> UpdateStructure[Update Organizational Structure]
    UpdateStructure --> CreateAuditLog[Create Audit Log Entry]
    CreateAuditLog --> UpdateCache[Update Cache]
    UpdateCache --> SendNotification[Send Change Notification]
    SendNotification --> SuccessMessage[Display Success Message]
    SuccessMessage --> End

    %% Error Handling
    UpdateStructure -->|Database Error| DBError[Database Error]
    DBError --> ErrorMessage[Display Error Message]
    ErrorMessage --> SelectChangeType

    SendNotification -->|Notification Error| NotifError[Notification Error]
    NotifError --> LogError[Log Error]
    LogError --> SuccessMessage

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef error fill:#ffebee
    classDef change fill:#e8f5e8

    class Start,End startEnd
    class LoginPage,DepartmentChange,DivisionChange,PositionChange,AssignmentChange,ProcessDepartment,ProcessDivision,ProcessPosition,ProcessAssignment,CreateNewPeriod,SavePeriod,UpdateStructure,CreateAuditLog,UpdateCache,SendNotification,SuccessMessage process
    class Login,CheckPermission,SelectChangeType,ValidateDepartment,ValidateDivision,ValidatePosition,ValidateAssignment,CreatePeriod,ValidatePeriod decision
    class AccessDenied,DepartmentError,DivisionError,PositionError,AssignmentError,PeriodError,DBError,ErrorMessage,NotifError,LogError error
    class ProcessDepartment,ProcessDivision,ProcessPosition,ProcessAssignment change
```

## 🔄 Activity Diagram: Employee Assignment Change Process

### Detailed Employee Assignment Change Flow

```mermaid
flowchart TD
    Start([Start Assignment Change]) --> SelectEmployee[Select Employee]
    SelectEmployee --> ValidateEmployee{Employee Valid?}
    ValidateEmployee -->|Invalid| EmployeeError[Employee Not Found]
    EmployeeError --> SelectEmployee
    ValidateEmployee -->|Valid| SelectNewPosition[Select New Position]

    SelectNewPosition --> ValidatePosition{Position Valid?}
    ValidatePosition -->|Invalid| PositionError[Position Not Found]
    PositionError --> SelectNewPosition
    ValidatePosition -->|Valid| SelectPeriod[Select Structural Period]

    SelectPeriod --> ValidatePeriod{Period Valid?}
    ValidatePeriod -->|Invalid| PeriodError[Period Not Found]
    PeriodError --> SelectPeriod
    ValidatePeriod -->|Valid| CheckConflicts{Check Assignment Conflicts}

    CheckConflicts -->|Conflict Found| ConflictError[Assignment Conflict Error]
    ConflictError --> ResolveConflict[Resolve Assignment Conflict]
    ResolveConflict --> CheckConflicts

    CheckConflicts -->|No Conflicts| CreateAssignment[Create New Assignment]
    CreateAssignment --> UpdatePreviousAssignment{Update Previous Assignment?}

    UpdatePreviousAssignment -->|Yes| DeactivatePrevious[Deactivate Previous Assignment]
    DeactivatePrevious --> LogPreviousChange[Log Previous Assignment Change]
    LogPreviousChange --> SaveNewAssignment[Save New Assignment]

    UpdatePreviousAssignment -->|No| SaveNewAssignment
    SaveNewAssignment --> CreateAssignmentAudit[Create Assignment Audit Log]
    CreateAssignmentAudit --> UpdateEmployeeHistory[Update Employee History]
    UpdateEmployeeHistory --> NotifyStakeholders[Notify Stakeholders]

    NotifyStakeholders --> NotifyEmployee[Notify Employee]
    NotifyStakeholders --> NotifyManager[Notify Department Manager]
    NotifyStakeholders --> NotifyHR[Notify HR Team]

    NotifyEmployee --> NotifyManager
    NotifyManager --> NotifyHR
    NotifyHR --> UpdateDashboard[Update Organizational Dashboard]
    UpdateDashboard --> End([Assignment Change Complete])

    %% Error Handling
    CreateAssignment -->|Database Error| AssignmentDBError[Assignment Database Error]
    AssignmentDBError --> HandleError[Handle Assignment Error]
    HandleError --> SelectEmployee

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef error fill:#ffebee
    classDef notification fill:#e3f2fd

    class Start,End startEnd
    class SelectEmployee,SelectNewPosition,SelectPeriod,CreateAssignment,DeactivatePrevious,LogPreviousChange,SaveNewAssignment,CreateAssignmentAudit,UpdateEmployeeHistory,UpdateDashboard,ResolveConflict process
    class ValidateEmployee,ValidatePosition,ValidatePeriod,CheckConflicts,UpdatePreviousAssignment decision
    class EmployeeError,PositionError,PeriodError,ConflictError,AssignmentDBError,HandleError error
    class NotifyStakeholders,NotifyEmployee,NotifyManager,NotifyHR notification
```

## 🏢 Activity Diagram: Department Restructuring Process

### Department Restructuring with Approval Workflow

```mermaid
flowchart TD
    Start([Start Department Restructuring]) --> CreateRestructurePlan[Create Restructuring Plan]
    CreateRestructurePlan --> DefineChanges[Define Department Changes]
    DefineChanges --> ImpactAnalysis[Perform Impact Analysis]

    ImpactAnalysis --> AnalyzeEmployees[Analyze Affected Employees]
    AnalyzeEmployees --> AnalyzePositions[Analyze Affected Positions]
    AnalyzePositions --> AnalyzeDivisions[Analyze Affected Divisions]
    AnalyzeDivisions --> GenerateImpactReport[Generate Impact Report]

    GenerateImpactReport --> SubmitForApproval[Submit for Approval]
    SubmitForApproval --> NotifyApprovers[Notify Approvers]
    NotifyApprovers --> WaitApproval[Wait for Approval]

    WaitApproval --> CheckApproval{Approval Status}
    CheckApproval -->|Pending| WaitApproval
    CheckApproval -->|Rejected| HandleRejection[Handle Rejection]
    CheckApproval -->|Approved| ExecuteRestructure[Execute Restructuring]

    HandleRejection --> NotifyRejection[Notify Plan Creator]
    NotifyRejection --> RevisePlan[Revise Restructuring Plan]
    RevisePlan --> CreateRestructurePlan

    ExecuteRestructure --> CreateNewPeriod[Create New Structural Period]
    CreateNewPeriod --> UpdateDepartments[Update Department Structure]
    UpdateDepartments --> UpdateDivisions[Update Division Structure]
    UpdateDivisions --> UpdatePositions[Update Position Structure]
    UpdatePositions --> ReassignEmployees[Reassign Affected Employees]

    ReassignEmployees --> ValidateAssignments{Validate All Assignments}
    ValidateAssignments -->|Invalid| FixAssignments[Fix Invalid Assignments]
    FixAssignments --> ValidateAssignments
    ValidateAssignments -->|Valid| CreateAuditTrail[Create Complete Audit Trail]

    CreateAuditTrail --> NotifyAllStakeholders[Notify All Stakeholders]
    NotifyAllStakeholders --> NotifyAffectedEmployees[Notify Affected Employees]
    NotifyAffectedEmployees --> NotifyDepartmentManagers[Notify Department Managers]
    NotifyDepartmentManagers --> NotifyHRTeam[Notify HR Team]
    NotifyHRTeam --> NotifyManagement[Notify Senior Management]

    NotifyManagement --> UpdateSystem[Update System Configuration]
    UpdateSystem --> UpdateReports[Update Reporting Structure]
    UpdateReports --> UpdateDashboard[Update Organizational Dashboard]
    UpdateDashboard --> End([Restructuring Complete])

    %% Error Handling
    ExecuteRestructure -->|Error| HandleRestructureError[Handle Restructuring Error]
    HandleRestructureError --> RollbackChanges[Rollback Changes]
    RollbackChanges --> NotifyError[Notify Error to Stakeholders]
    NotifyError --> End

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef error fill:#ffebee
    classDef approval fill:#e8f5e8
    classDef notification fill:#e3f2fd

    class Start,End startEnd
    class CreateRestructurePlan,DefineChanges,ImpactAnalysis,AnalyzeEmployees,AnalyzePositions,AnalyzeDivisions,GenerateImpactReport,SubmitForApproval,NotifyApprovers,WaitApproval,ExecuteRestructure,CreateNewPeriod,UpdateDepartments,UpdateDivisions,UpdatePositions,ReassignEmployees,CreateAuditTrail,UpdateSystem,UpdateReports,UpdateDashboard process
    class CheckApproval,ValidateAssignments decision
    class HandleRejection,NotifyRejection,RevisePlan,FixAssignments approval
    class NotifyAllStakeholders,NotifyAffectedEmployees,NotifyDepartmentManagers,NotifyHRTeam,NotifyManagement notification
    class HandleRestructureError,RollbackChanges,NotifyError error
```

## 📊 Activity Diagram: Organizational Change Reporting

### Change Impact Reporting and Analytics

```mermaid
flowchart TD
    Start([Change Complete]) --> GenerateChangeReport[Generate Change Report]
    GenerateChangeReport --> CollectChangeData[Collect Change Data]
    CollectChangeData --> AnalyzeImpact[Analyze Change Impact]

    AnalyzeImpact --> AnalyzeEmployeeImpact[Analyze Employee Impact]
    AnalyzeEmployeeImpact --> AnalyzePositionImpact[Analyze Position Impact]
    AnalyzePositionImpact --> AnalyzeDepartmentImpact[Analyze Department Impact]
    AnalyzeDepartmentImpact --> GenerateImpactMetrics[Generate Impact Metrics]

    GenerateImpactMetrics --> CreateChangeSummary[Create Change Summary]
    CreateChangeSummary --> FormatReport[Format Report]
    FormatReport --> DetermineRecipients{Determine Report Recipients}

    DetermineRecipients -->|HR Team| SendHRReport[Send Report to HR Team]
    DetermineRecipients -->|Management| SendManagementReport[Send Report to Management]
    DetermineRecipients -->|Stakeholders| SendStakeholderReport[Send Report to Stakeholders]
    DetermineRecipients -->|All| SendAllReports[Send Reports to All Recipients]

    SendHRReport --> UpdateHRDashboard[Update HR Dashboard]
    SendManagementReport --> UpdateManagementDashboard[Update Management Dashboard]
    SendStakeholderReport --> UpdateStakeholderDashboard[Update Stakeholder Dashboard]
    SendAllReports --> UpdateAllDashboards[Update All Dashboards]

    UpdateHRDashboard --> LogReportSent[Log Report Sent]
    UpdateManagementDashboard --> LogReportSent
    UpdateStakeholderDashboard --> LogReportSent
    UpdateAllDashboards --> LogReportSent

    LogReportSent --> UpdateChangeHistory[Update Change History]
    UpdateChangeHistory --> ArchiveChangeData[Archive Change Data]
    ArchiveChangeData --> UpdateAnalytics[Update Analytics Database]
    UpdateAnalytics --> End([Reporting Complete])

    %% Error Handling
    CollectChangeData -->|Error| HandleDataError[Handle Data Collection Error]
    AnalyzeImpact -->|Error| HandleAnalysisError[Handle Analysis Error]
    FormatReport -->|Error| HandleFormatError[Handle Format Error]
    SendHRReport -->|Error| HandleSendError[Handle Send Error]
    SendManagementReport -->|Error| HandleSendError
    SendStakeholderReport -->|Error| HandleSendError
    SendAllReports -->|Error| HandleSendError

    HandleDataError --> LogError[Log Error]
    HandleAnalysisError --> LogError
    HandleFormatError --> LogError
    HandleSendError --> LogError
    LogError --> UpdateChangeHistory

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef error fill:#ffebee
    classDef report fill:#f1f8e9

    class Start,End startEnd
    class GenerateChangeReport,CollectChangeData,AnalyzeImpact,AnalyzeEmployeeImpact,AnalyzePositionImpact,AnalyzeDepartmentImpact,GenerateImpactMetrics,CreateChangeSummary,FormatReport,UpdateHRDashboard,UpdateManagementDashboard,UpdateStakeholderDashboard,UpdateAllDashboards,LogReportSent,UpdateChangeHistory,ArchiveChangeData,UpdateAnalytics process
    class DetermineRecipients decision
    class SendHRReport,SendManagementReport,SendStakeholderReport,SendAllReports report
    class HandleDataError,HandleAnalysisError,HandleFormatError,HandleSendError,LogError error
```

## 🔔 Activity Diagram: Organizational Change Notification

### Comprehensive Notification System for Organizational Changes

```mermaid
flowchart TD
    Start([Change Approved]) --> DetermineNotificationScope{Determine Notification Scope}

    DetermineNotificationScope -->|All Employees| NotifyAllEmployees[Notify All Employees]
    DetermineNotificationScope -->|Affected Employees| NotifyAffectedOnly[Notify Affected Employees Only]
    DetermineNotificationScope -->|Management Only| NotifyManagementOnly[Notify Management Only]
    DetermineNotificationScope -->|Custom Scope| NotifyCustomScope[Notify Custom Scope]

    %% All Employees Notification
    NotifyAllEmployees --> SendAllEmployeeEmail[Send Email to All Employees]
    SendAllEmployeeEmail --> SendAllEmployeeSMS[Send SMS to All Employees]
    SendAllEmployeeSMS --> PostAllEmployeeNotice[Post Notice on Employee Portal]
    PostAllEmployeeNotice --> LogAllEmployeeNotification[Log All Employee Notification]

    %% Affected Employees Only
    NotifyAffectedOnly --> GetAffectedEmployees[Get Affected Employees List]
    GetAffectedEmployees --> SendAffectedEmail[Send Email to Affected Employees]
    SendAffectedEmail --> SendAffectedSMS[Send SMS to Affected Employees]
    SendAffectedSMS --> PostAffectedNotice[Post Notice for Affected Employees]
    PostAffectedNotice --> LogAffectedNotification[Log Affected Employee Notification]

    %% Management Only
    NotifyManagementOnly --> GetManagementList[Get Management List]
    GetManagementList --> SendManagementEmail[Send Email to Management]
    SendManagementEmail --> SendManagementSMS[Send SMS to Management]
    SendManagementSMS --> LogManagementNotification[Log Management Notification]

    %% Custom Scope
    NotifyCustomScope --> DefineCustomScope[Define Custom Notification Scope]
    DefineCustomScope --> SendCustomEmail[Send Email to Custom Scope]
    SendCustomEmail --> SendCustomSMS[Send SMS to Custom Scope]
    SendCustomSMS --> LogCustomNotification[Log Custom Notification]

    %% Consolidate All Notifications
    LogAllEmployeeNotification --> ConsolidateNotifications[Consolidate All Notifications]
    LogAffectedNotification --> ConsolidateNotifications
    LogManagementNotification --> ConsolidateNotifications
    LogCustomNotification --> ConsolidateNotifications

    ConsolidateNotifications --> UpdateNotificationStatus[Update Notification Status]
    UpdateNotificationStatus --> GenerateNotificationReport[Generate Notification Report]
    GenerateNotificationReport --> SendNotificationSummary[Send Notification Summary to HR]
    SendNotificationSummary --> End([Notification Complete])

    %% Error Handling
    SendAllEmployeeEmail -->|Error| HandleEmailError[Handle Email Error]
    SendAffectedEmail -->|Error| HandleEmailError
    SendManagementEmail -->|Error| HandleEmailError
    SendCustomEmail -->|Error| HandleEmailError

    SendAllEmployeeSMS -->|Error| HandleSMSError[Handle SMS Error]
    SendAffectedSMS -->|Error| HandleSMSError
    SendManagementSMS -->|Error| HandleSMSError
    SendCustomSMS -->|Error| HandleSMSError

    HandleEmailError --> LogNotificationError[Log Notification Error]
    HandleSMSError --> LogNotificationError
    LogNotificationError --> ConsolidateNotifications

    %% Styling
    classDef startEnd fill:#e1f5fe
    classDef process fill:#f3e5f5
    classDef decision fill:#fff3e0
    classDef notification fill:#e3f2fd
    classDef error fill:#ffebee

    class Start,End startEnd
    class NotifyAllEmployees,NotifyAffectedOnly,NotifyManagementOnly,NotifyCustomScope,GetAffectedEmployees,GetManagementList,DefineCustomScope,SendAllEmployeeEmail,SendAllEmployeeSMS,PostAllEmployeeNotice,LogAllEmployeeNotification,SendAffectedEmail,SendAffectedSMS,PostAffectedNotice,LogAffectedNotification,SendManagementEmail,SendManagementSMS,LogManagementNotification,SendCustomEmail,SendCustomSMS,LogCustomNotification,ConsolidateNotifications,UpdateNotificationStatus,GenerateNotificationReport,SendNotificationSummary process
    class DetermineNotificationScope decision
    class SendAllEmployeeEmail,SendAllEmployeeSMS,PostAllEmployeeNotice,SendAffectedEmail,SendAffectedSMS,PostAffectedNotice,SendManagementEmail,SendManagementSMS,SendCustomEmail,SendCustomSMS notification
    class HandleEmailError,HandleSMSError,LogNotificationError error
```

## 🎯 Key Process Steps

### 1. **Change Initiation**

- User authentication and authorization
- Change type selection
- Impact assessment
- Approval workflow

### 2. **Validation & Conflict Resolution**

- Data validation
- Conflict detection
- Conflict resolution
- Business rule validation

### 3. **Change Execution**

- Structural period management
- Database updates
- Audit trail creation
- Cache management

### 4. **Notification & Communication**

- Multi-channel notifications
- Stakeholder communication
- Status updates
- Error handling

### 5. **Reporting & Analytics**

- Impact analysis
- Change reporting
- Dashboard updates
- Historical tracking

## 🔧 Process Optimization

### 1. **Parallel Processing**

- Multiple change validations
- Concurrent notifications
- Parallel report generation
- Async processing

### 2. **Error Handling**

- Graceful error recovery
- Rollback mechanisms
- Error logging
- User feedback

### 3. **Performance Optimization**

- Caching strategies
- Database optimization
- Batch processing
- Resource management

### 4. **User Experience**

- Clear change workflows
- Progress indicators
- Confirmation dialogs
- Success feedback

---

**Dokumen ini dibuat**: 2024-01-01  
**Versi**: v1.0.0  
**Status**: Complete
