# Functional Requirements - SIMPEG UNISM

## 📋 Overview

Dokumen ini mendefinisikan functional requirements untuk sistem SIMPEG (Sistem Informasi Manajemen Pegawai) Universitas Islam Negeri Sultan Maulana Hasanuddin.

## 👥 User Requirements

### User Roles dan Permissions

#### 1. HR Administrator

**Role**: Full access untuk semua fitur sistem

- ✅ **Employee Management**: CRUD operations untuk semua pegawai
- ✅ **Organizational Structure**: Manage departemen, divisi, dan posisi
- ✅ **Reference Data**: Manage data referensi
- ✅ **Reports**: Generate semua jenis laporan
- ✅ **Audit Trail**: Access audit logs
- ✅ **System Configuration**: Configure system settings

#### 2. HR Staff

**Role**: Limited access untuk operasi sehari-hari

- ✅ **Employee Management**: CRUD operations untuk pegawai
- ✅ **Organizational Structure**: View struktur organisasi
- ✅ **Reference Data**: View data referensi
- ✅ **Reports**: Generate basic reports
- ❌ **Audit Trail**: No access
- ❌ **System Configuration**: No access

#### 3. Department Manager

**Role**: Access terbatas pada departemennya

- ✅ **Employee Management**: View pegawai di departemennya
- ✅ **Organizational Structure**: View struktur departemennya
- ✅ **Reports**: Generate reports untuk departemennya
- ❌ **Reference Data**: No access
- ❌ **Audit Trail**: No access
- ❌ **System Configuration**: No access

#### 4. Employee

**Role**: View-only access untuk data pribadi

- ✅ **Employee Management**: View data pribadinya
- ✅ **Organizational Structure**: View posisinya dalam struktur
- ❌ **Reports**: No access
- ❌ **Reference Data**: No access
- ❌ **Audit Trail**: No access
- ❌ **System Configuration**: No access

## 🔧 System Features dan Capabilities

### 1. Employee Management Module

#### 1.1 Employee Registration

**Function**: Registrasi pegawai baru

- ✅ **Input Validation**: Validasi semua field input
- ✅ **Data Validation**: Validasi NIK, email, dll.
- ✅ **Auto-assignment**: Auto-assign employee ID
- ✅ **Notification**: Notifikasi registrasi berhasil
- ✅ **Audit Log**: Log registrasi dalam audit trail

**Input Fields**:

- NIK (16 digit, unique)
- Nama lengkap
- Tempat dan tanggal lahir
- Jenis kelamin
- Email (unique)
- Nomor telepon
- Alamat
- Data referensi (agama, pendidikan, dll.)

#### 1.2 Employee Information Update

**Function**: Update informasi pegawai

- ✅ **Field Validation**: Validasi field yang diupdate
- ✅ **Change Tracking**: Track perubahan data
- ✅ **Approval Workflow**: Workflow approval untuk perubahan penting
- ✅ **Notification**: Notifikasi perubahan
- ✅ **Audit Log**: Log perubahan dalam audit trail

#### 1.3 Employee Search dan Filter

**Function**: Pencarian dan filter pegawai

- ✅ **Advanced Search**: Search berdasarkan multiple criteria
- ✅ **Filter Options**: Filter berdasarkan departemen, status, dll.
- ✅ **Sort Options**: Sort berdasarkan nama, NIK, tanggal masuk
- ✅ **Pagination**: Pagination untuk hasil pencarian
- ✅ **Export**: Export hasil pencarian ke Excel/PDF

**Search Criteria**:

- Nama pegawai
- NIK
- Email
- Departemen
- Status pegawai
- Tanggal masuk kerja

#### 1.4 Employee Deactivation

**Function**: Deaktivasi pegawai

- ✅ **Soft Delete**: Soft delete untuk data historis
- ✅ **Reason Tracking**: Track alasan deaktivasi
- ✅ **Approval Workflow**: Workflow approval
- ✅ **Notification**: Notifikasi deaktivasi
- ✅ **Audit Log**: Log deaktivasi dalam audit trail

### 2. Organizational Structure Module

#### 2.1 Department Management

**Function**: Manajemen departemen

- ✅ **CRUD Operations**: Create, Read, Update, Delete departemen
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi nama departemen unik
- ✅ **Audit Log**: Log perubahan departemen

#### 2.2 Division Management

**Function**: Manajemen divisi

- ✅ **CRUD Operations**: Create, Read, Update, Delete divisi
- ✅ **Department Association**: Associate divisi dengan departemen
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi nama divisi unik dalam departemen
- ✅ **Audit Log**: Log perubahan divisi

#### 2.3 Position Management

**Function**: Manajemen posisi/jabatan

