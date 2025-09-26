# Spesifikasi API Design SIMPEG UNISM

## 📋 Overview

Dokumen ini mendefinisikan desain API untuk sistem SIMPEG (Sistem Informasi Manajemen Pegawai) Universitas Islam Negeri Sultan Maulana Hasanuddin. API dirancang berdasarkan struktur database yang telah didefinisikan dalam `DATABASE_ERD_ANALYSIS.md`.

## 🎯 Prinsip Desain API

- **RESTful Architecture**: Mengikuti standar REST API
- **JSON Response Format**: Semua response dalam format JSON
- **Authentication**: JWT-based authentication
- **Versioning**: API versioning untuk backward compatibility
- **Error Handling**: Standardized error response format
- **Pagination**: Support untuk pagination pada endpoint list

## 🔐 Authentication & Authorization

### Base URL

```
https://api.simpeg.unism.ac.id/v1
```

### Authentication Headers

```http
Authorization: Bearer {jwt_token}
Content-Type: application/json
Accept: application/json
```

### Error Response Format

```json
{
  "success": false,
  "message": "Error message",
  "errors": {
    "field": ["Validation error message"]
  },
  "code": "ERROR_CODE",
  "timestamp": "2024-01-01T00:00:00Z"
}
```

## 📊 API Endpoints

### 1. Authentication Endpoints

#### POST /auth/login

Login user ke sistem

**Request:**

```json
{
  "username": "string",
  "password": "string"
}
```

**Response:**

```json
{
  "success": true,
  "data": {
    "token": "jwt_token",
    "user": {
      "id": 1,
      "name": "John Doe",
      "username": "johndoe",
      "role": "admin"
    }
  }
}
```

#### POST /auth/logout

Logout user dari sistem

#### POST /auth/refresh

Refresh JWT token

### 2. Employee Management

#### GET /employees

Mendapatkan daftar pegawai dengan pagination dan filter

**Query Parameters:**

- `page`: Halaman (default: 1)
- `limit`: Jumlah data per halaman (default: 10, max: 100)
- `search`: Pencarian berdasarkan nama atau NIK
- `department_id`: Filter berdasarkan departemen
- `status_id`: Filter berdasarkan status pegawai
- `sort`: Sorting (nama, nik, tanggal_masuk_kerja)

**Response:**

```json
{
  "success": true,
  "data": {
    "employees": [
      {
        "id": "EMP001",
        "nik": "1234567890123456",
        "nama": "John Doe",
        "email": "john.doe@unism.ac.id",
        "departement": {
          "id": 1,
          "nama": "Teknik Informatika"
        },
        "position": {
          "id": 1,
          "nama": "Dosen"
        },
        "status": "active",
        "tanggal_masuk_kerja": "2020-01-01"
      }
    ],
    "pagination": {
      "current_page": 1,
      "per_page": 10,
      "total": 150,
      "last_page": 15
    }
  }
}
```

#### GET /employees/{id}

Mendapatkan detail pegawai berdasarkan ID

#### POST /employees

Membuat pegawai baru

**Request:**

```json
{
  "nik": "1234567890123456",
  "nama": "John Doe",
  "tempat_lahir": "Jakarta",
  "tanggal_lahir": "1990-01-01",
  "jenis_kelamin": "L",
  "email": "john.doe@unism.ac.id",
  "agama_id": 1,
  "pendidikan_id": 1,
  "bidang_ilmu_id": 1,
  "status_id": 1,
  "ikatan_kerja_id": 1,
  "aktivitas_id": 1
}
```

#### PUT /employees/{id}

Update data pegawai

#### DELETE /employees/{id}

Soft delete pegawai

### 3. Organizational Structure

#### GET /departments

Mendapatkan daftar departemen

#### GET /departments/{id}/divisions

Mendapatkan divisi dalam departemen

#### GET /positions

Mendapatkan daftar posisi/jabatan

#### GET /structurals

Mendapatkan struktur organisasi

**Query Parameters:**

