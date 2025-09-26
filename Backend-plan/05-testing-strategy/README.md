# Testing Strategy - SIMPEG Backend

## 📋 Overview

Strategi testing komprehensif untuk backend SIMPEG meliputi unit testing, integration testing, API testing, dan performance testing.

## 🧪 Testing Pyramid

### 1. Unit Tests (70%)

- Model tests
- Service tests
- Helper function tests
- Validation tests

### 2. Integration Tests (20%)

- Database integration
- API endpoint tests
- Authentication flow tests
- Business logic tests

### 3. End-to-End Tests (10%)

- Complete user workflows
- Cross-system integration
- Performance tests

## 📁 Testing Structure

```
tests/
├── Unit/
│   ├── Models/
│   │   ├── EmployeeTest.php
│   │   ├── DepartmentTest.php
│   │   └── UserTest.php
│   ├── Services/
│   │   ├── EmployeeServiceTest.php
│   │   └── AuthServiceTest.php
│   └── Helpers/
│       └── ValidationHelperTest.php
├── Feature/
│   ├── Auth/
│   │   ├── LoginTest.php
│   │   └── RegisterTest.php
│   ├── Api/
│   │   ├── EmployeeApiTest.php
│   │   ├── DepartmentApiTest.php
│   │   └── ReferenceApiTest.php
│   └── Database/
│       ├── EmployeeDatabaseTest.php
│       └── MigrationTest.php
├── Integration/
│   ├── AuthFlowTest.php
│   ├── EmployeeWorkflowTest.php
│   └── OrganizationalStructureTest.php
└── Browser/
    ├── EmployeeManagementTest.php
    └── UserAuthenticationTest.php
```

## 🔧 Testing Configuration

### PHPUnit Configuration

```xml
<!-- phpunit.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="./vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
        <testsuite name="Integration">
            <directory suffix="Test.php">./tests/Integration</directory>
        </testsuite>
    </testsuites>
    <source>
        <include>
            <directory suffix=".php">./app</directory>
        </include>
    </source>
    <php>
        <env name="APP_ENV" value="testing"/>
        <env name="BCRYPT_ROUNDS" value="4"/>
        <env name="CACHE_DRIVER" value="array"/>
        <env name="DB_CONNECTION" value="sqlite"/>
        <env name="DB_DATABASE" value=":memory:"/>
        <env name="MAIL_MAILER" value="array"/>
        <env name="QUEUE_CONNECTION" value="sync"/>
        <env name="SESSION_DRIVER" value="array"/>
        <env name="TELESCOPE_ENABLED" value="false"/>
    </php>
</phpunit>
```

### Test Environment Setup

```php
// tests/CreatesApplication.php
<?php

namespace Tests;

use Illuminate\Contracts\Console\Kernel;

trait CreatesApplication
{
    public function createApplication()
    {
        $app = require __DIR__.'/../bootstrap/app.php';

        $app->make(Kernel::class)->bootstrap();

        return $app;
    }
}
```

## 🧪 Unit Testing

### Employee Model Test

```php
<?php

namespace Tests\Unit\Models;

use App\Models\Employee;
use App\Models\Religion;
use App\Models\Education;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class EmployeeTest extends TestCase
{
    use RefreshDatabase;

    public function test_can_create_employee(): void
    {
        $employee = Employee::factory()->create([
            'nik' => '1234567890123456',
            'full_name' => 'John Doe',
            'email' => 'john@example.com',
        ]);

        $this->assertDatabaseHas('employees', [
            'nik' => '1234567890123456',
            'full_name' => 'John Doe',
            'email' => 'john@example.com',
        ]);
    }

    public function test_employee_belongs_to_religion(): void
    {
        $religion = Religion::factory()->create();
        $employee = Employee::factory()->create(['religion_id' => $religion->id]);

        $this->assertInstanceOf(Religion::class, $employee->religion);
        $this->assertEquals($religion->id, $employee->religion->id);
    }

    public function test_employee_belongs_to_education(): void
    {
        $education = Education::factory()->create();
        $employee = Employee::factory()->create(['education_id' => $education->id]);

        $this->assertInstanceOf(Education::class, $employee->education);
        $this->assertEquals($education->id, $employee->education->id);
    }

    public function test_employee_has_many_histories(): void
    {
        $employee = Employee::factory()->create();
        $employee->histories()->create([
            'field_name' => 'full_name',
            'old_value' => 'Old Name',
            'new_value' => 'New Name',
            'changed_by' => 'admin@example.com',
            'changed_at' => now(),
        ]);

        $this->assertCount(1, $employee->histories);
        $this->assertEquals('full_name', $employee->histories->first()->field_name);
    }

    public function test_employee_soft_deletes(): void
    {
        $employee = Employee::factory()->create();
        $employeeId = $employee->id;

        $employee->delete();

        $this->assertSoftDeleted('employees', ['id' => $employeeId]);
        $this->assertDatabaseHas('employees', ['id' => $employeeId]);
    }

    public function test_employee_full_name_accessor(): void
    {
        $employee = Employee::factory()->create([
            'title_prefix' => 'Dr.',
            'full_name' => 'John Doe',
            'title_suffix' => 'S.Kom',
        ]);

        $this->assertEquals('Dr. John Doe, S.Kom', $employee->getFullNameWithTitles());
    }

    public function test_employee_nik_validation(): void
    {
        $this->expectException(\Illuminate\Database\QueryException::class);

        Employee::factory()->create(['nik' => '123']); // Invalid NIK length
    }
}
```