- ✅ **CRUD Operations**: Create, Read, Update, Delete posisi
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi nama posisi unik
- ✅ **Audit Log**: Log perubahan posisi

#### 2.4 Structural Assignment

**Function**: Assignment pegawai ke struktur organisasi

- ✅ **Period Management**: Assignment berdasarkan periode
- ✅ **Validation**: Validasi assignment tidak conflict
- ✅ **Approval Workflow**: Workflow approval untuk assignment
- ✅ **Notification**: Notifikasi assignment
- ✅ **Audit Log**: Log assignment dalam audit trail

#### 2.5 Organizational Chart

**Function**: Visualisasi struktur organisasi

- ✅ **Interactive Chart**: Chart yang dapat di-zoom dan navigate
- ✅ **Real-time Updates**: Update real-time ketika ada perubahan
- ✅ **Export**: Export chart ke PDF/PNG
- ✅ **Search**: Search pegawai dalam chart
- ✅ **Filter**: Filter berdasarkan departemen/divisi

### 3. Reference Data Management Module

#### 3.1 Religion Management

**Function**: Manajemen data agama

- ✅ **CRUD Operations**: Create, Read, Update, Delete agama
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi nama agama unik
- ✅ **Audit Log**: Log perubahan agama

#### 3.2 Education Management

**Function**: Manajemen data pendidikan

- ✅ **CRUD Operations**: Create, Read, Update, Delete pendidikan
- ✅ **Level Management**: Manage tingkat pendidikan
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi nama pendidikan unik
- ✅ **Audit Log**: Log perubahan pendidikan

#### 3.3 Field of Study Management

**Function**: Manajemen bidang ilmu

- ✅ **CRUD Operations**: Create, Read, Update, Delete bidang ilmu
- ✅ **Code Management**: Manage kode bidang ilmu
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi kode bidang ilmu unik
- ✅ **Audit Log**: Log perubahan bidang ilmu

#### 3.4 Employment Status Management

**Function**: Manajemen status pegawai

- ✅ **CRUD Operations**: Create, Read, Update, Delete status
- ✅ **Description Management**: Manage deskripsi status
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi nama status unik
- ✅ **Audit Log**: Log perubahan status

#### 3.5 Employment Type Management

**Function**: Manajemen jenis ikatan kerja

- ✅ **CRUD Operations**: Create, Read, Update, Delete jenis ikatan kerja
- ✅ **Description Management**: Manage deskripsi jenis ikatan kerja
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi nama jenis ikatan kerja unik
- ✅ **Audit Log**: Log perubahan jenis ikatan kerja

#### 3.6 Activity Management

**Function**: Manajemen aktivitas pegawai

- ✅ **CRUD Operations**: Create, Read, Update, Delete aktivitas
- ✅ **Description Management**: Manage deskripsi aktivitas
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi nama aktivitas unik
- ✅ **Audit Log**: Log perubahan aktivitas

#### 3.7 Jabatan Fungsional Management

**Function**: Manajemen jabatan fungsional

- ✅ **CRUD Operations**: Create, Read, Update, Delete jabatan fungsional
- ✅ **Sub-jabatan Management**: Manage sub-jabatan fungsional
- ✅ **Credit Management**: Manage angka kredit minimum
- ✅ **Status Management**: Active/Inactive status
- ✅ **Validation**: Validasi nama jabatan fungsional unik
- ✅ **Audit Log**: Log perubahan jabatan fungsional

### 4. Audit Trail Module

#### 4.1 Change Logging

**Function**: Log semua perubahan data

- ✅ **Automatic Logging**: Otomatis log semua perubahan
- ✅ **User Tracking**: Track siapa yang melakukan perubahan
- ✅ **Timestamp**: Record timestamp perubahan
- ✅ **Field Tracking**: Track field apa yang berubah
- ✅ **Value Tracking**: Track nilai lama dan baru

#### 4.2 Audit Report

**Function**: Generate audit report

- ✅ **Filter Options**: Filter berdasarkan user, date, table
- ✅ **Export**: Export audit report ke Excel/PDF
- ✅ **Search**: Search dalam audit log
- ✅ **Pagination**: Pagination untuk audit log
- ✅ **Real-time**: Real-time audit log viewing

#### 4.3 Data History

**Function**: View riwayat data pegawai

- ✅ **Employee History**: View riwayat perubahan data pegawai
- ✅ **Structural History**: View riwayat perubahan struktur
- ✅ **Comparison**: Compare data lama dan baru
- ✅ **Timeline**: Timeline perubahan data
- ✅ **Export**: Export riwayat ke Excel/PDF

### 5. Reports Module

#### 5.1 Employee Summary Report

**Function**: Generate laporan ringkasan pegawai

