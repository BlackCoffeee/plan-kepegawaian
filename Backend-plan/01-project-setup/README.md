# Project Setup - SIMPEG Backend

## 📋 Overview

Direktori ini berisi planning dan dokumentasi untuk setup awal project backend SIMPEG.

## 🎯 Setup Checklist

### 1. Laravel Project Initialization

- [ ] Create Laravel 10 project
- [ ] Configure composer dependencies
- [ ] Setup basic project structure
- [ ] Configure environment variables

### 2. Development Environment

- [ ] PHP 8.1+ installation
- [ ] MySQL 8.0+ setup
- [ ] Redis installation
- [ ] Node.js dan NPM setup

### 3. Dependencies & Packages

- [ ] Laravel Sanctum (Authentication)
- [ ] Spatie Laravel Permission (RBAC)
- [ ] Laravel Excel (Reports)
- [ ] Laravel Telescope (Debugging)

### 4. Development Tools

- [ ] IDE/Editor configuration
- [ ] PHP CS Fixer setup
- [ ] Laravel Pint (Code formatting)
- [ ] PHPStan (Static analysis)

## 📁 Project Structure

```
simpeg-backend/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Api/
│   │   │   │   ├── AuthController.php
│   │   │   │   ├── EmployeeController.php
│   │   │   │   ├── DepartmentController.php
│   │   │   │   └── ...
│   │   │   └── ...
│   │   ├── Middleware/
│   │   ├── Requests/
│   │   └── Resources/
│   ├── Models/
│   │   ├── Employee.php
│   │   ├── Department.php
│   │   ├── User.php
│   │   └── ...
│   ├── Services/
│   ├── Repositories/
│   └── ...
├── config/
├── database/
│   ├── migrations/
│   ├── seeders/
│   └── factories/
├── routes/
│   ├── api.php
│   ├── web.php
│   └── ...
├── tests/
├── storage/
└── ...
```

## 🔧 Configuration Files

### Environment Variables (.env)

```env
APP_NAME="SIMPEG UNISM"
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=simpeg_unism
DB_USERNAME=root
DB_PASSWORD=

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

### Composer Dependencies

```json
{
  "require": {
    "laravel/framework": "^12.0",
    "laravel/sanctum": "^3.0",
    "spatie/laravel-permission": "^5.0",
    "maatwebsite/excel": "^3.1"
  },
  "require-dev": {
    "laravel/telescope": "^4.0",
    "laravel/pint": "^1.0",
    "phpunit/phpunit": "^10.0"
  }
}
```

## 🚀 Setup Commands

### 1. Laravel Project Creation

```bash
# Create new Laravel project
composer create-project laravel/laravel simpeg-backend

# Navigate to project
cd simpeg-backend

# Install dependencies
composer install
```

### 2. Environment Setup

```bash
# Copy environment file
cp .env.example .env

# Generate application key
php artisan key:generate
```

### 3. Database Setup

```bash
# Create database
mysql -u root -p
CREATE DATABASE simpeg_unism;

# Run migrations
php artisan migrate

# Seed database
php artisan db:seed
```

### 4. Development Tools

```bash
# Install Laravel Telescope
composer require laravel/telescope --dev
php artisan telescope:install
php artisan migrate

# Install Laravel Pint
composer require laravel/pint --dev
```

## 📝 Next Steps

1. **Database Implementation**: Setup migrations dan models
2. **Authentication System**: Implement Laravel Sanctum authentication
3. **API Development**: Create API endpoints
4. **Testing**: Setup testing environment

---

**Status**: 🔄 Ready for Implementation  
**Priority**: High  
**Estimated Time**: 1-2 days
