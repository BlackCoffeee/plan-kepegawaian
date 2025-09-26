# Deployment Plan - SIMPEG Backend

## 📋 Overview

Rencana deployment untuk backend SIMPEG meliputi environment setup, production configuration, monitoring, dan maintenance procedures.

## 🚀 Deployment Environments

### 1. Development Environment

- **Purpose**: Local development dan testing
- **Server**: Local machine
- **Database**: Local MySQL/PostgreSQL
- **Cache**: Local Redis
- **Domain**: `localhost:8000`

### 2. Staging Environment

- **Purpose**: Pre-production testing
- **Server**: Cloud server (DigitalOcean/AWS)
- **Database**: Cloud database
- **Cache**: Cloud Redis
- **Domain**: `staging-api.simpeg.unism.ac.id`

### 3. Production Environment

- **Purpose**: Live application
- **Server**: High-availability cloud setup
- **Database**: Managed database cluster
- **Cache**: Managed Redis cluster
- **Domain**: `api.simpeg.unism.ac.id`

## 🏗️ Production Architecture

### Server Configuration

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Load Balancer │    │   Load Balancer │    │   Load Balancer │
│     (Nginx)     │    │     (Nginx)     │    │     (Nginx)     │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          ▼                      ▼                      ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   App Server 1  │    │   App Server 2  │    │   App Server 3  │
│   (Laravel)     │    │   (Laravel)     │    │   (Laravel)     │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          └──────────────────────┼──────────────────────┘
                                 ▼
                    ┌─────────────────┐
                    │  Database       │
                    │  (MySQL/PostgreSQL) │
                    └─────────────────┘
```

### Infrastructure Components

#### 1. Web Servers

- **Nginx**: Reverse proxy dan load balancer
- **PHP-FPM**: PHP process manager
- **Laravel**: Application framework
- **SSL/TLS**: HTTPS encryption

#### 2. Database

- **MySQL 8.0+**: Primary database
- **Redis**: Caching dan session storage
- **Backup**: Automated daily backups

#### 3. Storage

- **File Storage**: User uploads (photos, documents)
- **CDN**: Static asset delivery
- **Backup Storage**: Database dan file backups

## 🔧 Environment Configuration

### Production Environment Variables

```env
# Application
APP_NAME="SIMPEG UNISM"
APP_ENV=production
APP_DEBUG=false
APP_URL=https://api.simpeg.unism.ac.id
APP_KEY=base64:your-app-key-here

# Database
DB_CONNECTION=mysql
DB_HOST=your-db-host
DB_PORT=3306
DB_DATABASE=simpeg_production
DB_USERNAME=simpeg_user
DB_PASSWORD=secure-password

# Cache
REDIS_HOST=your-redis-host
REDIS_PASSWORD=redis-password
REDIS_PORT=6379

# Mail
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your-email@unism.ac.id
MAIL_PASSWORD=your-app-password
MAIL_ENCRYPTION=tls

# JWT
JWT_SECRET=your-jwt-secret
JWT_TTL=60
JWT_REFRESH_TTL=20160

# Queue
QUEUE_CONNECTION=redis

# Logging
LOG_CHANNEL=stack
LOG_LEVEL=error

# Telescope (disable in production)
TELESCOPE_ENABLED=false
```

### Nginx Configuration

```nginx
# /etc/nginx/sites-available/simpeg-api
server {
    listen 80;
    server_name api.simpeg.unism.ac.id;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name api.simpeg.unism.ac.id;

    root /var/www/simpeg-backend/public;
    index index.php;

    # SSL Configuration
    ssl_certificate /etc/ssl/certs/simpeg.crt;
    ssl_certificate_key /etc/ssl/private/simpeg.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;

    # Security Headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;

    # Rate Limiting
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
    limit_req zone=api burst=20 nodelay;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_hide_header X-Powered-By;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }

    # API specific configuration
    location /api/ {
        limit_req zone=api burst=20 nodelay;
        try_files $uri $uri/ /index.php?$query_string;
    }

    # Static files caching
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

## 🚀 Deployment Process

### 1. Pre-Deployment Checklist