### Employee Service Test

```php
<?php

namespace Tests\Unit\Services;

use App\Models\Employee;
use App\Services\EmployeeService;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class EmployeeServiceTest extends TestCase
{
    use RefreshDatabase;

    protected EmployeeService $employeeService;

    protected function setUp(): void
    {
        parent::setUp();
        $this->employeeService = new EmployeeService();
    }

    public function test_can_create_employee(): void
    {
        $employeeData = [
            'nik' => '1234567890123456',
            'full_name' => 'John Doe',
            'email' => 'john@example.com',
            'phone_number' => '081234567890',
        ];

        $employee = $this->employeeService->createEmployee($employeeData);

        $this->assertInstanceOf(Employee::class, $employee);
        $this->assertEquals('1234567890123456', $employee->nik);
        $this->assertEquals('John Doe', $employee->full_name);
    }

    public function test_can_update_employee(): void
    {
        $employee = Employee::factory()->create();
        $updateData = ['full_name' => 'Updated Name'];

        $updatedEmployee = $this->employeeService->updateEmployee($employee, $updateData);

        $this->assertEquals('Updated Name', $updatedEmployee->full_name);
    }

    public function test_can_search_employees(): void
    {
        Employee::factory()->create(['full_name' => 'John Doe']);
        Employee::factory()->create(['full_name' => 'Jane Smith']);
        Employee::factory()->create(['full_name' => 'John Smith']);

        $results = $this->employeeService->searchEmployees('John');

        $this->assertCount(2, $results);
        $this->assertTrue($results->contains('full_name', 'John Doe'));
        $this->assertTrue($results->contains('full_name', 'John Smith'));
    }

    public function test_can_get_employee_statistics(): void
    {
        Employee::factory()->count(10)->create();
        Employee::factory()->count(5)->create(['gender' => 'L']);
        Employee::factory()->count(3)->create(['gender' => 'P']);

        $stats = $this->employeeService->getEmployeeStatistics();

        $this->assertEquals(18, $stats['total_employees']);
        $this->assertEquals(8, $stats['male_employees']); // 5 + 3 default
        $this->assertEquals(10, $stats['female_employees']); // 3 + 7 default
    }
}
```

## 🔗 Feature Testing

### Authentication Flow Test

