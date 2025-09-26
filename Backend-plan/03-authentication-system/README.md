# Authentication System - SIMPEG Backend

## 📋 Overview

Implementasi sistem autentikasi dan otorisasi untuk SIMPEG menggunakan Laravel Sanctum untuk API authentication.

## 🔐 Authentication Architecture

### Laravel Sanctum Implementation

- **Token Type**: Personal Access Token
- **Storage**: Database (personal_access_tokens table)
- **Expiration**: Configurable (default: no expiration)
- **Security**: Token hashing dan rate limiting

### User Roles & Permissions

- **Admin**: Full system access
- **HR Manager**: Employee management access
- **Employee**: Limited self-access
- **Public User**: Read-only public data

## 🏗️ Authentication Components

### 1. Laravel Sanctum Configuration

#### Sanctum Service Provider

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Laravel\Sanctum\Sanctum;

class AppServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        //
    }

    public function boot(): void
    {
        Sanctum::usePersonalAccessTokenModel(PersonalAccessToken::class);
    }
}
```

#### Sanctum Configuration

```php
// config/sanctum.php
return [
    'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', sprintf(
        '%s%s',
        'localhost,localhost:3000,127.0.0.1,127.0.0.1:8000,::1',
        Sanctum::currentApplicationUrlWithPort()
    ))),

    'guard' => ['web'],

    'expiration' => env('SANCTUM_TOKEN_EXPIRATION', null),

    'middleware' => [
        'verify_csrf_token' => App\Http\Middleware\VerifyCsrfToken::class,
        'encrypt_cookies' => App\Http\Middleware\EncryptCookies::class,
    ],
];
```

### 2. User Model Enhancement

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Laravel\Sanctum\HasApiTokens;
use Spatie\Permission\Traits\HasRoles;

class User extends Authenticatable
{
    use HasApiTokens, HasRoles;

    protected $fillable = [
        'name',
        'username',
        'phone',
        'prodi',
        'oauth_client_role_id',
        'email_verified_at',
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'password' => 'hashed',
    ];

    /**
     * Create a new personal access token
     */
    public function createToken(string $name, array $abilities = ['*']): NewAccessToken
    {
        $token = $this->tokens()->create([
            'name' => $name,
            'token' => hash('sha256', $plainTextToken = Str::random(40)),
            'abilities' => $abilities,
        ]);

        return new NewAccessToken($token, $token->getKey() . '|' . $plainTextToken);
    }

    /**
     * Get user permissions based on role
     */
    public function getPermissions(): array
    {
        if ($this->hasRole('admin')) {
            return ['*'];
        }

        if ($this->hasRole('hr_manager')) {
            return [
                'employee:read',
                'employee:write',
                'department:read',
                'department:write',
                'division:read',
                'division:write',
                'position:read',
                'position:write',
            ];
        }

        if ($this->hasRole('employee')) {
            return [
                'employee:read:own',
                'employee:write:own',
            ];
        }

        return ['public:read'];
    }
}
```

### 3. Authentication Controller

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Http\Requests\LoginRequest;
use App\Http\Requests\RegisterRequest;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;
use Laravel\Sanctum\PersonalAccessToken;

class AuthController extends Controller
{
    /**
     * User login
     */
    public function login(LoginRequest $request)
    {
        $credentials = $request->only('username', 'password');

        if (!Auth::attempt($credentials)) {
            return response()->json([
                'success' => false,
                'message' => 'Invalid credentials',
            ], 401);
        }

        $user = Auth::user();
        $token = $user->createToken('api-token');

        return response()->json([
            'success' => true,
            'data' => [
                'access_token' => $token->plainTextToken,
                'token_type' => 'Bearer',
                'user' => $user->load('roles'),
                'permissions' => $user->getPermissions(),
            ],
        ]);
    }

    /**
     * User registration
     */
    public function register(RegisterRequest $request)
    {
        $user = User::create([
            'name' => $request->name,
            'username' => $request->username,
            'phone' => $request->phone,
            'password' => Hash::make($request->password),
        ]);

        // Assign default role
        $user->assignRole('employee');

        $token = $user->createToken('api-token');

        return response()->json([
            'success' => true,
            'message' => 'User registered successfully',
            'data' => [
                'access_token' => $token->plainTextToken,
                'token_type' => 'Bearer',
                'user' => $user->load('roles'),
                'permissions' => $user->getPermissions(),
            ],
        ], 201);
    }

    /**
     * User logout
     */
    public function logout(Request $request)
    {
        $request->user()->currentAccessToken()->delete();

        return response()->json([
            'success' => true,
            'message' => 'Successfully logged out',
        ]);
    }

    /**
     * Revoke all tokens
     */
    public function logoutAll(Request $request)
    {
        $request->user()->tokens()->delete();

        return response()->json([
            'success' => true,
            'message' => 'All tokens revoked successfully',
        ]);
    }

    /**
     * Get authenticated user
     */
    public function me(Request $request)
    {
        $user = $request->user()->load('roles');

        return response()->json([
            'success' => true,
            'data' => [
                'user' => $user,
                'permissions' => $user->getPermissions(),
            ],
        ]);
    }

    /**
     * Get user tokens
     */
    public function tokens(Request $request)
    {
        $tokens = $request->user()->tokens;

        return response()->json([
            'success' => true,
            'data' => $tokens,
        ]);
    }
}
```

### 4. Request Validation

#### LoginRequest

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class LoginRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'username' => 'required|string',
            'password' => 'required|string|min:6',
        ];
    }

    public function messages(): array
    {
        return [
            'username.required' => 'Username is required',
            'password.required' => 'Password is required',
            'password.min' => 'Password must be at least 6 characters',
        ];
    }
}
```

