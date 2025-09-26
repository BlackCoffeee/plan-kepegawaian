# Relationship Analysis - SIMPEG UNISM

## 📋 Overview

Dokumen ini berisi analisis relasi antar entitas untuk database sistem SIMPEG (Sistem Informasi Manajemen Pegawai) Universitas Islam Negeri Sultan Maulana Hasanuddin.

## 🔗 Hierarki Relasi

### 1. Organizational Hierarchy Relationships

#### 1.1 Departements → Divisions

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu departemen dapat memiliki banyak divisi
- **Participation**:
  - Departements: Partial (tidak semua departemen memiliki divisi)
  - Divisions: Total (setiap divisi harus memiliki parent departemen)
- **Foreign Key**: `divisions.departement_id` → `departements.id`
- **Business Rule**: Divisi tidak boleh dihapus jika masih memiliki posisi aktif

```sql
ALTER TABLE divisions
ADD CONSTRAINT fk_divisions_departement
FOREIGN KEY (departement_id) REFERENCES departements(id)
ON DELETE RESTRICT;
```

#### 1.2 Divisions → Positions (Through Structurals)

**Relasi**: Many-to-Many (M:N) dengan junction table

- **Cardinality**: Satu divisi dapat memiliki banyak posisi, satu posisi dapat ada di banyak divisi
- **Junction Table**: `structurals`
- **Foreign Keys**:
  - `structurals.division_id` → `divisions.id`
  - `structurals.position_id` → `positions.id`
- **Business Rule**: Posisi dalam divisi harus sesuai dengan periode struktur

### 2. Structural Organization Relationships

#### 2.1 Structural Periods → Structurals

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu periode struktur dapat memiliki banyak assignment struktur
- **Participation**:
  - Structural Periods: Total (setiap periode harus memiliki assignment)
  - Structurals: Total (setiap struktur harus memiliki periode)
- **Foreign Key**: `structurals.structural_period_id` → `structural_periods.id`
- **Business Rule**: Periode tidak boleh overlap

```sql
ALTER TABLE structurals
ADD CONSTRAINT fk_structurals_period
FOREIGN KEY (structural_period_id) REFERENCES structural_periods(id)
ON DELETE CASCADE;
```

#### 2.2 Departements → Structurals

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu departemen dapat memiliki banyak assignment dalam struktur
- **Participation**:
  - Departements: Partial
  - Structurals: Total
- **Foreign Key**: `structurals.departement_id` → `departements.id`
- **Business Rule**: Assignment departemen harus valid

#### 2.3 Positions → Structurals

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu posisi dapat diisi oleh banyak pegawai dalam periode berbeda
- **Participation**:
  - Positions: Partial
  - Structurals: Total
- **Foreign Key**: `structurals.position_id` → `positions.id`
- **Business Rule**: Posisi tidak boleh konflik dalam periode yang sama

### 3. Employee Management Relationships

#### 3.1 Employees → Structurals

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu pegawai dapat memiliki banyak posisi dalam struktur (dalam periode berbeda)
- **Participation**:
  - Employees: Partial (tidak semua pegawai memiliki posisi struktur)
  - Structurals: Total (setiap struktur harus memiliki pegawai)
- **Foreign Key**: `structurals.employee_nik` → `employees.nik`
- **Business Rule**: Satu pegawai tidak boleh memiliki posisi konflik dalam periode yang sama

```sql
ALTER TABLE structurals
ADD CONSTRAINT fk_structurals_employee
FOREIGN KEY (employee_nik) REFERENCES employees(nik)
ON DELETE CASCADE;
```

#### 3.2 Employees → Employee Histories

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu pegawai dapat memiliki banyak riwayat perubahan data
- **Participation**:
  - Employees: Partial
  - Employee Histories: Total
- **Foreign Key**: `employee_histories.employee_nik` → `employees.nik`
- **Business Rule**: Setiap perubahan data pegawai harus dicatat

```sql
ALTER TABLE employee_histories
ADD CONSTRAINT fk_employee_histories_employee
FOREIGN KEY (employee_nik) REFERENCES employees(nik)
ON DELETE CASCADE;
```

#### 3.3 Employees → Structural Histories

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu pegawai dapat memiliki banyak riwayat perubahan struktur
- **Participation**:
  - Employees: Partial
  - Structural Histories: Total
- **Foreign Key**: `structural_histories.employee_nik` → `employees.nik`
- **Business Rule**: Setiap perubahan struktur harus dicatat

### 4. Reference Data Relationships

#### 4.1 Religions → Employees

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu agama dapat dimiliki oleh banyak pegawai
- **Participation**:
  - Religions: Partial
  - Employees: Partial (pegawai dapat tidak memiliki agama)
- **Foreign Key**: `employees.agama_id` → `religions.id`
- **Business Rule**: Agama tidak boleh dihapus jika masih digunakan

