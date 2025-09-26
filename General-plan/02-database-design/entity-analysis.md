# Entity Analysis - SIMPEG UNISM

## 📋 Overview

Dokumen ini berisi analisis entitas untuk database sistem SIMPEG (Sistem Informasi Manajemen Pegawai) Universitas Islam Negeri Sultan Maulana Hasanuddin.

## 🏢 Core Entities

### 1. Employees Entity

#### Overview

Entitas utama untuk menyimpan data pegawai/karyawan dengan informasi lengkap.

#### Attributes

| Attribute                | Type      | Constraints  | Description                         |
| ------------------------ | --------- | ------------ | ----------------------------------- |
| `id`                     | string    | PK, Not Null | Primary key pegawai                 |
| `id_sp`                  | string    | UK, Not Null | ID dari sistem Sister               |
| `nik`                    | string    | UK, Not Null | Nomor Induk Kependudukan (16 digit) |
| `nidn`                   | string    | Nullable     | Nomor Induk Dosen Nasional          |
| `full_name`              | string    | Not Null     | Nama lengkap pegawai                |
| `birth_place`            | string    | Nullable     | Tempat lahir                        |
| `birth_date`             | date      | Nullable     | Tanggal lahir                       |
| `gender`                 | enum      | Nullable     | L/P                                 |
| `title_prefix`           | string    | Nullable     | Gelar depan                         |
| `title_suffix`           | string    | Nullable     | Gelar belakang                      |
| `field_of_study_id`      | bigint    | FK, Nullable | Referensi bidang ilmu               |
| `education_id`           | bigint    | FK, Nullable | Referensi pendidikan                |
| `ektp`                   | string    | Nullable     | Nomor E-KTP                         |
| `family_card_no`         | string    | Nullable     | Nomor Kartu Keluarga                |
| `ktp_address`            | text      | Nullable     | Alamat sesuai KTP                   |
| `current_address`        | text      | Nullable     | Alamat domisili                     |
| `religion_id`            | bigint    | FK, Nullable | Referensi agama                     |
| `employment_status_id`   | bigint    | FK, Nullable | Referensi status pegawai            |
| `phone_number`           | string    | Nullable     | Nomor telepon                       |
| `email`                  | string    | Nullable     | Email pegawai                       |
| `employment_type_id`     | bigint    | FK, Nullable | Referensi jenis ikatan kerja        |
| `activity_id`            | bigint    | FK, Nullable | Referensi aktivitas                 |
| `photo`                  | string    | Nullable     | Path foto pegawai                   |
| `employment_date`        | date      | Nullable     | Tanggal masuk kerja                 |
| `role`                   | string    | Nullable     | Peran pegawai                       |
| `main_duties`            | text      | Nullable     | Tugas utama pegawai                 |
| `lecturer_certification` | string    | Nullable     | Status sertifikasi dosen            |
| `functional_position_id` | bigint    | FK, Nullable | Referensi jabatan fungsional        |
| `created_at`             | timestamp | Not Null     | Timestamp pembuatan                 |
| `updated_at`             | timestamp | Not Null     | Timestamp update                    |
| `deleted_at`             | timestamp | Nullable     | Timestamp soft delete               |

#### Business Rules

1. NIK harus unik dan valid (16 digit)
2. Email harus valid format dan unik
3. Tanggal lahir tidak boleh di masa depan
4. Data pegawai tidak boleh dihapus permanen (soft delete)
5. Setiap perubahan data harus dicatat dalam audit trail

#### Indexes

- Primary key: `id`
- Unique indexes: `id_sp`, `nik`
- Foreign key indexes: `field_of_study_id`, `education_id`, `religion_id`, `employment_status_id`, `employment_type_id`, `activity_id`, `functional_position_id`
- Search indexes: `full_name`, `email`

### 2. Users Entity

#### Overview

Entitas untuk pengguna sistem dengan autentikasi.

#### Attributes

| Attribute              | Type      | Constraints  | Description                |
| ---------------------- | --------- | ------------ | -------------------------- |
| `id`                   | bigint    | PK, Not Null | Primary key user           |
| `name`                 | string    | Not Null     | Nama lengkap user          |
| `username`             | string    | UK, Not Null | Username untuk login       |
| `phone`                | string    | UK, Nullable | Nomor telepon              |
| `prodi`                | char      | Nullable     | Program studi              |
| `oauth_client_role_id` | bigint    | Nullable     | Role OAuth client          |
| `email_verified_at`    | timestamp | Nullable     | Timestamp verifikasi email |
| `remember_token`       | string    | Nullable     | Token remember me          |
| `created_at`           | timestamp | Not Null     | Timestamp pembuatan        |
| `updated_at`           | timestamp | Not Null     | Timestamp update           |

