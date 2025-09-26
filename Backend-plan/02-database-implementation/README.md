# Database Implementation - SIMPEG Backend

## 📋 Overview

Implementasi database berdasarkan design yang telah dibuat di General Plan. Meliputi migrations, models, seeders, dan factories.

## 🗄️ Database Schema Implementation

### Migration Order

1. **Reference Tables** (1-7)

   - [ ] Religions
   - [ ] Educations
   - [ ] Fields of Study
   - [ ] Employment Status
   - [ ] Employment Types
   - [ ] Activities
   - [ ] Jabfungs

2. **Core Tables** (8-12)

   - [ ] Users
   - [ ] Departments
   - [ ] Divisions
   - [ ] Positions
   - [ ] Structural Periods

3. **Employee Tables** (13-14)

   - [ ] Employees
   - [ ] Employee Histories

4. **Structural Tables** (15-16)

   - [ ] Structurals
   - [ ] Structural Histories

5. **Authentication Tables** (17-18)
   - [ ] Sessions
   - [ ] Password Reset Tokens

## 📁 Migration Files Structure

```
database/migrations/
├── 001_create_religions_table.php
├── 002_create_educations_table.php
├── 003_create_fields_of_study_table.php
├── 004_create_employment_status_table.php
├── 005_create_employment_types_table.php
├── 006_create_activities_table.php
├── 007_create_jabfungs_table.php
├── 008_create_users_table.php
├── 009_create_departments_table.php
├── 010_create_divisions_table.php
├── 011_create_positions_table.php
├── 012_create_structural_periods_table.php
├── 013_create_employees_table.php
├── 014_create_employee_histories_table.php
├── 015_create_structurals_table.php
├── 016_create_structural_histories_table.php
├── 017_create_sessions_table.php
└── 018_create_password_reset_tokens_table.php
```

## 🏗️ Model Implementation

### Core Models

#### Employee Model

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Employee extends Model
{
    use SoftDeletes;

    protected $fillable = [
        'id',
        'id_sp',
        'nik',
        'nidn',
        'full_name',
        'birth_place',
        'birth_date',
        'gender',
        'title_prefix',
        'title_suffix',
        'field_of_study_id',
        'education_id',
        'ektp',
        'family_card_no',
        'ktp_address',
        'current_address',
        'religion_id',
        'employment_status_id',
        'phone_number',
        'email',
        'employment_type_id',
        'activity_id',
        'photo',
        'employment_date',
        'role',
        'main_duties',
        'lecturer_certification',
        'functional_position_id',
    ];

    protected $casts = [
        'birth_date' => 'date',
        'employment_date' => 'date',
    ];

    // Relationships
    public function religion(): BelongsTo
    {
        return $this->belongsTo(Religion::class);
    }

    public function education(): BelongsTo
    {
        return $this->belongsTo(Education::class);
    }

    public function fieldOfStudy(): BelongsTo
    {
        return $this->belongsTo(FieldOfStudy::class);
    }

    public function employmentStatus(): BelongsTo
    {
        return $this->belongsTo(EmploymentStatus::class);
    }

    public function employmentType(): BelongsTo
    {
        return $this->belongsTo(EmploymentType::class);
    }

    public function activity(): BelongsTo
    {
        return $this->belongsTo(Activity::class);
    }

    public function functionalPosition(): BelongsTo
    {
        return $this->belongsTo(Jabfung::class, 'functional_position_id');
    }

    public function histories(): HasMany
    {
        return $this->hasMany(EmployeeHistory::class, 'employee_nik', 'nik');
    }

    public function structurals(): HasMany
    {
        return $this->hasMany(Structural::class, 'employee_nik', 'nik');
    }
}
```

## 🌱 Seeder Implementation

### DatabaseSeeder

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        $this->call([
            // Reference data seeders
            ReligionSeeder::class,
            EducationSeeder::class,
            FieldOfStudySeeder::class,
            EmploymentStatusSeeder::class,
            EmploymentTypeSeeder::class,
            ActivitySeeder::class,
            JabfungSeeder::class,

            // Core data seeders
            UserSeeder::class,
            DepartmentSeeder::class,
            DivisionSeeder::class,
            PositionSeeder::class,
            StructuralPeriodSeeder::class,

            // Test data seeders
            EmployeeSeeder::class,
        ]);
    }
}
```

### ReligionSeeder

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class ReligionSeeder extends Seeder
{
    public function run(): void
    {
        $religions = [
            ['name' => 'Islam', 'is_active' => true],
            ['name' => 'Kristen', 'is_active' => true],
            ['name' => 'Katolik', 'is_active' => true],
            ['name' => 'Hindu', 'is_active' => true],
            ['name' => 'Buddha', 'is_active' => true],
            ['name' => 'Konghucu', 'is_active' => true],
        ];

        DB::table('religions')->insert($religions);
    }
}
```

## 🏭 Factory Implementation

### EmployeeFactory

```php
<?php

