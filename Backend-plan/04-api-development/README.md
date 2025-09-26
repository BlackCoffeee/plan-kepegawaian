# API Development - SIMPEG Backend

## 📋 Overview

Implementasi API endpoints berdasarkan spesifikasi yang telah dibuat di General Plan. Meliputi controllers, requests, resources, dan API documentation.

## 🌐 API Architecture

### RESTful API Design

- **Base URL**: `https://api.simpeg.unism.ac.id/v1`
- **Authentication**: JWT Bearer Token
- **Response Format**: JSON
- **HTTP Methods**: GET, POST, PUT, DELETE
- **Status Codes**: Standard HTTP status codes

### API Versioning

- **Current Version**: v1
- **Versioning Strategy**: URL path versioning
- **Backward Compatibility**: Maintained for 6 months

## 📁 API Structure

### Controllers Organization

```
app/Http/Controllers/Api/
├── AuthController.php
├── EmployeeController.php
├── DepartmentController.php
├── DivisionController.php
├── PositionController.php
├── StructuralController.php
├── ReferenceController.php
├── ReportController.php
└── UserController.php
```

### Request Validation

```
app/Http/Requests/
├── Auth/
│   ├── LoginRequest.php
│   └── RegisterRequest.php
├── Employee/
│   ├── StoreEmployeeRequest.php
│   └── UpdateEmployeeRequest.php
└── Department/
    ├── StoreDepartmentRequest.php
    └── UpdateDepartmentRequest.php
```

### API Resources

```
app/Http/Resources/
├── EmployeeResource.php
├── DepartmentResource.php
├── UserResource.php
└── PaginatedResource.php
```

## 🎯 Core API Endpoints

### 1. Employee Management API

#### EmployeeController

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Http\Requests\StoreEmployeeRequest;
use App\Http\Requests\UpdateEmployeeRequest;
use App\Http\Resources\EmployeeResource;
use App\Models\Employee;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;

class EmployeeController extends Controller
{
    /**
     * Display a listing of employees
     */
    public function index(Request $request): JsonResponse
    {
        $query = Employee::with([
            'religion',
            'education',
            'fieldOfStudy',
            'employmentStatus',
            'employmentType',
            'activity',
            'functionalPosition'
        ]);

        // Search functionality
        if ($request->has('search')) {
            $search = $request->get('search');
            $query->where(function ($q) use ($search) {
                $q->where('full_name', 'like', "%{$search}%")
                  ->orWhere('nik', 'like', "%{$search}%")
                  ->orWhere('email', 'like', "%{$search}%");
            });
        }

        // Filter by department
        if ($request->has('department_id')) {
            $query->whereHas('structurals', function ($q) use ($request) {
                $q->where('department_id', $request->get('department_id'));
            });
        }

        // Filter by employment status
        if ($request->has('employment_status_id')) {
            $query->where('employment_status_id', $request->get('employment_status_id'));
        }

        // Sorting
        $sortField = $request->get('sort', 'full_name');
        $sortDirection = $request->get('direction', 'asc');
        $query->orderBy($sortField, $sortDirection);

        // Pagination
        $employees = $query->paginate($request->get('per_page', 10));

        return response()->json([
            'success' => true,
            'data' => EmployeeResource::collection($employees->items()),
            'pagination' => [
                'current_page' => $employees->currentPage(),
                'per_page' => $employees->perPage(),
                'total' => $employees->total(),
                'last_page' => $employees->lastPage(),
                'from' => $employees->firstItem(),
                'to' => $employees->lastItem(),
            ],
        ]);
    }

    /**
     * Store a newly created employee
     */
    public function store(StoreEmployeeRequest $request): JsonResponse
    {
        $employee = Employee::create($request->validated());

        return response()->json([
            'success' => true,
            'message' => 'Employee created successfully',
            'data' => new EmployeeResource($employee->load([
                'religion',
                'education',
                'fieldOfStudy',
                'employmentStatus',
                'employmentType',
                'activity',
                'functionalPosition'
            ])),
        ], 201);
    }

    /**
     * Display the specified employee
     */
    public function show(Employee $employee): JsonResponse
    {
        $employee->load([
            'religion',
            'education',
            'fieldOfStudy',
            'employmentStatus',
            'employmentType',
            'activity',
            'functionalPosition',
            'histories',
            'structurals.department',
            'structurals.division',
            'structurals.position'
        ]);

        return response()->json([
            'success' => true,
            'data' => new EmployeeResource($employee),
        ]);
    }

    /**
     * Update the specified employee
     */
    public function update(UpdateEmployeeRequest $request, Employee $employee): JsonResponse
    {
        $employee->update($request->validated());

        return response()->json([
            'success' => true,
            'message' => 'Employee updated successfully',
            'data' => new EmployeeResource($employee->load([
                'religion',
                'education',
                'fieldOfStudy',
                'employmentStatus',
                'employmentType',
                'activity',
                'functionalPosition'
            ])),
        ]);
    }