#### Business Rules

1. Username harus unik
2. Phone harus unik jika tidak null
3. Password harus sesuai policy keamanan
4. User harus diverifikasi email sebelum aktif

#### Indexes

- Primary key: `id`
- Unique indexes: `username`, `phone`
- Composite index: `username`, `phone`

### 3. Departements Entity

#### Overview

Entitas untuk departemen dalam struktur organisasi.

#### Attributes

| Attribute    | Type      | Constraints             | Description             |
| ------------ | --------- | ----------------------- | ----------------------- |
| `id`         | bigint    | PK, Not Null            | Primary key departemen  |
| `name`       | string    | Not Null                | Nama departemen         |
| `is_active`  | boolean   | Not Null, Default: true | Status aktif departemen |
| `created_at` | timestamp | Not Null                | Timestamp pembuatan     |
| `updated_at` | timestamp | Not Null                | Timestamp update        |
| `deleted_at` | timestamp | Nullable                | Timestamp soft delete   |

#### Business Rules

1. Nama departemen harus unik
2. Departemen tidak boleh dihapus jika masih memiliki divisi aktif
3. Status aktif harus dipertahankan untuk data historis

#### Indexes

- Primary key: `id`
- Search index: `name`
- Status index: `is_active`

### 4. Divisions Entity

#### Overview

Entitas untuk divisi yang berada di bawah departemen.

#### Attributes

| Attribute       | Type      | Constraints             | Description           |
| --------------- | --------- | ----------------------- | --------------------- |
| `id`            | bigint    | PK, Not Null            | Primary key divisi    |
| `department_id` | bigint    | FK, Not Null            | Referensi departemen  |
| `name`          | string    | Not Null                | Nama divisi           |
| `is_active`     | boolean   | Not Null, Default: true | Status aktif divisi   |
| `created_at`    | timestamp | Not Null                | Timestamp pembuatan   |
| `updated_at`    | timestamp | Not Null                | Timestamp update      |
| `deleted_at`    | timestamp | Nullable                | Timestamp soft delete |

#### Business Rules

1. Nama divisi harus unik dalam satu departemen
2. Divisi tidak boleh dihapus jika masih memiliki posisi aktif
3. Divisi harus memiliki parent departemen

#### Indexes

- Primary key: `id`
- Foreign key: `department_id`
- Composite unique: `department_id`, `name`
- Status index: `is_active`

### 5. Positions Entity

#### Overview

Entitas untuk posisi/jabatan dalam organisasi.

#### Attributes

| Attribute    | Type      | Constraints             | Description           |
| ------------ | --------- | ----------------------- | --------------------- |
| `id`         | bigint    | PK, Not Null            | Primary key posisi    |
| `name`       | string    | Not Null                | Nama posisi/jabatan   |
| `is_active`  | boolean   | Not Null, Default: true | Status aktif posisi   |
| `created_at` | timestamp | Not Null                | Timestamp pembuatan   |
| `updated_at` | timestamp | Not Null                | Timestamp update      |
| `deleted_at` | timestamp | Nullable                | Timestamp soft delete |

#### Business Rules

1. Nama posisi harus unik
2. Posisi tidak boleh dihapus jika masih digunakan dalam struktur aktif
3. Status aktif harus dipertahankan untuk data historis

#### Indexes

- Primary key: `id`
- Search index: `name`
- Status index: `is_active`

### 6. Structural Periods Entity

#### Overview

Entitas untuk periode struktur organisasi.

#### Attributes

| Attribute    | Type      | Constraints             | Description           |
| ------------ | --------- | ----------------------- | --------------------- |
| `id`         | bigint    | PK, Not Null            | Primary key periode   |
| `start_date` | date      | Not Null                | Tanggal mulai periode |
| `end_date`   | date      | Not Null                | Tanggal akhir periode |
| `is_active`  | boolean   | Not Null, Default: true | Status aktif periode  |
| `created_at` | timestamp | Not Null                | Timestamp pembuatan   |
| `updated_at` | timestamp | Not Null                | Timestamp update      |
| `deleted_at` | timestamp | Nullable                | Timestamp soft delete |

#### Business Rules