namespace Database\Factories;

use App\Models\Employee;
use App\Models\Religion;
use App\Models\Education;
use App\Models\FieldOfStudy;
use App\Models\EmploymentStatus;
use App\Models\EmploymentType;
use App\Models\Activity;
use App\Models\Jabfung;
use Illuminate\Database\Eloquent\Factories\Factory;

class EmployeeFactory extends Factory
{
    protected $model = Employee::class;

    public function definition(): array
    {
        return [
            'id' => $this->faker->unique()->regexify('[A-Z]{3}[0-9]{3}'),
            'id_sp' => $this->faker->unique()->regexify('SP[0-9]{6}'),
            'nik' => $this->faker->unique()->numerify('################'),
            'nidn' => $this->faker->optional()->numerify('##########'),
            'full_name' => $this->faker->name(),
            'birth_place' => $this->faker->city(),
            'birth_date' => $this->faker->date(),
            'gender' => $this->faker->randomElement(['L', 'P']),
            'title_prefix' => $this->faker->optional()->randomElement(['Dr.', 'Prof.', 'Ir.', 'M.T.']),
            'title_suffix' => $this->faker->optional()->randomElement(['S.Kom', 'S.T', 'S.Pd', 'M.Kom']),
            'field_of_study_id' => FieldOfStudy::factory(),
            'education_id' => Education::factory(),
            'ektp' => $this->faker->numerify('################'),
            'family_card_no' => $this->faker->numerify('############'),
            'ktp_address' => $this->faker->address(),
            'current_address' => $this->faker->address(),
            'religion_id' => Religion::factory(),
            'employment_status_id' => EmploymentStatus::factory(),
            'phone_number' => $this->faker->phoneNumber(),
            'email' => $this->faker->unique()->safeEmail(),
            'employment_type_id' => EmploymentType::factory(),
            'activity_id' => Activity::factory(),
            'photo' => $this->faker->optional()->imageUrl(),
            'employment_date' => $this->faker->date(),
            'role' => $this->faker->randomElement(['Dosen', 'Staff', 'Admin']),
            'main_duties' => $this->faker->text(),
            'lecturer_certification' => $this->faker->optional()->randomElement(['Sertifikat A', 'Sertifikat B']),
            'functional_position_id' => Jabfung::factory(),
        ];
    }
}
```

## 🔧 Database Commands

### Migration Commands

```bash
# Create migration
php artisan make:migration create_employees_table

# Run migrations
php artisan migrate

# Rollback migrations
php artisan migrate:rollback

# Fresh migration with seeders
php artisan migrate:fresh --seed
```

### Model Commands

```bash
# Create model with migration
php artisan make:model Employee -m

# Create model with migration, factory, and seeder
php artisan make:model Employee -mfs
```

### Seeder Commands

```bash
# Create seeder
php artisan make:seeder EmployeeSeeder

# Run specific seeder
php artisan db:seed --class=EmployeeSeeder

# Run all seeders
php artisan db:seed
```

## 📊 Database Optimization

### Indexes

```php
// In migration files
$table->index('nik');
$table->index('full_name');
$table->index('email');
$table->index(['field_of_study_id', 'education_id']);
```

### Foreign Key Constraints

```php
$table->foreignId('religion_id')
      ->constrained('religions')
      ->onDelete('set null');
```

### Soft Deletes

```php
$table->softDeletes(); // Adds deleted_at column
```

## 🧪 Testing Database

### Database Testing

```php
<?php

namespace Tests\Feature;

use App\Models\Employee;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class EmployeeTest extends TestCase
{
    use RefreshDatabase;

    public function test_can_create_employee(): void
    {
        $employee = Employee::factory()->create();

        $this->assertDatabaseHas('employees', [
            'id' => $employee->id,
            'nik' => $employee->nik,
        ]);
    }
}
```

## 📝 Implementation Checklist

### Phase 1: Reference Tables

- [ ] Create migration files (001-007)
- [ ] Implement models
- [ ] Create seeders
- [ ] Test migrations

### Phase 2: Core Tables

- [ ] Create migration files (008-012)
- [ ] Implement models with relationships
- [ ] Create seeders
- [ ] Test relationships

### Phase 3: Employee Tables

- [ ] Create migration files (013-014)
- [ ] Implement Employee model
- [ ] Create EmployeeHistory model
- [ ] Test employee operations

### Phase 4: Structural Tables

- [ ] Create migration files (015-016)
- [ ] Implement Structural models
- [ ] Test organizational structure

### Phase 5: Authentication Tables

- [ ] Create migration files (017-018)
- [ ] Implement authentication models
- [ ] Test authentication flow

---

**Status**: ⏳ Ready for Implementation  
**Priority**: High  
**Estimated Time**: 3-4 days  
**Dependencies**: Project Setup completed