    /**
     * Remove the specified employee (soft delete)
     */
    public function destroy(Employee $employee): JsonResponse
    {
        $employee->delete();

        return response()->json([
            'success' => true,
            'message' => 'Employee deleted successfully',
        ]);
    }

    /**
     * Get employee history
     */
    public function history(Employee $employee): JsonResponse
    {
        $histories = $employee->histories()
            ->orderBy('changed_at', 'desc')
            ->paginate(20);

        return response()->json([
            'success' => true,
            'data' => $histories->items(),
            'pagination' => [
                'current_page' => $histories->currentPage(),
                'per_page' => $histories->perPage(),
                'total' => $histories->total(),
                'last_page' => $histories->lastPage(),
            ],
        ]);
    }
}
```

### 2. Request Validation

#### StoreEmployeeRequest

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreEmployeeRequest extends FormRequest
{
    public function authorize(): bool
    {
        return auth()->user()->getRole() !== 'employee';
    }

    public function rules(): array
    {
        return [
            'id_sp' => 'required|string|unique:employees,id_sp',
            'nik' => 'required|string|size:16|unique:employees,nik',
            'nidn' => 'nullable|string|unique:employees,nidn',
            'full_name' => 'required|string|max:255',
            'birth_place' => 'nullable|string|max:255',
            'birth_date' => 'nullable|date|before:today',
            'gender' => 'nullable|in:L,P',
            'title_prefix' => 'nullable|string|max:50',
            'title_suffix' => 'nullable|string|max:50',
            'field_of_study_id' => 'nullable|exists:fields_of_study,id',
            'education_id' => 'nullable|exists:educations,id',
            'ektp' => 'nullable|string|size:16',
            'family_card_no' => 'nullable|string|max:20',
            'ktp_address' => 'nullable|string',
            'current_address' => 'nullable|string',
            'religion_id' => 'nullable|exists:religions,id',
            'employment_status_id' => 'nullable|exists:employment_status,id',
            'phone_number' => 'nullable|string|max:20',
            'email' => 'nullable|email|unique:employees,email',
            'employment_type_id' => 'nullable|exists:employment_types,id',
            'activity_id' => 'nullable|exists:activities,id',
            'photo' => 'nullable|image|mimes:jpeg,png,jpg|max:2048',
            'employment_date' => 'nullable|date',
            'role' => 'nullable|string|max:100',
            'main_duties' => 'nullable|string',
            'lecturer_certification' => 'nullable|string|max:100',
            'functional_position_id' => 'nullable|exists:jabfungs,id',
        ];
    }

    public function messages(): array
    {
        return [
            'nik.required' => 'NIK is required',
            'nik.size' => 'NIK must be exactly 16 digits',
            'nik.unique' => 'NIK already exists',
            'email.email' => 'Email must be a valid email address',
            'email.unique' => 'Email already exists',
            'birth_date.before' => 'Birth date must be before today',
            'photo.image' => 'Photo must be an image file',
            'photo.max' => 'Photo size must not exceed 2MB',
        ];
    }
}
```

### 3. API Resources

#### EmployeeResource

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class EmployeeResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'id_sp' => $this->id_sp,
            'nik' => $this->nik,
            'nidn' => $this->nidn,
            'full_name' => $this->full_name,
            'birth_place' => $this->birth_place,
            'birth_date' => $this->birth_date?->format('Y-m-d'),
            'gender' => $this->gender,
            'title_prefix' => $this->title_prefix,
            'title_suffix' => $this->title_suffix,
            'email' => $this->email,
            'phone_number' => $this->phone_number,
            'photo' => $this->photo ? asset('storage/' . $this->photo) : null,
            'employment_date' => $this->employment_date?->format('Y-m-d'),
            'role' => $this->role,
            'main_duties' => $this->main_duties,
            'lecturer_certification' => $this->lecturer_certification,
            'created_at' => $this->created_at?->format('Y-m-d H:i:s'),
            'updated_at' => $this->updated_at?->format('Y-m-d H:i:s'),

            // Relationships
            'religion' => $this->whenLoaded('religion', function () {
                return [
                    'id' => $this->religion->id,
                    'name' => $this->religion->name,
                ];
            }),

            'education' => $this->whenLoaded('education', function () {
                return [
                    'id' => $this->education->id,
                    'level' => $this->education->level,
                    'name' => $this->education->name,
                ];
            }),

            'employment_status' => $this->whenLoaded('employmentStatus', function () {
                return [
                    'id' => $this->employmentStatus->id,
                    'name' => $this->employmentStatus->name,
                ];
            }),

            'current_structure' => $this->whenLoaded('structurals', function () {
                $currentStructure = $this->structurals
                    ->where('structural_period.is_active', true)
                    ->first();

                return $currentStructure ? [
                    'department' => [
                        'id' => $currentStructure->department->id,
                        'name' => $currentStructure->department->name,
                    ],
                    'division' => $currentStructure->division ? [
                        'id' => $currentStructure->division->id,
                        'name' => $currentStructure->division->name,
                    ] : null,
                    'position' => [
                        'id' => $currentStructure->position->id,
                        'name' => $currentStructure->position->name,
                    ],
                ] : null;
            }),
        ];
    }
}
```

### 4. Reference Data API

#### ReferenceController

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Religion;
use App\Models\Education;
use App\Models\FieldOfStudy;
use App\Models\EmploymentStatus;
use App\Models\EmploymentType;
use App\Models\Activity;
use App\Models\Jabfung;
use Illuminate\Http\JsonResponse;

class ReferenceController extends Controller
{
    /**
     * Get all religions
     */
    public function religions(): JsonResponse
    {
        $religions = Religion::where('is_active', true)->get();

        return response()->json([
            'success' => true,
            'data' => $religions,
        ]);
    }

    /**
     * Get all education levels
     */
    public function educations(): JsonResponse
    {
        $educations = Education::where('is_active', true)->get();

        return response()->json([
            'success' => true,
            'data' => $educations,
        ]);
    }

    /**
     * Get all fields of study
     */
    public function fieldsOfStudy(): JsonResponse
    {
        $fieldsOfStudy = FieldOfStudy::where('is_active', true)->get();

        return response()->json([
            'success' => true,
            'data' => $fieldsOfStudy,
        ]);
    }

    /**
     * Get all employment statuses
     */
    public function employmentStatuses(): JsonResponse
    {
        $statuses = EmploymentStatus::where('is_active', true)->get();

        return response()->json([
            'success' => true,
            'data' => $statuses,
        ]);
    }

    /**
     * Get all employment types
     */
    public function employmentTypes(): JsonResponse
    {
        $types = EmploymentType::where('is_active', true)->get();

        return response()->json([
            'success' => true,
            'data' => $types,
        ]);
    }

    /**
     * Get all activities
     */
    public function activities(): JsonResponse
    {
        $activities = Activity::where('is_active', true)->get();

        return response()->json([
            'success' => true,
            'data' => $activities,
        ]);
    }

    /**
     * Get all functional positions
     */
    public function jabfungs(): JsonResponse
    {
        $jabfungs = Jabfung::all();

        return response()->json([
            'success' => true,
            'data' => $jabfungs,
        ]);
    }
}
```

