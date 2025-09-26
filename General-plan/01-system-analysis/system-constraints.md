# System Constraints - SIMPEG UNISM

## 📋 Overview

Dokumen ini mendefinisikan system constraints untuk sistem SIMPEG (Sistem Informasi Manajemen Pegawai) Universitas Islam Negeri Sultan Maulana Hasanuddin.

## 🔧 Technical Constraints

### 1. Technology Stack Constraints

#### 1.1 Backend Technology

- ✅ **Framework**: Laravel 10+ (PHP 8.1+)
- ✅ **Database**: MySQL 8.0+ atau PostgreSQL 13+
- ✅ **Cache**: Redis 6.0+
- ✅ **Queue**: Redis Queue atau Database Queue
- ✅ **File Storage**: Local storage atau AWS S3 compatible
- ✅ **Web Server**: Apache 2.4+ atau Nginx 1.18+

#### 1.2 Frontend Technology

- ✅ **Framework**: Vue.js 3+ (untuk admin panel)
- ✅ **UI Library**: Bootstrap 5+ atau Tailwind CSS
- ✅ **Build Tool**: Vite atau Webpack
- ✅ **Package Manager**: npm atau yarn
- ✅ **Browser Support**: Modern browsers (Chrome 90+, Firefox 88+, Safari 14+, Edge 90+)

#### 1.3 Infrastructure Technology

- ✅ **Operating System**: Ubuntu 20.04 LTS+ atau CentOS 8+
- ✅ **PHP Version**: PHP 8.1+ dengan extensions yang diperlukan
- ✅ **Node.js**: Node.js 16+ (untuk frontend build)
- ✅ **SSL/TLS**: TLS 1.2+ untuk secure connections
- ✅ **Load Balancer**: Nginx atau HAProxy

### 2. Database Constraints

#### 2.1 Database Design Constraints

- ✅ **Naming Convention**: snake_case untuk table dan column names
- ✅ **Primary Keys**: Auto-incrementing bigint untuk primary keys
- ✅ **Foreign Keys**: Semua relasi harus menggunakan foreign key constraints
- ✅ **Indexes**: Index pada field yang sering digunakan untuk query
- ✅ **Soft Deletes**: Implement soft delete untuk data historis
- ✅ **Timestamps**: created_at dan updated_at pada semua tabel

#### 2.2 Data Constraints

- ✅ **Data Types**: Gunakan data types yang sesuai (string, integer, boolean, date, text)
- ✅ **Field Lengths**: Limit field lengths sesuai kebutuhan
- ✅ **Null Constraints**: Define null constraints dengan jelas
- ✅ **Unique Constraints**: Implement unique constraints untuk field yang unik
- ✅ **Check Constraints**: Implement check constraints untuk validasi data

#### 2.3 Performance Constraints

- ✅ **Query Performance**: Database queries harus < 1 detik
- ✅ **Connection Pooling**: Implement connection pooling
- ✅ **Query Optimization**: Optimize queries dengan proper indexes
- ✅ **Data Archiving**: Archive old data untuk performance
- ✅ **Partitioning**: Consider partitioning untuk large tables

### 3. API Constraints

#### 3.1 API Design Constraints

- ✅ **RESTful Design**: Follow REST principles
- ✅ **HTTP Methods**: Gunakan HTTP methods yang sesuai (GET, POST, PUT, DELETE)
- ✅ **Status Codes**: Gunakan HTTP status codes yang standard
- ✅ **JSON Format**: Semua API responses dalam format JSON
- ✅ **API Versioning**: Implement API versioning (v1, v2, dll.)
- ✅ **Rate Limiting**: Implement rate limiting (1000 requests/hour per user)

#### 3.2 Authentication Constraints

- ✅ **JWT Tokens**: Gunakan JWT untuk authentication
- ✅ **Token Expiration**: Token expiration 24 jam
- ✅ **Refresh Tokens**: Implement refresh token mechanism
- ✅ **Password Policy**: Minimum 8 karakter, kombinasi huruf/angka/simbol
- ✅ **Session Management**: Session timeout 30 menit inaktivitas

#### 3.3 Security Constraints

- ✅ **HTTPS Only**: Semua komunikasi menggunakan HTTPS
- ✅ **Input Validation**: Validate semua input data
- ✅ **SQL Injection Prevention**: Gunakan parameterized queries
- ✅ **XSS Protection**: Implement XSS protection
- ✅ **CSRF Protection**: Implement CSRF protection