```php
<?php

namespace Tests\Feature\Auth;

use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class AuthenticationTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_login_with_valid_credentials(): void
    {
        $user = User::factory()->create([
            'username' => 'testuser',
            'password' => bcrypt('password123'),
        ]);

        $response = $this->postJson('/api/auth/login', [
            'username' => 'testuser',
            'password' => 'password123',
        ]);

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'success',
                    'data' => [
                        'access_token',
                        'token_type',
                        'expires_in',
                        'user',
                    ],
                ]);

        $this->assertAuthenticated('api');
    }

    public function test_user_cannot_login_with_invalid_credentials(): void
    {
        $response = $this->postJson('/api/auth/login', [
            'username' => 'nonexistent',
            'password' => 'wrongpassword',
        ]);

        $response->assertStatus(401)
                ->assertJson([
                    'success' => false,
                    'message' => 'Invalid credentials',
                ]);

        $this->assertGuest('api');
    }

    public function test_user_can_register(): void
    {
        $userData = [
            'name' => 'John Doe',
            'username' => 'johndoe',
            'phone' => '081234567890',
            'password' => 'password123',
            'password_confirmation' => 'password123',
        ];

        $response = $this->postJson('/api/auth/register', $userData);

        $response->assertStatus(201)
                ->assertJson([
                    'success' => true,
                    'message' => 'User registered successfully',
                ]);

        $this->assertDatabaseHas('users', [
            'username' => 'johndoe',
            'phone' => '081234567890',
        ]);
    }

    public function test_user_can_logout(): void
    {
        $user = User::factory()->create();
        $token = auth('api')->login($user);

        $response = $this->withHeaders([
            'Authorization' => 'Bearer ' . $token,
        ])->postJson('/api/auth/logout');

        $response->assertStatus(200)
                ->assertJson([
                    'success' => true,
                    'message' => 'Successfully logged out',
                ]);
    }

    public function test_user_can_refresh_token(): void
    {
        $user = User::factory()->create();
        $token = auth('api')->login($user);

        $response = $this->withHeaders([
            'Authorization' => 'Bearer ' . $token,
        ])->postJson('/api/auth/refresh');

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'success',
                    'data' => [
                        'access_token',
                        'token_type',
                        'expires_in',
                    ],
                ]);
    }
}
```

### Employee API Test

```php
<?php

namespace Tests\Feature\Api;

use App\Models\Employee;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class EmployeeApiTest extends TestCase
{
    use RefreshDatabase;

    protected $user;

    protected function setUp(): void
    {
        parent::setUp();

        $this->user = User::factory()->create();
        $this->actingAs($this->user, 'api');
    }

    public function test_can_get_employees_list(): void
    {
        Employee::factory()->count(5)->create();

        $response = $this->getJson('/api/v1/employees');

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'success',
                    'data' => [
                        '*' => [
                            'id',
                            'nik',
                            'full_name',
                            'email',
                            'religion',
                            'education',
                        ]
                    ],
                    'pagination',
                ]);
    }

    public function test_can_create_employee(): void
    {
        $employeeData = Employee::factory()->make()->toArray();

        $response = $this->postJson('/api/v1/employees', $employeeData);

        $response->assertStatus(201)
                ->assertJson([
                    'success' => true,
                    'message' => 'Employee created successfully',
                ]);

        $this->assertDatabaseHas('employees', [
            'nik' => $employeeData['nik'],
            'full_name' => $employeeData['full_name'],
        ]);
    }

    public function test_can_get_single_employee(): void
    {
        $employee = Employee::factory()->create();

        $response = $this->getJson("/api/v1/employees/{$employee->id}");

        $response->assertStatus(200)
                ->assertJson([
                    'success' => true,
                    'data' => [
                        'id' => $employee->id,
                        'nik' => $employee->nik,
                        'full_name' => $employee->full_name,
                    ],
                ]);
    }

    public function test_can_update_employee(): void
    {
        $employee = Employee::factory()->create();
        $updateData = ['full_name' => 'Updated Name'];

        $response = $this->putJson("/api/v1/employees/{$employee->id}", $updateData);

        $response->assertStatus(200)
                ->assertJson([
                    'success' => true,
                    'message' => 'Employee updated successfully',
                ]);

        $this->assertDatabaseHas('employees', [
            'id' => $employee->id,
            'full_name' => 'Updated Name',
        ]);
    }

    public function test_can_delete_employee(): void
    {
        $employee = Employee::factory()->create();

        $response = $this->deleteJson("/api/v1/employees/{$employee->id}");

        $response->assertStatus(200)
                ->assertJson([
                    'success' => true,
                    'message' => 'Employee deleted successfully',
                ]);

        $this->assertSoftDeleted('employees', ['id' => $employee->id]);
    }

    public function test_can_search_employees(): void
    {
        Employee::factory()->create(['full_name' => 'John Doe']);
        Employee::factory()->create(['full_name' => 'Jane Smith']);

        $response = $this->getJson('/api/v1/employees?search=John');

        $response->assertStatus(200);

        $data = $response->json('data');
        $this->assertCount(1, $data);
        $this->assertEquals('John Doe', $data[0]['full_name']);
    }

    public function test_can_filter_employees_by_department(): void
    {
        // Implementation depends on structural setup
        $response = $this->getJson('/api/v1/employees?department_id=1');

        $response->assertStatus(200);
    }
}
```

## 🔄 Integration Testing

### Employee Workflow Test