1. Periode tidak boleh overlap
2. Tanggal akhir harus lebih besar dari tanggal mulai
3. Hanya satu periode yang boleh aktif pada satu waktu

#### Indexes

- Primary key: `id`
- Date range index: `start_date`, `end_date`
- Status index: `is_active`

### 7. Structurals Entity

#### Overview

Entitas untuk menghubungkan pegawai dengan posisi dalam struktur organisasi.

#### Attributes

| Attribute              | Type      | Constraints  | Description                |
| ---------------------- | --------- | ------------ | -------------------------- |
| `id`                   | string    | PK, Not Null | Primary key struktur       |
| `structural_period_id` | bigint    | FK, Not Null | Referensi periode struktur |
| `department_id`        | bigint    | FK, Not Null | Referensi departemen       |
| `division_id`          | bigint    | FK, Nullable | Referensi divisi           |
| `position_id`          | bigint    | FK, Not Null | Referensi posisi           |
| `employee_nik`         | string    | FK, Not Null | Referensi pegawai (NIK)    |
| `created_at`           | timestamp | Not Null     | Timestamp pembuatan        |
| `updated_at`           | timestamp | Not Null     | Timestamp update           |
| `deleted_at`           | timestamp | Nullable     | Timestamp soft delete      |

#### Business Rules

1. Satu pegawai dapat memiliki satu posisi utama dalam satu periode
2. Posisi harus dalam departemen yang valid
3. Divisi harus dalam departemen yang sama
4. Tidak boleh ada konflik assignment dalam periode yang sama

#### Indexes

- Primary key: `id`
- Foreign keys: `structural_period_id`, `department_id`, `division_id`, `position_id`, `employee_nik`
- Unique constraint: `structural_period_id`, `department_id`, `division_id`, `position_id`
- Employee-period index: `employee_nik`, `structural_period_id`

### 8. Sessions Entity

#### Overview

Entitas untuk session pengguna sistem.

#### Attributes

| Attribute       | Type     | Constraints  | Description             |
| --------------- | -------- | ------------ | ----------------------- |
| `id`            | string   | PK, Not Null | Primary key session     |
| `user_id`       | bigint   | FK, Nullable | Referensi user          |
| `ip_address`    | string   | Nullable     | IP address user         |
| `user_agent`    | text     | Nullable     | User agent browser      |
| `payload`       | longtext | Not Null     | Session payload         |
| `last_activity` | integer  | Not Null     | Timestamp last activity |

#### Business Rules

1. Session memiliki expiration time
2. Session dapat di-invalidate secara manual
3. Multiple sessions per user diperbolehkan

#### Indexes

- Primary key: `id`
- Foreign key: `user_id`
- Activity index: `last_activity`

## 📚 Reference Entities

### 1. Religions Entity

#### Overview

Entitas referensi untuk agama.

#### Attributes

| Attribute    | Type      | Constraints             | Description           |
| ------------ | --------- | ----------------------- | --------------------- |
| `id`         | bigint    | PK, Not Null            | Primary key agama     |
| `name`       | string    | Not Null                | Nama agama            |
| `is_active`  | boolean   | Not Null, Default: true | Status aktif agama    |
| `created_at` | timestamp | Not Null                | Timestamp pembuatan   |
| `updated_at` | timestamp | Not Null                | Timestamp update      |
| `deleted_at` | timestamp | Nullable                | Timestamp soft delete |

#### Business Rules

1. Nama agama harus unik
2. Agama tidak boleh dihapus jika masih digunakan

#### Indexes

- Primary key: `id`
- Search index: `name`
- Status index: `is_active`

### 2. Educations Entity

#### Overview

Entitas referensi untuk tingkat pendidikan.

#### Attributes

| Attribute    | Type      | Constraints             | Description             |
| ------------ | --------- | ----------------------- | ----------------------- |
| `id`         | bigint    | PK, Not Null            | Primary key pendidikan  |
| `level`      | string    | Not Null                | Tingkat pendidikan      |
| `name`       | string    | Not Null                | Nama pendidikan         |
| `is_active`  | boolean   | Not Null, Default: true | Status aktif pendidikan |
| `created_at` | timestamp | Not Null                | Timestamp pembuatan     |
| `updated_at` | timestamp | Not Null                | Timestamp update        |
| `deleted_at` | timestamp | Nullable                | Timestamp soft delete   |

#### Business Rules

1. Kombinasi tingkat dan nama harus unik
2. Pendidikan tidak boleh dihapus jika masih digunakan

#### Indexes