```bash
# 1. Code Quality Check
composer install --no-dev --optimize-autoloader
php artisan config:cache
php artisan route:cache
php artisan view:cache

# 2. Database Migration
php artisan migrate --force

# 3. Asset Compilation
npm run production

# 4. Permission Setup
chmod -R 755 storage bootstrap/cache
chown -R www-data:www-data storage bootstrap/cache

# 5. Queue Worker Setup
php artisan queue:restart
```

### 2. Deployment Script

```bash
#!/bin/bash
# deploy.sh

set -e

echo "Starting deployment..."

# 1. Pull latest code
git pull origin main

# 2. Install dependencies
composer install --no-dev --optimize-autoloader

# 3. Run database migrations
php artisan migrate --force

# 4. Clear and cache configuration
php artisan config:cache
php artisan route:cache
php artisan view:cache

# 5. Restart queue workers
php artisan queue:restart

# 6. Restart PHP-FPM
sudo systemctl restart php8.1-fpm

# 7. Reload Nginx
sudo systemctl reload nginx

echo "Deployment completed successfully!"
```

### 3. Zero-Downtime Deployment

```bash
#!/bin/bash
# zero-downtime-deploy.sh

set -e

DEPLOY_DIR="/var/www/simpeg-backend"
BACKUP_DIR="/var/backups/simpeg"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

echo "Starting zero-downtime deployment..."

# 1. Create backup
echo "Creating backup..."
tar -czf "$BACKUP_DIR/backup_$TIMESTAMP.tar.gz" -C $DEPLOY_DIR .

# 2. Clone new version
echo "Cloning new version..."
git clone --depth 1 https://github.com/your-repo/simpeg-backend.git /tmp/simpeg-new

# 3. Install dependencies
echo "Installing dependencies..."
cd /tmp/simpeg-new
composer install --no-dev --optimize-autoloader

# 4. Run migrations
echo "Running migrations..."
php artisan migrate --force

# 5. Cache optimization
echo "Optimizing cache..."
php artisan config:cache
php artisan route:cache
php artisan view:cache

# 6. Atomic deployment
echo "Performing atomic deployment..."
mv $DEPLOY_DIR $DEPLOY_DIR.old
mv /tmp/simpeg-new $DEPLOY_DIR

# 7. Restart services
echo "Restarting services..."
php artisan queue:restart
sudo systemctl restart php8.1-fpm

# 8. Cleanup
echo "Cleaning up..."
rm -rf $DEPLOY_DIR.old
rm -rf /tmp/simpeg-new

echo "Zero-downtime deployment completed!"
```

## 📊 Monitoring & Logging

### Application Monitoring

```php
// config/logging.php
return [
    'default' => env('LOG_CHANNEL', 'stack'),
    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['single', 'slack'],
            'ignore_exceptions' => false,
        ],
        'single' => [
            'driver' => 'single',
            'path' => storage_path('logs/laravel.log'),
            'level' => env('LOG_LEVEL', 'error'),
        ],
        'slack' => [
            'driver' => 'slack',
            'url' => env('LOG_SLACK_WEBHOOK_URL'),
            'username' => 'SIMPEG Bot',
            'emoji' => ':boom:',
            'level' => env('LOG_LEVEL', 'error'),
        ],
    ],
];
```

### Health Check Endpoint

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Redis;

class HealthController extends Controller
{
    public function check()
    {
        $checks = [
            'database' => $this->checkDatabase(),
            'redis' => $this->checkRedis(),
            'storage' => $this->checkStorage(),
        ];

        $allHealthy = collect($checks)->every(fn($check) => $check['status'] === 'healthy');

        return response()->json([
            'status' => $allHealthy ? 'healthy' : 'unhealthy',
            'checks' => $checks,
            'timestamp' => now()->toISOString(),
        ], $allHealthy ? 200 : 503);
    }

    private function checkDatabase()
    {
        try {
            DB::connection()->getPdo();
            return ['status' => 'healthy', 'message' => 'Database connection successful'];
        } catch (\Exception $e) {
            return ['status' => 'unhealthy', 'message' => $e->getMessage()];
        }
    }

    private function checkRedis()
    {
        try {
            Redis::ping();
            return ['status' => 'healthy', 'message' => 'Redis connection successful'];
        } catch (\Exception $e) {
            return ['status' => 'unhealthy', 'message' => $e->getMessage()];
        }
    }