```sql
ALTER TABLE employees
ADD CONSTRAINT fk_employees_religion
FOREIGN KEY (agama_id) REFERENCES religions(id)
ON DELETE SET NULL;
```

#### 4.2 Educations → Employees

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu tingkat pendidikan dapat dimiliki oleh banyak pegawai
- **Participation**:
  - Educations: Partial
  - Employees: Partial
- **Foreign Key**: `employees.pendidikan_id` → `educations.id`
- **Business Rule**: Pendidikan tidak boleh dihapus jika masih digunakan

#### 4.3 Fields of Study → Employees

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu bidang ilmu dapat dimiliki oleh banyak pegawai
- **Participation**:
  - Fields of Study: Partial
  - Employees: Partial
- **Foreign Key**: `employees.bidangIlmu_id` → `fields_of_study.id`
- **Business Rule**: Bidang ilmu tidak boleh dihapus jika masih digunakan

#### 4.4 Employment Status → Employees

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu status pegawai dapat dimiliki oleh banyak pegawai
- **Participation**:
  - Employment Status: Partial
  - Employees: Partial
- **Foreign Key**: `employees.status_id` → `employment_status.id`
- **Business Rule**: Status tidak boleh dihapus jika masih digunakan

#### 4.5 Employment Types → Employees

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu jenis ikatan kerja dapat dimiliki oleh banyak pegawai
- **Participation**:
  - Employment Types: Partial
  - Employees: Partial
- **Foreign Key**: `employees.ikatanKerja_id` → `employment_types.id`
- **Business Rule**: Jenis ikatan kerja tidak boleh dihapus jika masih digunakan

#### 4.6 Activities → Employees

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu aktivitas dapat dimiliki oleh banyak pegawai
- **Participation**:
  - Activities: Partial
  - Employees: Partial
- **Foreign Key**: `employees.aktivitas_id` → `activities.id`
- **Business Rule**: Aktivitas tidak boleh dihapus jika masih digunakan

#### 4.7 Jabfungs → Employees

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu jabatan fungsional dapat dimiliki oleh banyak pegawai
- **Participation**:
  - Jabfungs: Partial
  - Employees: Partial
- **Foreign Key**: `employees.jafung` → `jabfungs.id`
- **Business Rule**: Jabatan fungsional tidak boleh dihapus jika masih digunakan

### 5. Authentication Relationships

#### 5.1 Users → Sessions

**Relasi**: One-to-Many (1:N)

- **Cardinality**: Satu user dapat memiliki banyak session
- **Participation**:
  - Users: Partial (user dapat tidak memiliki session aktif)
  - Sessions: Partial (session dapat tidak memiliki user)
- **Foreign Key**: `sessions.user_id` → `users.id`
- **Business Rule**: Session memiliki expiration time

```sql
ALTER TABLE sessions
ADD CONSTRAINT fk_sessions_user
FOREIGN KEY (user_id) REFERENCES users(id)
ON DELETE CASCADE;
```

## 🔧 Referential Integrity Rules

### 1. Cascade Rules

| Parent Table       | Child Table          | Action | Rule     |
| ------------------ | -------------------- | ------ | -------- |
| structural_periods | structurals          | DELETE | CASCADE  |
| departements       | structurals          | DELETE | RESTRICT |
| divisions          | structurals          | DELETE | RESTRICT |
| positions          | structurals          | DELETE | RESTRICT |
| employees          | structurals          | DELETE | CASCADE  |
| employees          | employee_histories   | DELETE | CASCADE  |
| employees          | structural_histories | DELETE | CASCADE  |
| users              | sessions             | DELETE | CASCADE  |

### 2. Set Null Rules

| Parent Table      | Child Table | Action | Rule     |
| ----------------- | ----------- | ------ | -------- |
| religions         | employees   | DELETE | SET NULL |
| educations        | employees   | DELETE | SET NULL |
| fields_of_study   | employees   | DELETE | SET NULL |
| employment_status | employees   | DELETE | SET NULL |
| employment_types  | employees   | DELETE | SET NULL |
| activities        | employees   | DELETE | SET NULL |
| jabfungs          | employees   | DELETE | SET NULL |

### 3. Restrict Rules

| Parent Table | Child Table | Action | Rule     |
| ------------ | ----------- | ------ | -------- |
| departements | divisions   | DELETE | RESTRICT |
| divisions    | structurals | DELETE | RESTRICT |

## 📊 Relationship Cardinality Summary

### One-to-Many Relationships