- Primary key: `id`
- Search index: `level`, `name`
- Status index: `is_active`

### 3. Fields of Study Entity

#### Overview

Entitas referensi untuk bidang ilmu.

#### Attributes

| Attribute    | Type      | Constraints             | Description              |
| ------------ | --------- | ----------------------- | ------------------------ |
| `id`         | bigint    | PK, Not Null            | Primary key bidang ilmu  |
| `name`       | string    | Not Null                | Nama bidang ilmu         |
| `code`       | string    | UK, Not Null            | Kode bidang ilmu         |
| `is_active`  | boolean   | Not Null, Default: true | Status aktif bidang ilmu |
| `created_at` | timestamp | Not Null                | Timestamp pembuatan      |
| `updated_at` | timestamp | Not Null                | Timestamp update         |
| `deleted_at` | timestamp | Nullable                | Timestamp soft delete    |

#### Business Rules

1. Kode bidang ilmu harus unik
2. Bidang ilmu tidak boleh dihapus jika masih digunakan

#### Indexes

- Primary key: `id`
- Unique index: `code`
- Search index: `name`
- Status index: `is_active`

### 4. Employment Status Entity

#### Overview

Entitas referensi untuk status pegawai.

#### Attributes

| Attribute     | Type      | Constraints             | Description           |
| ------------- | --------- | ----------------------- | --------------------- |
| `id`          | bigint    | PK, Not Null            | Primary key status    |
| `name`        | string    | Not Null                | Nama status pegawai   |
| `description` | text      | Nullable                | Deskripsi status      |
| `is_active`   | boolean   | Not Null, Default: true | Status aktif          |
| `created_at`  | timestamp | Not Null                | Timestamp pembuatan   |
| `updated_at`  | timestamp | Not Null                | Timestamp update      |
| `deleted_at`  | timestamp | Nullable                | Timestamp soft delete |

#### Business Rules

1. Nama status harus unik
2. Status tidak boleh dihapus jika masih digunakan

#### Indexes

- Primary key: `id`
- Search index: `name`
- Status index: `is_active`

### 5. Employment Types Entity

#### Overview

Entitas referensi untuk jenis ikatan kerja.

#### Attributes

| Attribute    | Type      | Constraints             | Description                    |
| ------------ | --------- | ----------------------- | ------------------------------ |
| `id`         | bigint    | PK, Not Null            | Primary key jenis ikatan kerja |
| `nama`       | string    | Not Null                | Nama jenis ikatan kerja        |
| `deskripsi`  | text      | Nullable                | Deskripsi jenis ikatan kerja   |
| `isActive`   | boolean   | Not Null, Default: true | Status aktif                   |
| `created_at` | timestamp | Not Null                | Timestamp pembuatan            |
| `updated_at` | timestamp | Not Null                | Timestamp update               |
| `deleted_at` | timestamp | Nullable                | Timestamp soft delete          |

#### Business Rules

1. Nama jenis ikatan kerja harus unik
2. Jenis ikatan kerja tidak boleh dihapus jika masih digunakan

#### Indexes

- Primary key: `id`
- Search index: `nama`
- Status index: `isActive`

### 6. Activities Entity

#### Overview

Entitas referensi untuk aktivitas pegawai.

#### Attributes

| Attribute    | Type      | Constraints             | Description           |
| ------------ | --------- | ----------------------- | --------------------- |
| `id`         | bigint    | PK, Not Null            | Primary key aktivitas |
| `nama`       | string    | Not Null                | Nama aktivitas        |
| `deskripsi`  | text      | Nullable                | Deskripsi aktivitas   |
| `isActive`   | boolean   | Not Null, Default: true | Status aktif          |
| `created_at` | timestamp | Not Null                | Timestamp pembuatan   |
| `updated_at` | timestamp | Not Null                | Timestamp update      |
| `deleted_at` | timestamp | Nullable                | Timestamp soft delete |

#### Business Rules

1. Nama aktivitas harus unik
2. Aktivitas tidak boleh dihapus jika masih digunakan

#### Indexes

- Primary key: `id`
- Search index: `nama`
- Status index: `isActive`

### 7. Jabfungs Entity

#### Overview

Entitas referensi untuk jabatan fungsional akademik.

#### Attributes