- ✅ **Filter Options**: Filter berdasarkan departemen, status, dll.
- ✅ **Grouping**: Group berdasarkan kriteria tertentu
- ✅ **Statistics**: Statistik pegawai
- ✅ **Export**: Export ke Excel/PDF
- ✅ **Scheduling**: Schedule report generation

#### 5.2 Organizational Structure Report

**Function**: Generate laporan struktur organisasi

- ✅ **Period Filter**: Filter berdasarkan periode
- ✅ **Department Filter**: Filter berdasarkan departemen
- ✅ **Visualization**: Visual representation
- ✅ **Export**: Export ke Excel/PDF
- ✅ **Scheduling**: Schedule report generation

#### 5.3 Employee Statistics Report

**Function**: Generate laporan statistik pegawai

- ✅ **Demographics**: Statistik demografis
- ✅ **Distribution**: Distribusi pegawai per departemen
- ✅ **Trends**: Trend perubahan pegawai
- ✅ **Export**: Export ke Excel/PDF
- ✅ **Scheduling**: Schedule report generation

### 6. Authentication dan Authorization Module

#### 6.1 User Authentication

**Function**: Autentikasi user

- ✅ **Login**: Login dengan username/password
- ✅ **JWT Token**: Generate dan validate JWT token
- ✅ **Session Management**: Manage user session
- ✅ **Logout**: Logout dan invalidate token
- ✅ **Password Reset**: Reset password functionality

#### 6.2 Role-based Access Control

**Function**: Kontrol akses berdasarkan role

- ✅ **Role Management**: Manage user roles
- ✅ **Permission Management**: Manage permissions per role
- ✅ **Access Control**: Control access berdasarkan role
- ✅ **Authorization**: Authorize user actions
- ✅ **Audit Log**: Log access attempts

### 7. API Integration Module

#### 7.1 Sister API Integration

**Function**: Integrasi dengan API Sister

- ✅ **Data Synchronization**: Sync data referensi
- ✅ **Cache Management**: Cache data untuk performa
- ✅ **Error Handling**: Handle API errors
- ✅ **Rate Limiting**: Implement rate limiting
- ✅ **Retry Logic**: Retry failed requests

#### 7.2 Data Validation

**Function**: Validasi data dengan sistem eksternal

- ✅ **Reference Validation**: Validate data referensi
- ✅ **Consistency Check**: Check data consistency
- ✅ **Error Reporting**: Report validation errors
- ✅ **Auto-correction**: Auto-correct data jika memungkinkan
- ✅ **Manual Review**: Flag data untuk manual review

## 📊 Data Requirements

### 1. Employee Data

- **Personal Information**: Nama, NIK, tempat/tanggal lahir, jenis kelamin
- **Contact Information**: Email, telepon, alamat
- **Professional Information**: NIDN, gelar, bidang ilmu, pendidikan
- **Employment Information**: Status, ikatan kerja, aktivitas, jabatan fungsional
- **Organizational Information**: Departemen, divisi, posisi, periode

### 2. Organizational Data

- **Department Data**: Nama departemen, status, timestamps
- **Division Data**: Nama divisi, departemen parent, status, timestamps
- **Position Data**: Nama posisi, status, timestamps
- **Structural Data**: Assignment pegawai ke struktur, periode

### 3. Reference Data

- **Religion Data**: Nama agama, status
- **Education Data**: Tingkat, nama, status
- **Field of Study Data**: Nama, kode, status
- **Employment Status Data**: Nama, deskripsi, status
- **Employment Type Data**: Nama, deskripsi, status
- **Activity Data**: Nama, deskripsi, status
- **Jabatan Fungsional Data**: Nama, sub-jabatan, angka kredit minimum

### 4. Audit Data

- **Change Log Data**: Table, field, old value, new value, user, timestamp
- **User Activity Data**: User, action, timestamp, IP address
- **System Log Data**: Event, level, message, timestamp

## 🔗 Interface Requirements

### 1. User Interface

- **Responsive Design**: Support desktop dan mobile
- **Intuitive Navigation**: Easy navigation antara modules
- **Search Functionality**: Global search dalam sistem
- **Filter Options**: Advanced filtering options
- **Export Functionality**: Export data ke berbagai format

### 2. API Interface

- **RESTful API**: Standard REST API design
- **JSON Format**: JSON request/response format
- **Authentication**: JWT-based authentication
- **Error Handling**: Standardized error responses
- **Documentation**: Complete API documentation

### 3. Integration Interface

- **Sister API**: Integration dengan API Sister
- **Webhook Support**: Support untuk webhook notifications
- **Batch Processing**: Support untuk batch operations
- **Real-time Sync**: Real-time data synchronization
- **Error Recovery**: Automatic error recovery

---

**Dokumen ini dibuat**: 2024-01-01  
**Versi**: v1.0.0  
**Status**: Complete