    private function checkStorage()
    {
        try {
            $testFile = storage_path('app/health-check.txt');
            file_put_contents($testFile, 'test');
            unlink($testFile);
            return ['status' => 'healthy', 'message' => 'Storage is writable'];
        } catch (\Exception $e) {
            return ['status' => 'unhealthy', 'message' => $e->getMessage()];
        }
    }
}
```

### Performance Monitoring

```php
// app/Http/Middleware/PerformanceMiddleware.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class PerformanceMiddleware
{
    public function handle(Request $request, Closure $next)
    {
        $startTime = microtime(true);
        $startMemory = memory_get_usage();

        $response = $next($request);

        $endTime = microtime(true);
        $endMemory = memory_get_usage();

        $executionTime = $endTime - $startTime;
        $memoryUsage = $endMemory - $startMemory;

        // Log slow requests
        if ($executionTime > 2.0) {
            Log::warning('Slow request detected', [
                'url' => $request->fullUrl(),
                'method' => $request->method(),
                'execution_time' => $executionTime,
                'memory_usage' => $memoryUsage,
                'user_id' => auth()->id(),
            ]);
        }

        return $response;
    }
}
```

## 🔄 Backup Strategy

### Database Backup

```bash
#!/bin/bash
# backup-database.sh

DB_NAME="simpeg_production"
DB_USER="backup_user"
DB_PASS="backup_password"
BACKUP_DIR="/var/backups/simpeg/database"
DATE=$(date +%Y%m%d_%H%M%S)

# Create backup directory
mkdir -p $BACKUP_DIR

# Create database backup
mysqldump -u $DB_USER -p$DB_PASS $DB_NAME | gzip > $BACKUP_DIR/backup_$DATE.sql.gz

# Keep only last 7 days of backups
find $BACKUP_DIR -name "backup_*.sql.gz" -mtime +7 -delete

echo "Database backup completed: backup_$DATE.sql.gz"
```

### File Backup

```bash
#!/bin/bash
# backup-files.sh

APP_DIR="/var/www/simpeg-backend"
BACKUP_DIR="/var/backups/simpeg/files"
DATE=$(date +%Y%m%d_%H%M%S)

# Create backup directory
mkdir -p $BACKUP_DIR

# Create file backup
tar -czf $BACKUP_DIR/files_$DATE.tar.gz -C $APP_DIR storage/app/public

# Keep only last 7 days of backups
find $BACKUP_DIR -name "files_*.tar.gz" -mtime +7 -delete

echo "File backup completed: files_$DATE.tar.gz"
```

### Automated Backup with Cron

```bash
# Add to crontab
# Database backup daily at 2 AM
0 2 * * * /var/scripts/backup-database.sh >> /var/log/backup-database.log 2>&1

# File backup daily at 3 AM
0 3 * * * /var/scripts/backup-files.sh >> /var/log/backup-files.log 2>&1
```

## 🔒 Security Configuration

### SSL/TLS Setup

```bash
# Generate SSL certificate (Let's Encrypt)
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d api.simpeg.unism.ac.id
```

### Firewall Configuration

```bash
# UFW Firewall
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

### Laravel Security

```php
// config/session.php
'lifetime' => 120,
'expire_on_close' => false,
'encrypt' => true,
'secure' => true,
'http_only' => true,
'same_site' => 'strict',
```

## 📝 Deployment Checklist

### Pre-Deployment

- [ ] Code review completed
- [ ] Tests passing (Unit, Feature, Integration)
- [ ] Security scan completed
- [ ] Performance testing done
- [ ] Database migration tested
- [ ] Backup strategy verified

### Deployment

- [ ] Environment variables configured
- [ ] SSL certificate installed
- [ ] Database migrations run
- [ ] Cache cleared and optimized
- [ ] Queue workers restarted
- [ ] Health check passing

### Post-Deployment

- [ ] Application monitoring active
- [ ] Error tracking configured
- [ ] Performance monitoring setup
- [ ] Backup schedule verified
- [ ] Documentation updated
- [ ] Team notified

---

**Status**: ⏳ Ready for Implementation  
**Priority**: Medium  
**Estimated Time**: 2-3 days  
**Dependencies**: Testing Strategy completed