#### RegisterRequest

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class RegisterRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'username' => 'required|string|unique:users,username',
            'phone' => 'required|string|unique:users,phone',
            'password' => 'required|string|min:6|confirmed',
        ];
    }
}
```

### 5. Authentication Middleware

#### Sanctum Middleware

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class EnsureTokenIsValid
{
    public function handle(Request $request, Closure $next)
    {
        if (!$request->bearerToken()) {
            return response()->json([
                'success' => false,
                'message' => 'Authorization token required',
            ], 401);
        }

        return $next($request);
    }
}
```

#### Role Middleware

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class RoleMiddleware
{
    public function handle(Request $request, Closure $next, string $role)
    {
        if (!$request->user()->hasRole($role)) {
            return response()->json([
                'success' => false,
                'message' => 'Access denied. Insufficient permissions.',
            ], 403);
        }

        return $next($request);
    }
}
```

#### Permission Middleware

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class PermissionMiddleware
{
    public function handle(Request $request, Closure $next, string $permission)
    {
        $user = $request->user();
        $permissions = $user->getPermissions();

        if (!in_array($permission, $permissions) && !in_array('*', $permissions)) {
            return response()->json([
                'success' => false,
                'message' => 'Access denied. Required permission: ' . $permission,
            ], 403);
        }

        return $next($request);
    }
}
```

### 6. API Routes

```php
// routes/api.php

use App\Http\Controllers\Api\AuthController;
use App\Http\Middleware\EnsureTokenIsValid;
use App\Http\Middleware\RoleMiddleware;
use App\Http\Middleware\PermissionMiddleware;

// Authentication routes
Route::prefix('auth')->group(function () {
    Route::post('login', [AuthController::class, 'login']);
    Route::post('register', [AuthController::class, 'register']);

    Route::middleware([EnsureTokenIsValid::class])->group(function () {
        Route::post('logout', [AuthController::class, 'logout']);
        Route::post('logout-all', [AuthController::class, 'logoutAll']);
        Route::get('me', [AuthController::class, 'me']);
        Route::get('tokens', [AuthController::class, 'tokens']);
    });
});

// Protected routes
Route::middleware([EnsureTokenIsValid::class])->group(function () {

    // Employee routes
    Route::middleware([PermissionMiddleware::class . ':employee:read'])->group(function () {
        Route::get('employees', [EmployeeController::class, 'index']);
        Route::get('employees/{employee}', [EmployeeController::class, 'show']);
    });

    Route::middleware([PermissionMiddleware::class . ':employee:write'])->group(function () {
        Route::post('employees', [EmployeeController::class, 'store']);
        Route::put('employees/{employee}', [EmployeeController::class, 'update']);
        Route::delete('employees/{employee}', [EmployeeController::class, 'destroy']);
    });

    // Admin only routes
    Route::middleware([RoleMiddleware::class . ':admin'])->group(function () {
        Route::apiResource('departments', DepartmentController::class);
        Route::apiResource('users', UserController::class);
    });

    // HR Manager routes
    Route::middleware([RoleMiddleware::class . ':hr_manager'])->group(function () {
        Route::apiResource('divisions', DivisionController::class);
        Route::apiResource('positions', PositionController::class);
    });
});
```

## 🔒 Security Features

### Password Security

```php
// Password hashing
$hashedPassword = Hash::make($password);

// Password verification
if (Hash::check($password, $hashedPassword)) {
    // Password is correct
}

// Password strength validation
'password' => 'required|string|min:8|regex:/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/'
```

### Rate Limiting

```php
// routes/api.php
Route::middleware(['throttle:5,1'])->group(function () {
    Route::post('auth/login', [AuthController::class, 'login']);
    Route::post('auth/register', [AuthController::class, 'register']);
});
```

### Token Management

```php
// Revoke specific token
$token = $user->tokens()->where('id', $tokenId)->first();
$token->delete();

// Revoke all tokens
$user->tokens()->delete();

// Create token with abilities
$token = $user->createToken('api-token', ['employee:read', 'employee:write']);
```

## 🧪 Testing Authentication

### Authentication Tests

```php
<?php

namespace Tests\Feature;

use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class AuthenticationTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_login(): void
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
                        'user',
                        'permissions',
                    ],
                ]);
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
    }

    public function test_user_can_logout(): void
    {
        $user = User::factory()->create();
        $token = $user->createToken('test-token');

        $response = $this->withHeaders([
            'Authorization' => 'Bearer ' . $token->plainTextToken,
        ])->postJson('/api/auth/logout');

        $response->assertStatus(200)
                ->assertJson([
                    'success' => true,
                    'message' => 'Successfully logged out',
                ]);

        $this->assertDatabaseMissing('personal_access_tokens', [
            'tokenable_id' => $user->id,
            'name' => 'test-token',
        ]);
    }
}
```

## 📝 Implementation Checklist

### Phase 1: Sanctum Setup

- [ ] Install Laravel Sanctum
- [ ] Configure Sanctum settings
- [ ] Update User model
- [ ] Create Sanctum migrations

### Phase 2: Authentication Controller

- [ ] Create AuthController
- [ ] Implement login method
- [ ] Implement logout method
- [ ] Implement register method

### Phase 3: Middleware & Security

- [ ] Create token validation middleware
- [ ] Create role middleware
- [ ] Create permission middleware
- [ ] Setup rate limiting

### Phase 4: API Routes

- [ ] Define authentication routes
- [ ] Setup protected routes
- [ ] Implement role-based access
- [ ] Implement permission-based access

### Phase 5: Testing

- [ ] Write authentication tests
- [ ] Test token flow
- [ ] Test role permissions
- [ ] Test security features

---

**Status**: ⏳ Ready for Implementation  
**Priority**: High  
**Estimated Time**: 2-3 days  
**Dependencies**: Database Implementation completed