1. **Departements** → **Divisions** (1:N)
2. **Structural Periods** → **Structurals** (1:N)
3. **Departements** → **Structurals** (1:N)
4. **Divisions** → **Structurals** (1:N)
5. **Positions** → **Structurals** (1:N)
6. **Employees** → **Structurals** (1:N)
7. **Employees** → **Employee Histories** (1:N)
8. **Employees** → **Structural Histories** (1:N)
9. **Users** → **Sessions** (1:N)
10. **Religions** → **Employees** (1:N)
11. **Educations** → **Employees** (1:N)
12. **Fields of Study** → **Employees** (1:N)
13. **Employment Status** → **Employees** (1:N)
14. **Employment Types** → **Employees** (1:N)
15. **Activities** → **Employees** (1:N)
16. **Jabfungs** → **Employees** (1:N)

### Many-to-Many Relationships

1. **Employees** ↔ **Positions** (Through Structurals) (M:N)
2. **Employees** ↔ **Departements** (Through Structurals) (M:N)
3. **Employees** ↔ **Divisions** (Through Structurals) (M:N)

## 🔍 Relationship Constraints

### 1. Unique Constraints

```sql
-- Unique structural assignment per period
ALTER TABLE structurals
ADD CONSTRAINT unique_structural_assignment
UNIQUE (structural_period_id, departement_id, division_id, position_id);

-- Unique employee NIK
ALTER TABLE employees
ADD CONSTRAINT unique_employee_nik
UNIQUE (nik);

-- Unique employee ID from Sister
ALTER TABLE employees
ADD CONSTRAINT unique_employee_id_sp
UNIQUE (id_sp);

-- Unique username
ALTER TABLE users
ADD CONSTRAINT unique_username
UNIQUE (username);

-- Unique phone number
ALTER TABLE users
ADD CONSTRAINT unique_phone
UNIQUE (phone);
```

### 2. Check Constraints

```sql
-- Employee gender constraint
ALTER TABLE employees
ADD CONSTRAINT check_gender
CHECK (jenisKelamin IN ('L', 'P'));

-- Structural period date constraint
ALTER TABLE structural_periods
ADD CONSTRAINT check_period_dates
CHECK (sampaiTahun > dariTahun);

-- Active status constraint
ALTER TABLE departements
ADD CONSTRAINT check_is_active
CHECK (isActive IN (0, 1));

-- Credit minimum constraint
ALTER TABLE jabfungs
ADD CONSTRAINT check_credit_minimum
CHECK (angkaKreditMinimum >= 0);
```

### 3. Index Constraints

```sql
-- Performance indexes for relationships
CREATE INDEX idx_structurals_employee_period
ON structurals (employee_nik, structural_period_id);

CREATE INDEX idx_employee_histories_employee_time
ON employee_histories (employee_nik, changed_at);

CREATE INDEX idx_structural_histories_employee_time
ON structural_histories (employee_nik, created_at);

CREATE INDEX idx_sessions_user_activity
ON sessions (user_id, last_activity);
```

## 🎯 Business Rules for Relationships

### 1. Organizational Structure Rules

1. **Hierarchy Integrity**: Divisi harus dalam departemen yang valid
2. **Period Consistency**: Assignment struktur harus dalam periode yang valid
3. **No Overlap**: Pegawai tidak boleh memiliki posisi konflik dalam periode yang sama
4. **Active Status**: Hanya struktur aktif yang dapat di-assign pegawai

### 2. Employee Management Rules

1. **Unique Assignment**: Satu pegawai tidak boleh memiliki posisi yang sama dalam periode yang sama
2. **Data Consistency**: Data pegawai harus konsisten dengan referensi
3. **Audit Trail**: Semua perubahan data pegawai harus dicatat
4. **Soft Delete**: Data pegawai tidak boleh dihapus permanen

### 3. Reference Data Rules

1. **Referential Integrity**: Data referensi tidak boleh dihapus jika masih digunakan
2. **Active Status**: Hanya data referensi aktif yang dapat digunakan
3. **Data Consistency**: Data referensi harus sinkron dengan sistem Sister
4. **Validation**: Data referensi harus valid sebelum digunakan

### 4. Authentication Rules

1. **Session Management**: Session memiliki expiration time
2. **User Validation**: User harus valid sebelum login
3. **Security**: Session harus secure dan dapat di-invalidate
4. **Multiple Sessions**: User dapat memiliki multiple sessions

## 📈 Performance Considerations

### 1. Relationship Query Optimization

- **Eager Loading**: Load related data dalam satu query
- **Lazy Loading**: Load related data saat dibutuhkan
- **Index Optimization**: Optimize indexes untuk relationship queries
- **Query Caching**: Cache query results untuk performance

### 2. Relationship Maintenance

- **Cascade Updates**: Automatic update related data
- **Constraint Validation**: Validate constraints sebelum insert/update
- **Relationship Cleanup**: Clean up orphaned relationships
- **Performance Monitoring**: Monitor relationship query performance

---

**Dokumen ini dibuat**: 2024-01-01  
**Versi**: v1.0.0  
**Status**: Complete