## 📊 API Documentation

### OpenAPI/Swagger Setup

```bash
composer require darkaonline/l5-swagger
php artisan vendor:publish --provider="L5Swagger\L5SwaggerServiceProvider"
```

### API Documentation Controller

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

/**
 * @OA\Info(
 *     title="SIMPEG UNISM API",
 *     version="1.0.0",
 *     description="API documentation for SIMPEG (Sistem Informasi Manajemen Pegawai) Universitas Islam Negeri Sultan Maulana Hasanuddin"
 * )
 *
 * @OA\SecurityScheme(
 *     securityScheme="bearerAuth",
 *     type="http",
 *     scheme="bearer",
 *     bearerFormat="JWT"
 * )
 */
class ApiDocumentationController extends Controller
{
    /**
     * @OA\Get(
     *     path="/api/v1/employees",
     *     summary="Get list of employees",
     *     tags={"Employees"},
     *     security={{"bearerAuth":{}}},
     *     @OA\Parameter(
     *         name="page",
     *         in="query",
     *         description="Page number",
     *         @OA\Schema(type="integer")
     *     ),
     *     @OA\Parameter(
     *         name="per_page",
     *         in="query",
     *         description="Items per page",
     *         @OA\Schema(type="integer")
     *     ),
     *     @OA\Parameter(
     *         name="search",
     *         in="query",
     *         description="Search term",
     *         @OA\Schema(type="string")
     *     ),
     *     @OA\Response(
     *         response=200,
     *         description="Successful operation",
     *         @OA\JsonContent(
     *             @OA\Property(property="success", type="boolean"),
     *             @OA\Property(property="data", type="array", @OA\Items(ref="#/components/schemas/Employee")),
     *             @OA\Property(property="pagination", type="object")
     *         )
     *     )
     * )
     */
    public function index()
    {
        // Implementation
    }
}
```

## 🧪 API Testing

### API Test Example

```php
<?php

namespace Tests\Feature;

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
}
```

## 📝 Implementation Checklist

### Phase 1: Core Controllers

- [ ] Create EmployeeController
- [ ] Create DepartmentController
- [ ] Create DivisionController
- [ ] Create PositionController

### Phase 2: Request Validation

- [ ] Create StoreEmployeeRequest
- [ ] Create UpdateEmployeeRequest
- [ ] Create other request classes
- [ ] Test validation rules

### Phase 3: API Resources

- [ ] Create EmployeeResource
- [ ] Create DepartmentResource
- [ ] Create other resource classes
- [ ] Format API responses

### Phase 4: Reference APIs

- [ ] Create ReferenceController
- [ ] Implement reference endpoints
- [ ] Cache reference data

### Phase 5: Documentation & Testing

- [ ] Setup Swagger/OpenAPI
- [ ] Write API documentation
- [ ] Create API tests
- [ ] Test all endpoints

---

**Status**: ⏳ Ready for Implementation  
**Priority**: High  
**Estimated Time**: 4-5 days  
**Dependencies**: Authentication System completed