- `period_id`: Filter berdasarkan periode struktur
- `department_id`: Filter berdasarkan departemen
- `division_id`: Filter berdasarkan divisi
- `employee_nik`: Filter berdasarkan pegawai

#### POST /structurals

Membuat posisi struktur baru

#### PUT /structurals/{id}

Update posisi struktur

#### DELETE /structurals/{id}

Hapus posisi struktur

### 4. Reference Data

#### GET /reference/religions

Mendapatkan daftar agama

#### GET /reference/educations

Mendapatkan daftar tingkat pendidikan

#### GET /reference/fields-of-study

Mendapatkan daftar bidang ilmu

#### GET /reference/employment-status

Mendapatkan daftar status pegawai

#### GET /reference/employment-types

Mendapatkan daftar jenis ikatan kerja

#### GET /reference/activities

Mendapatkan daftar aktivitas pegawai

#### GET /reference/jabfungs

Mendapatkan daftar jabatan fungsional

### 5. Audit & History

#### GET /employees/{id}/history

Mendapatkan riwayat perubahan data pegawai

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "field_name": "nama",
      "old_value": "John Smith",
      "new_value": "John Doe",
      "changed_by": "admin@unism.ac.id",
      "changed_at": "2024-01-01T10:00:00Z"
    }
  ]
}
```

#### GET /structurals/{id}/history

Mendapatkan riwayat perubahan struktur organisasi

### 6. Reports & Analytics

#### GET /reports/employee-summary

Laporan ringkasan pegawai

**Query Parameters:**

- `department_id`: Filter berdasarkan departemen
- `date_from`: Tanggal mulai
- `date_to`: Tanggal akhir

#### GET /reports/organizational-chart

Struktur organisasi dalam format chart

#### GET /reports/employee-statistics

Statistik pegawai (jumlah per departemen, status, dll)

## 🔧 Technical Specifications

### Response Status Codes

- `200`: Success
- `201`: Created
- `400`: Bad Request
- `401`: Unauthorized
- `403`: Forbidden
- `404`: Not Found
- `422`: Validation Error
- `500`: Internal Server Error

### Pagination Format

```json
{
  "pagination": {
    "current_page": 1,
    "per_page": 10,
    "total": 150,
    "last_page": 15,
    "from": 1,
    "to": 10
  }
}
```

### Search & Filter

Semua endpoint list mendukung:

- **Search**: Pencarian teks pada field yang relevan
- **Filter**: Filter berdasarkan field yang tersedia
- **Sort**: Sorting berdasarkan field yang diizinkan
- **Pagination**: Limit dan offset untuk data besar

### Rate Limiting

- **Standard**: 1000 requests per hour per user
- **Admin**: 5000 requests per hour per user
- **Headers**:
  - `X-RateLimit-Limit`
  - `X-RateLimit-Remaining`
  - `X-RateLimit-Reset`

## 🛡️ Security Considerations

### Data Validation

- Input validation pada semua endpoint
- SQL injection prevention
- XSS protection
- CSRF protection

### Data Privacy

- Encryption untuk data sensitif (NIK, email)
- Logging untuk audit trail
- Data masking untuk response yang tidak perlu

### Access Control

- Role-based access control (RBAC)
- Resource-level permissions
- API key management

## 📝 Implementation Notes

### Database Integration

- Menggunakan Eloquent ORM untuk Laravel
- Query optimization dengan eager loading
- Database indexing untuk performa

### Caching Strategy

- Redis cache untuk data referensi
- Cache invalidation pada data update
- Response caching untuk endpoint yang jarang berubah

### Error Handling

- Centralized error handling
- Logging untuk debugging
- User-friendly error messages

## 🚀 Next Steps

1. **API Documentation**: Generate OpenAPI/Swagger documentation
2. **Testing**: Unit testing dan integration testing
3. **Performance**: Load testing dan optimization
4. **Monitoring**: API monitoring dan alerting
5. **Documentation**: User guide dan developer documentation

---

**Versi Dokumen**: v1.0.0  
**Tanggal**: 2024-01-01  
**Status**: Draft - Menunggu Review