```php
<?php

namespace Tests\Integration;

use App\Models\Employee;
use App\Models\Department;
use App\Models\Position;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class EmployeeWorkflowTest extends TestCase
{
    use RefreshDatabase;

    public function test_complete_employee_lifecycle(): void
    {
        // 1. Create employee
        $employee = Employee::factory()->create();

        // 2. Assign to department and position
        $department = Department::factory()->create();
        $position = Position::factory()->create();

        $employee->structurals()->create([
            'department_id' => $department->id,
            'position_id' => $position->id,
            'structural_period_id' => 1, // Assuming period exists
        ]);

        // 3. Update employee data
        $employee->update(['full_name' => 'Updated Name']);

        // 4. Verify history is recorded
        $this->assertDatabaseHas('employee_histories', [
            'employee_nik' => $employee->nik,
            'field_name' => 'full_name',
            'new_value' => 'Updated Name',
        ]);

        // 5. Soft delete employee
        $employee->delete();

        // 6. Verify soft delete
        $this->assertSoftDeleted('employees', ['id' => $employee->id]);
    }

    public function test_organizational_structure_integration(): void
    {
        $department = Department::factory()->create();
        $division = $department->divisions()->create([
            'name' => 'IT Division',
        ]);

        $position = Position::factory()->create();
        $employee = Employee::factory()->create();

        // Create structural assignment
        $structural = $employee->structurals()->create([
            'department_id' => $department->id,
            'division_id' => $division->id,
            'position_id' => $position->id,
            'structural_period_id' => 1,
        ]);

        // Verify relationships
        $this->assertEquals($department->id, $structural->department->id);
        $this->assertEquals($division->id, $structural->division->id);
        $this->assertEquals($position->id, $structural->position->id);
        $this->assertEquals($employee->nik, $structural->employee->nik);
    }
}
```

## 📊 Performance Testing

### Database Performance Test

```php
<?php

namespace Tests\Performance;

use App\Models\Employee;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class DatabasePerformanceTest extends TestCase
{
    use RefreshDatabase;

    public function test_employee_query_performance(): void
    {
        // Create test data
        Employee::factory()->count(1000)->create();

        $startTime = microtime(true);

        // Test query performance
        $employees = Employee::with([
            'religion',
            'education',
            'fieldOfStudy',
            'employmentStatus',
            'employmentType',
            'activity',
            'functionalPosition'
        ])->paginate(50);

        $endTime = microtime(true);
        $executionTime = $endTime - $startTime;

        // Assert query executes within acceptable time (e.g., 500ms)
        $this->assertLessThan(0.5, $executionTime);

        // Assert we get expected results
        $this->assertCount(50, $employees->items());
    }

    public function test_search_performance(): void
    {
        Employee::factory()->count(1000)->create();

        $startTime = microtime(true);

        $results = Employee::where('full_name', 'like', '%John%')
                          ->orWhere('email', 'like', '%john%')
                          ->get();

        $endTime = microtime(true);
        $executionTime = $endTime - $startTime;

        $this->assertLessThan(0.2, $executionTime);
    }
}
```

## 🛠️ Testing Tools & Commands

### Running Tests

```bash
# Run all tests
php artisan test

# Run specific test suite
php artisan test --testsuite=Unit
php artisan test --testsuite=Feature
php artisan test --testsuite=Integration

# Run specific test file
php artisan test tests/Unit/Models/EmployeeTest.php

# Run with coverage
php artisan test --coverage

# Run tests in parallel
php artisan test --parallel
```

### Test Data Management

```bash
# Refresh database for each test
php artisan test --recreate-databases

# Run tests without database refresh
php artisan test --without-databases
```

### Code Coverage

```bash
# Generate coverage report
php artisan test --coverage --min=80

# Generate HTML coverage report
php artisan test --coverage-html coverage/
```

## 📝 Testing Checklist

### Unit Testing

- [ ] Model relationships
- [ ] Model attributes and accessors
- [ ] Service layer methods
- [ ] Validation rules
- [ ] Helper functions

### Feature Testing

- [ ] Authentication flow
- [ ] API endpoints
- [ ] Database operations
- [ ] File uploads
- [ ] Email sending

### Integration Testing

- [ ] Complete workflows
- [ ] Cross-model relationships
- [ ] External API integrations
- [ ] Queue processing

### Performance Testing

- [ ] Database query performance
- [ ] API response times
- [ ] Memory usage
- [ ] Concurrent user handling

---

**Status**: ⏳ Ready for Implementation  
**Priority**: Medium  
**Estimated Time**: 3-4 days  
**Dependencies**: API Development completed