### 4. Performance Constraints

#### 4.1 Response Time Constraints

- ✅ **API Response**: < 2 detik untuk 95% requests
- ✅ **Database Queries**: < 1 detik untuk 95% queries
- ✅ **Page Load**: < 3 detik untuk page load
- ✅ **File Upload**: < 30 detik untuk file upload
- ✅ **Report Generation**: < 30 detik untuk report generation

#### 4.2 Resource Constraints

- ✅ **Memory Usage**: < 512MB per PHP process
- ✅ **CPU Usage**: < 70% average CPU usage
- ✅ **Disk Usage**: < 80% disk usage
- ✅ **Network Bandwidth**: < 100Mbps average usage
- ✅ **Database Connections**: < 100 concurrent connections

#### 4.3 Scalability Constraints

- ✅ **Concurrent Users**: Support 1000+ concurrent users
- ✅ **Data Volume**: Support 100,000+ employee records
- ✅ **API Requests**: Support 100+ requests per second
- ✅ **File Storage**: Support 10GB+ file storage
- ✅ **Database Size**: Support 1GB+ database size

## 💼 Business Constraints

### 1. Budget Constraints

#### 1.1 Infrastructure Costs

- ✅ **Server Costs**: Budget terbatas untuk server infrastructure
- ✅ **Database Costs**: Budget terbatas untuk database licensing
- ✅ **Storage Costs**: Budget terbatas untuk storage
- ✅ **Bandwidth Costs**: Budget terbatas untuk bandwidth
- ✅ **Third-party Costs**: Budget terbatas untuk third-party services

#### 1.2 Development Costs

- ✅ **Development Time**: Timeline project yang ketat
- ✅ **Team Size**: Tim development terbatas
- ✅ **Training Costs**: Budget terbatas untuk training
- ✅ **Testing Costs**: Budget terbatas untuk testing tools
- ✅ **Maintenance Costs**: Budget terbatas untuk maintenance

### 2. Timeline Constraints

#### 2.1 Development Timeline

- ✅ **Phase 1**: Database design dan setup (2 minggu)
- ✅ **Phase 2**: Core API development (4 minggu)
- ✅ **Phase 3**: Employee management module (3 minggu)
- ✅ **Phase 4**: Organizational structure module (3 minggu)
- ✅ **Phase 5**: Reports dan audit module (2 minggu)
- ✅ **Phase 6**: Testing dan deployment (2 minggu)

#### 2.2 Delivery Constraints

- ✅ **MVP Delivery**: MVP harus selesai dalam 16 minggu
- ✅ **Testing Period**: 2 minggu untuk testing
- ✅ **User Training**: 1 minggu untuk user training
- ✅ **Go-live**: Go-live sesuai dengan timeline yang ditetapkan
- ✅ **Post-launch Support**: 4 minggu post-launch support

### 3. Resource Constraints

#### 3.1 Human Resources

- ✅ **Developer**: 2-3 developers
- ✅ **Designer**: 1 UI/UX designer
- ✅ **Tester**: 1 QA tester
- ✅ **Project Manager**: 1 project manager
- ✅ **Domain Expert**: 1 HR domain expert

#### 3.2 Infrastructure Resources

- ✅ **Development Server**: 1 development server
- ✅ **Staging Server**: 1 staging server
- ✅ **Production Server**: 1 production server
- ✅ **Database Server**: 1 database server
- ✅ **Cache Server**: 1 Redis server

### 4. Regulatory Constraints

#### 4.1 Data Protection

- ✅ **Data Privacy**: Compliance dengan data privacy regulations
- ✅ **Data Retention**: Compliance dengan data retention policies
- ✅ **Data Security**: Compliance dengan data security standards
- ✅ **Audit Requirements**: Compliance dengan audit requirements
- ✅ **Reporting Requirements**: Compliance dengan reporting requirements

#### 4.2 Educational Regulations

- ✅ **Educational Standards**: Compliance dengan educational standards
- ✅ **Institutional Policies**: Compliance dengan institutional policies
- ✅ **Academic Calendar**: Compliance dengan academic calendar
- ✅ **Student Data**: Compliance dengan student data protection
- ✅ **Employee Data**: Compliance dengan employee data protection