| Attribute            | Type      | Constraints          | Description                    |
| -------------------- | --------- | -------------------- | ------------------------------ |
| `id`                 | bigint    | PK, Not Null         | Primary key jabatan fungsional |
| `jabfung`            | string    | Not Null             | Nama jabatan fungsional        |
| `subJabfung`         | string    | Nullable             | Sub jabatan fungsional         |
| `angkaKreditMinimum` | integer   | Not Null, Default: 0 | Angka kredit minimum           |
| `created_at`         | timestamp | Not Null             | Timestamp pembuatan            |
| `updated_at`         | timestamp | Not Null             | Timestamp update               |
| `deleted_at`         | timestamp | Nullable             | Timestamp soft delete          |

#### Business Rules

1. Kombinasi jabfung dan subJabfung harus unik
2. Angka kredit minimum harus >= 0
3. Jabatan fungsional tidak boleh dihapus jika masih digunakan

#### Indexes

- Primary key: `id`
- Search index: `jabfung`, `subJabfung`
- Credit index: `angkaKreditMinimum`

## 📝 Audit Entities

### 1. Employee Histories Entity

#### Overview

Entitas untuk audit trail perubahan data pegawai.

#### Attributes

| Attribute      | Type      | Constraints  | Description                   |
| -------------- | --------- | ------------ | ----------------------------- |
| `id`           | bigint    | PK, Not Null | Primary key history           |
| `employee_nik` | string    | FK, Not Null | Referensi pegawai (NIK)       |
| `field_name`   | string    | Not Null     | Nama field yang berubah       |
| `old_value`    | text      | Nullable     | Nilai lama                    |
| `new_value`    | text      | Nullable     | Nilai baru                    |
| `changed_by`   | string    | Not Null     | User yang melakukan perubahan |
| `changed_at`   | timestamp | Not Null     | Timestamp perubahan           |

#### Business Rules

1. Setiap perubahan data pegawai harus dicatat
2. History tidak boleh dihapus
3. Timestamp perubahan harus akurat

#### Indexes

- Primary key: `id`
- Foreign key: `employee_nik`
- Employee-time index: `employee_nik`, `changed_at`
- Field index: `field_name`

### 2. Structural Histories Entity

#### Overview

Entitas untuk audit trail perubahan struktur organisasi.

#### Attributes

| Attribute      | Type      | Constraints  | Description             |
| -------------- | --------- | ------------ | ----------------------- |
| `id`           | bigint    | PK, Not Null | Primary key history     |
| `employee_nik` | string    | FK, Not Null | Referensi pegawai (NIK) |
| `status`       | string    | Not Null     | Status perubahan        |
| `description`  | text      | Nullable     | Deskripsi perubahan     |
| `created_at`   | timestamp | Not Null     | Timestamp pembuatan     |
| `updated_at`   | timestamp | Not Null     | Timestamp update        |
| `deleted_at`   | timestamp | Nullable     | Timestamp soft delete   |

#### Business Rules

1. Setiap perubahan struktur harus dicatat
2. Status perubahan harus jelas
3. History tidak boleh dihapus

#### Indexes

- Primary key: `id`
- Foreign key: `employee_nik`
- Employee-time index: `employee_nik`, `created_at`
- Status index: `status`

### 3. Password Reset Tokens Entity

#### Overview

Entitas untuk token reset password.

#### Attributes

| Attribute    | Type      | Constraints  | Description          |
| ------------ | --------- | ------------ | -------------------- |
| `email`      | string    | PK, Not Null | Email user           |
| `token`      | string    | Not Null     | Token reset password |
| `created_at` | timestamp | Not Null     | Timestamp pembuatan  |

#### Business Rules

1. Token memiliki expiration time
2. Token harus unik
3. Token dihapus setelah digunakan

#### Indexes

- Primary key: `email`
- Token index: `token`

## 🔧 Entity Design Patterns

### 1. Soft Delete Pattern

Semua entitas utama menggunakan soft delete dengan field `deleted_at` untuk mempertahankan data historis.

### 2. Timestamp Pattern

Semua entitas memiliki `created_at` dan `updated_at` untuk tracking waktu.

### 3. Active Status Pattern

Entitas referensi menggunakan `isActive` untuk mengontrol status aktif.

### 4. Foreign Key Pattern

Semua relasi menggunakan foreign key constraints untuk referential integrity.

### 5. Unique Constraint Pattern

Field yang harus unik menggunakan unique constraints.

### 6. Index Strategy Pattern

- Primary key indexes
- Foreign key indexes
- Search indexes untuk field yang sering dicari
- Composite indexes untuk kombinasi field

---

**Dokumen ini dibuat**: 2024-01-01  
**Versi**: v1.0.0  
**Status**: Complete
