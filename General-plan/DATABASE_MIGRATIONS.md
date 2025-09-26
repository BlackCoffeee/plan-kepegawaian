# Database Migrations SIMPEG UNISM

## 📋 Overview

Dokumen ini berisi migration scripts untuk membuat struktur database SIMPEG UNISM berdasarkan analisis ERD yang telah dibuat.

## 🎯 Migration Strategy

- **Laravel Migrations**: Menggunakan Laravel migration system
- **Foreign Key Constraints**: Semua relasi menggunakan foreign key constraints
- **Indexing**: Index pada field yang sering digunakan untuk query
- **Soft Deletes**: Implementasi soft delete untuk data historis

## 📊 Migration Files

### 1. Reference Tables (Migration Order: 1-7)

#### 001_create_religions_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateReligionsTable extends Migration
{
    public function up()
    {
        Schema::create('religions', function (Blueprint $table) {
            $table->id();
            $table->string('nama');
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down()
    {
        Schema::dropIfExists('religions');
    }
}
```

#### 002_create_educations_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEducationsTable extends Migration
{
    public function up()
    {
        Schema::create('educations', function (Blueprint $table) {
            $table->id();
            $table->string('tingkat');
            $table->string('nama');
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down()
    {
        Schema::dropIfExists('educations');
    }
}
```

#### 003_create_fields_of_study_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateFieldsOfStudyTable extends Migration
{
    public function up()
    {
        Schema::create('fields_of_study', function (Blueprint $table) {
            $table->id();
            $table->string('nama');
            $table->string('kode')->unique();
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down()
    {
        Schema::dropIfExists('fields_of_study');
    }
}
```

#### 004_create_employment_status_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEmploymentStatusTable extends Migration
{
    public function up()
    {
        Schema::create('employment_status', function (Blueprint $table) {
            $table->id();
            $table->string('nama');
            $table->text('deskripsi')->nullable();
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down()
    {
        Schema::dropIfExists('employment_status');
    }
}
```

#### 005_create_employment_types_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEmploymentTypesTable extends Migration
{
    public function up()
    {
        Schema::create('employment_types', function (Blueprint $table) {
            $table->id();
            $table->string('nama');
            $table->text('deskripsi')->nullable();
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down()
    {
        Schema::dropIfExists('employment_types');
    }
}
```

#### 006_create_activities_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateActivitiesTable extends Migration
{
    public function up()
    {
        Schema::create('activities', function (Blueprint $table) {
            $table->id();
            $table->string('nama');
            $table->text('deskripsi')->nullable();
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down()
    {
        Schema::dropIfExists('activities');
    }
}
```

#### 007_create_jabfungs_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateJabfungsTable extends Migration
{
    public function up()
    {
        Schema::create('jabfungs', function (Blueprint $table) {
            $table->id();
            $table->string('jabfung');
            $table->string('subJabfung')->nullable();
            $table->integer('angkaKreditMinimum')->default(0);
            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down()
    {
        Schema::dropIfExists('jabfungs');
    }
}
```

### 2. Core Tables (Migration Order: 8-12)

#### 008_create_users_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateUsersTable extends Migration
{
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('username')->unique();
            $table->string('phone')->unique();
            $table->char('prodi')->nullable();
            $table->bigInteger('oauth_client_role_id')->nullable();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('remember_token')->nullable();
            $table->timestamps();

            $table->index(['username', 'phone']);
        });
    }

    public function down()
    {
        Schema::dropIfExists('users');
    }
}
```

#### 009_create_departements_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateDepartementsTable extends Migration
{
    public function up()
    {
        Schema::create('departements', function (Blueprint $table) {
            $table->id();
            $table->string('namaDepartement');
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();

            $table->index('namaDepartement');
        });
    }

    public function down()
    {
        Schema::dropIfExists('departements');
    }
}
```

#### 010_create_divisions_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateDivisionsTable extends Migration
{
    public function up()
    {
        Schema::create('divisions', function (Blueprint $table) {
            $table->id();
            $table->foreignId('departement_id')->constrained('departements')->onDelete('cascade');
            $table->string('namaDivision');
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();

            $table->index(['departement_id', 'namaDivision']);
        });
    }

    public function down()
    {
        Schema::dropIfExists('divisions');
    }
}
```

#### 011_create_positions_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePositionsTable extends Migration
{
    public function up()
    {
        Schema::create('positions', function (Blueprint $table) {
            $table->id();
            $table->string('namaPosition');
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();

            $table->index('namaPosition');
        });
    }

    public function down()
    {
        Schema::dropIfExists('positions');
    }
}
```

#### 012_create_structural_periods_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateStructuralPeriodsTable extends Migration
{
    public function up()
    {
        Schema::create('structural_periods', function (Blueprint $table) {
            $table->id();
            $table->date('dariTahun');
            $table->date('sampaiTahun');
            $table->boolean('isActive')->default(true);
            $table->timestamps();
            $table->softDeletes();

            $table->index(['dariTahun', 'sampaiTahun']);
        });
    }

    public function down()
    {
        Schema::dropIfExists('structural_periods');
    }
}
```

### 3. Employee Tables (Migration Order: 13-14)

#### 013_create_employees_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEmployeesTable extends Migration
{
    public function up()
    {
        Schema::create('employees', function (Blueprint $table) {
            $table->string('id')->primary();
            $table->string('id_sp')->unique();
            $table->string('nik')->unique();
            $table->string('nidn')->nullable();
            $table->string('nama');
            $table->string('tempatLahir')->nullable();
            $table->date('tanggalLahir')->nullable();
            $table->enum('jenisKelamin', ['L', 'P'])->nullable();
            $table->string('gelarDepan')->nullable();
            $table->string('gelarBelakang')->nullable();

            // Foreign Keys to reference tables
            $table->foreignId('bidangIlmu_id')->nullable()->constrained('fields_of_study')->onDelete('set null');
            $table->foreignId('pendidikan_id')->nullable()->constrained('educations')->onDelete('set null');
            $table->foreignId('agama_id')->nullable()->constrained('religions')->onDelete('set null');
            $table->foreignId('status_id')->nullable()->constrained('employment_status')->onDelete('set null');
            $table->foreignId('ikatanKerja_id')->nullable()->constrained('employment_types')->onDelete('set null');
            $table->foreignId('aktivitas_id')->nullable()->constrained('activities')->onDelete('set null');
            $table->foreignId('jafung')->nullable()->constrained('jabfungs')->onDelete('set null');

            $table->string('ektp')->nullable();
            $table->string('nokk')->nullable();
            $table->text('alamatKtp')->nullable();
            $table->text('alamat')->nullable();
            $table->string('noWa')->nullable();
            $table->string('email')->nullable();
            $table->string('foto')->nullable();
            $table->date('tanggalMasukKerja')->nullable();
            $table->string('peran')->nullable();
            $table->text('tugasUtama')->nullable();
            $table->string('serdos')->nullable();

            $table->timestamps();
            $table->softDeletes();

            // Indexes
            $table->index('nik');
            $table->index('nama');
            $table->index('email');
            $table->index(['bidangIlmu_id', 'pendidikan_id']);
        });
    }

    public function down()
    {
        Schema::dropIfExists('employees');
    }
}
```

#### 014_create_employee_histories_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEmployeeHistoriesTable extends Migration
{
    public function up()
    {
        Schema::create('employee_histories', function (Blueprint $table) {
            $table->id();
            $table->string('employee_nik');
            $table->string('field_name');
            $table->text('old_value')->nullable();
            $table->text('new_value')->nullable();
            $table->string('changed_by');
            $table->timestamp('changed_at');

            $table->foreign('employee_nik')->references('nik')->on('employees')->onDelete('cascade');

            $table->index(['employee_nik', 'changed_at']);
            $table->index('field_name');
        });
    }

    public function down()
    {
        Schema::dropIfExists('employee_histories');
    }
}
```

### 4. Structural Tables (Migration Order: 15-16)

#### 015_create_structurals_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateStructuralsTable extends Migration
{
    public function up()
    {
        Schema::create('structurals', function (Blueprint $table) {
            $table->string('id')->primary();
            $table->foreignId('structural_period_id')->constrained('structural_periods')->onDelete('cascade');
            $table->foreignId('departement_id')->constrained('departements')->onDelete('cascade');
            $table->foreignId('division_id')->nullable()->constrained('divisions')->onDelete('cascade');
            $table->foreignId('position_id')->constrained('positions')->onDelete('cascade');
            $table->string('employee_nik');

            $table->foreign('employee_nik')->references('nik')->on('employees')->onDelete('cascade');

            $table->timestamps();
            $table->softDeletes();

            // Indexes
            $table->index(['structural_period_id', 'departement_id']);
            $table->index(['employee_nik', 'structural_period_id']);
            $table->unique(['structural_period_id', 'departement_id', 'division_id', 'position_id'], 'unique_structural_position');
        });
    }

    public function down()
    {
        Schema::dropIfExists('structurals');
    }
}
```

#### 016_create_structural_histories_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateStructuralHistoriesTable extends Migration
{
    public function up()
    {
        Schema::create('structural_histories', function (Blueprint $table) {
            $table->id();
            $table->string('employee_nik');
            $table->string('status');
            $table->text('description')->nullable();

            $table->foreign('employee_nik')->references('nik')->on('employees')->onDelete('cascade');

            $table->timestamps();
            $table->softDeletes();

            $table->index(['employee_nik', 'created_at']);
        });
    }

    public function down()
    {
        Schema::dropIfExists('structural_histories');
    }
}
```

### 5. Authentication Tables (Migration Order: 17-18)

#### 017_create_sessions_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateSessionsTable extends Migration
{
    public function up()
    {
        Schema::create('sessions', function (Blueprint $table) {
            $table->string('id')->primary();
            $table->foreignId('user_id')->nullable()->index();
            $table->string('ip_address', 45)->nullable();
            $table->text('user_agent')->nullable();
            $table->longText('payload');
            $table->integer('last_activity')->index();
        });
    }

    public function down()
    {
        Schema::dropIfExists('sessions');
    }
}
```

#### 018_create_password_reset_tokens_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePasswordResetTokensTable extends Migration
{
    public function up()
    {
        Schema::create('password_reset_tokens', function (Blueprint $table) {
            $table->string('email')->primary();
            $table->string('token');
            $table->timestamp('created_at')->nullable();
        });
    }

    public function down()
    {
        Schema::dropIfExists('password_reset_tokens');
    }
}
```

## 🌱 Seeder Data

### Reference Data Seeders

#### ReligionsSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class ReligionsSeeder extends Seeder
{
    public function run()
    {
        $religions = [
            ['nama' => 'Islam', 'isActive' => true],
            ['nama' => 'Kristen', 'isActive' => true],
            ['nama' => 'Katolik', 'isActive' => true],
            ['nama' => 'Hindu', 'isActive' => true],
            ['nama' => 'Buddha', 'isActive' => true],
            ['nama' => 'Konghucu', 'isActive' => true],
        ];

        DB::table('religions')->insert($religions);
    }
}
```

#### EducationsSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class EducationsSeeder extends Seeder
{
    public function run()
    {
        $educations = [
            ['tingkat' => 'SMA', 'nama' => 'Sekolah Menengah Atas', 'isActive' => true],
            ['tingkat' => 'D3', 'nama' => 'Diploma 3', 'isActive' => true],
            ['tingkat' => 'S1', 'nama' => 'Sarjana', 'isActive' => true],
            ['tingkat' => 'S2', 'nama' => 'Magister', 'isActive' => true],
            ['tingkat' => 'S3', 'nama' => 'Doktor', 'isActive' => true],
        ];

        DB::table('educations')->insert($educations);
    }
}
```

## 🚀 Migration Commands

### Run All Migrations

```bash
php artisan migrate
```

### Run Specific Migration

```bash
php artisan migrate --path=/database/migrations/001_create_religions_table.php
```

### Rollback Migrations

```bash
php artisan migrate:rollback
```

### Fresh Migration with Seeders

```bash
php artisan migrate:fresh --seed
```

## 📝 Notes

1. **Migration Order**: Pastikan migration dijalankan sesuai urutan yang telah ditentukan
2. **Foreign Key Constraints**: Semua relasi menggunakan foreign key constraints untuk data integrity
3. **Indexing**: Index ditambahkan pada field yang sering digunakan untuk query
4. **Soft Deletes**: Implementasi soft delete untuk mempertahankan data historis
5. **Data Seeding**: Gunakan seeders untuk data referensi yang statis

---

**Versi Dokumen**: v1.0.0  
**Tanggal**: 2024-01-01  
**Status**: Draft - Menunggu Review