## 🚫 Operational Constraints

### 1. User Constraints

#### 1.1 User Experience Constraints

- ✅ **Learning Curve**: System harus mudah dipelajari (max 2 jam training)
- ✅ **User Interface**: Interface harus user-friendly
- ✅ **Accessibility**: System harus accessible untuk users dengan disabilities
- ✅ **Mobile Support**: System harus accessible dari mobile devices
- ✅ **Browser Support**: System harus compatible dengan major browsers

#### 1.2 User Training Constraints

- ✅ **Training Time**: Max 4 jam training untuk end users
- ✅ **Training Materials**: Training materials harus comprehensive
- ✅ **Support**: Support harus available selama jam kerja
- ✅ **Documentation**: Documentation harus up-to-date
- ✅ **Help System**: Built-in help system harus available

### 2. Maintenance Constraints

#### 2.1 System Maintenance

- ✅ **Maintenance Window**: Maintenance hanya pada weekend atau after hours
- ✅ **Downtime**: Max 4 jam downtime per maintenance
- ✅ **Backup**: Daily automated backups
- ✅ **Updates**: Monthly security updates
- ✅ **Monitoring**: 24/7 system monitoring

#### 2.2 Support Constraints

- ✅ **Support Hours**: Support available 8AM-5PM (Monday-Friday)
- ✅ **Response Time**: Max 4 jam response time untuk critical issues
- ✅ **Resolution Time**: Max 24 jam resolution time untuk critical issues
- ✅ **Escalation**: Clear escalation path untuk issues
- ✅ **Documentation**: Comprehensive support documentation

### 3. Integration Constraints

#### 3.1 External System Integration

- ✅ **API Sister**: Integration dengan API Sister dengan rate limiting
- ✅ **Data Sync**: Data sync hanya pada jam kerja (8AM-5PM)
- ✅ **Error Handling**: Robust error handling untuk external services
- ✅ **Fallback**: Fallback mechanism jika external service down
- ✅ **Monitoring**: Monitor external service health

#### 3.2 Internal System Integration

- ✅ **Database**: Single database instance untuk production
- ✅ **Cache**: Redis cache untuk performance
- ✅ **Queue**: Database queue untuk background jobs
- ✅ **Storage**: Local file storage atau cloud storage
- ✅ **Monitoring**: Application monitoring dan logging

## 📊 Compliance Constraints

### 1. Security Compliance

- ✅ **Security Standards**: Compliance dengan security standards
- ✅ **Encryption**: Data encryption untuk sensitive data
- ✅ **Access Control**: Role-based access control
- ✅ **Audit Trail**: Complete audit trail
- ✅ **Incident Response**: Incident response plan

### 2. Data Compliance

- ✅ **Data Protection**: Compliance dengan data protection laws
- ✅ **Data Retention**: Compliance dengan data retention policies
- ✅ **Data Portability**: Data export capability
- ✅ **Data Deletion**: Data deletion capability
- ✅ **Data Consent**: User consent untuk data processing

### 3. Audit Compliance

- ✅ **Audit Trail**: Complete audit trail untuk all operations
- ✅ **Audit Logging**: Comprehensive audit logging
- ✅ **Audit Reports**: Generate audit reports
- ✅ **Audit Access**: Controlled access ke audit data
- ✅ **Audit Retention**: Audit data retention policy

## 🎯 Quality Constraints

### 1. Code Quality

- ✅ **Code Standards**: Follow coding standards
- ✅ **Code Review**: Mandatory code review
- ✅ **Testing**: Minimum 80% code coverage
- ✅ **Documentation**: Comprehensive code documentation
- ✅ **Refactoring**: Regular code refactoring

### 2. System Quality

- ✅ **Performance**: Meet performance requirements
- ✅ **Reliability**: Meet reliability requirements
- ✅ **Security**: Meet security requirements
- ✅ **Usability**: Meet usability requirements
- ✅ **Maintainability**: Meet maintainability requirements

### 3. Process Quality

- ✅ **Development Process**: Follow development process
- ✅ **Testing Process**: Follow testing process
- ✅ **Deployment Process**: Follow deployment process
- ✅ **Maintenance Process**: Follow maintenance process
- ✅ **Support Process**: Follow support process

---

**Dokumen ini dibuat**: 2024-01-01  
**Versi**: v1.0.0  
**Status**: Complete
