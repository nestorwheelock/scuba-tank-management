# TECHNICAL ARCHITECTURE

**Project**: DiveTank Pro
**Version**: EPOCH 1 (v0.1.0)
**Last Updated**: 2025-09-30

---

## ARCHITECTURE OVERVIEW

**Architecture Pattern**: Monolithic Django application with RESTful API and modern frontend

**Key Principles:**
- API-first design (backend/frontend separation)
- Mobile-first responsive design
- Test-driven development (>95% coverage)
- Security by default (HTTPS, RBAC, CSRF protection)
- Scalable foundation for future epochs

---

## TECHNOLOGY STACK

### Backend

**Framework**: Django 5.0+
- **Why**: Mature, secure, batteries-included framework
- **Benefits**: Built-in admin, ORM, migrations, auth, security features
- **Risk**: None (proven, stable, well-documented)

**Language**: Python 3.11+
- **Why**: Team expertise, excellent Django ecosystem
- **Benefits**: Type hints, performance improvements, great libraries
- **Risk**: None (stable, widely adopted)

**Database**: PostgreSQL 15+
- **Why**: ACID compliance, JSON support, reliability
- **Benefits**: Complex queries, data integrity, full-text search
- **Risk**: Requires PostgreSQL-specific hosting (manageable)

**API Framework**: Django REST Framework (DRF) 3.14+
- **Why**: Industry standard for Django REST APIs
- **Benefits**: Serializers, viewsets, permissions, browsable API
- **Risk**: None (well-maintained, extensive docs)

**Background Jobs**: Celery 5.3+ with Redis
- **Why**: Async task processing (notifications, reports)
- **Benefits**: Scheduled tasks, retries, monitoring
- **Risk**: Adds complexity (but worth it for EPOCH 2+ notifications)
- **EPOCH 1**: Can defer, use synchronous processing initially

**Cache**: Redis 7+
- **Why**: Fast in-memory data store
- **Benefits**: Session storage, caching, Celery broker
- **Risk**: Adds infrastructure (but simple to deploy)
- **EPOCH 1**: Optional, can use Django default cache initially

### Frontend

**Framework**: React 18+ (Create React App or Next.js)
- **Why**: Component-based, excellent ecosystem, mobile-friendly
- **Benefits**: Reusable components, virtual DOM, great tooling
- **Risk**: None (industry standard)

**Alternative**: Vue.js 3+ (if team prefers)
- **Why**: Simpler learning curve, excellent docs
- **Benefits**: Progressive framework, easy integration
- **Risk**: Smaller ecosystem than React (but sufficient)

**UI Library**: Tailwind CSS + Headless UI or Material-UI (MUI)
- **Why**: Rapid UI development, mobile-first
- **Benefits**: Pre-built responsive components, consistent design
- **Risk**: None (widely adopted)

**State Management**: React Context API (EPOCH 1), Redux Toolkit (EPOCH 2+)
- **Why**: Simple for EPOCH 1, scalable for future
- **Benefits**: Centralized state, predictable updates
- **Risk**: None (can start simple, add Redux later)

**HTTP Client**: Axios or Fetch API
- **Why**: Easy API communication
- **Benefits**: Interceptors, request/response transformations
- **Risk**: None (standard)

### Mobile (Progressive Web App)

**Approach**: Responsive Web App (RWA) → Progressive Web App (PWA)
- **EPOCH 1**: Mobile-responsive web (works on all devices)
- **EPOCH 2**: Add PWA features (offline, install, push notifications)
- **EPOCH 3**: PWA for delivery drivers (offline route access)

**Why PWA over Native:**
- Single codebase (web + mobile)
- No app store approval delays
- Instant updates
- Lower development cost
- Works offline when needed

**PWA Features (EPOCH 2+):**
- Service workers for offline support
- Add to home screen
- Push notifications
- Background sync

### DevOps & Infrastructure

**Containerization**: Docker + Docker Compose
- **Why**: Consistent environments, easy deployment
- **Benefits**: Dev/prod parity, reproducible builds
- **Risk**: None (industry standard)

**Hosting Options** (Choose one):
1. **Railway / Render / Fly.io** (Recommended for MVP)
   - **Why**: Simple Django deployment, managed PostgreSQL/Redis
   - **Cost**: ~$20-50/month
   - **Benefits**: Auto-deploy, SSL, scaling, backups

2. **AWS / GCP / Azure** (For production scale)
   - **Why**: Full control, scalability
   - **Cost**: ~$50-200/month depending on usage
   - **Benefits**: All services available, mature ecosystem

3. **DigitalOcean App Platform**
   - **Why**: Middle ground (simple + flexible)
   - **Cost**: ~$30-80/month
   - **Benefits**: Managed services, reasonable pricing

**Version Control**: Git + GitHub
- **Why**: Industry standard, excellent tooling
- **Benefits**: Code review, CI/CD, issue tracking
- **Risk**: None

**CI/CD**: GitHub Actions
- **Why**: Free for public repos, integrated with GitHub
- **Benefits**: Automated testing, deployment
- **Workflow**:
  1. Push to main → Run tests
  2. Tests pass → Deploy to staging
  3. Manual approval → Deploy to production

---

## DATA ARCHITECTURE

### Database Schema (EPOCH 1)

**Core Models:**

```
User (Django built-in, extended)
├── id (PK)
├── username (unique)
├── email
├── password (hashed)
├── role (ADMIN | TECHNICIAN)
├── is_active
└── Relationships:
    ├── created_orders → ServiceOrder
    ├── audit_logs → AuditLog
    └── updated_configs → ShopConfiguration

Customer
├── id (PK)
├── customer_type (INDIVIDUAL | DIVE_SHOP | OPERATOR)
├── name (indexed)
├── email (unique, nullable)
├── phone (normalized)
├── company_name (nullable)
├── notes
├── created_at
└── Relationships:
    ├── tanks → Tank
    └── orders → ServiceOrder

Tank
├── id (PK)
├── serial_number (unique, indexed)
├── size_cuft (40 | 63 | 80 | 100 | ...)
├── ownership_type (RENTAL | CLIENT_OWNED | OPERATOR_OWNED)
├── owner → Customer (FK, nullable)
├── current_location (IN_SHOP | OUT_FOR_SERVICE | WITH_CUSTOMER | ON_DELIVERY)
├── last_visual_date
├── last_hydro_date
├── notes
├── created_at
├── updated_at
└── Computed:
    ├── visual_status (Valid | Expiring Soon | Expired)
    ├── hydro_status (Valid | Expiring Soon | Expired)
    └── is_available_for_service

ServiceOrder
├── id (PK)
├── order_number (unique, auto-generated: ORD-YYYY-NNNNN)
├── customer → Customer (FK)
├── status (PENDING | IN_PROGRESS | COMPLETED | PAID)
├── order_type (WALK_IN | ADVANCE | DELIVERY)
├── subtotal (decimal, computed from items)
├── notes
├── created_by → User (FK)
├── created_at
├── updated_at
├── completed_at (nullable)
├── paid_at (nullable)
└── Relationships:
    └── items → ServiceOrderItem

ServiceOrderItem
├── id (PK)
├── order → ServiceOrder (FK)
├── tank → Tank (FK)
├── service_type (AIR_FILL | NITROX_32_FILL | VISUAL_INSPECTION | HYDRO_TEST)
├── price (decimal, from ServicePrice at time of order)
└── notes

ServicePrice
├── id (PK)
├── service_type (unique when is_active=True)
├── price (decimal)
├── is_active (boolean)
├── effective_date
├── updated_by → User (FK)
└── updated_at

ShopConfiguration (Singleton)
├── id (PK, always 1)
├── shop_name
├── contact_phone
├── contact_email
├── timezone
├── delivery_truck_capacity (integer)
├── updated_by → User (FK)
└── updated_at

AuditLog
├── id (PK)
├── user → User (FK)
├── action_type (CREATE | UPDATE | DELETE | STATUS_CHANGE | PRICE_CHANGE | USER_CHANGE | CONFIG_CHANGE)
├── model_name (string)
├── object_id (integer)
├── changes (JSON)
└── timestamp (indexed)
```

### Indexes (Performance Optimization)

**Critical Indexes:**
- `Tank.serial_number` (unique index for fast lookup)
- `Customer.name` (GIN index for full-text search)
- `Customer.phone` (index for search)
- `ServiceOrder.order_number` (unique index)
- `ServiceOrder.created_at` (index for date filtering)
- `ServiceOrder.status` (index for dashboard queries)
- `AuditLog.timestamp` (index for audit log queries)
- `AuditLog.user` (FK index for filtering)

**Composite Indexes (EPOCH 2+ if needed):**
- `(ServiceOrder.customer, ServiceOrder.created_at)` for customer history
- `(Tank.owner, Tank.current_location)` for owner's tank list

---

## API ARCHITECTURE

### API Design Principles

1. **RESTful Conventions**
   - Nouns for resources (not verbs)
   - HTTP methods: GET (read), POST (create), PATCH (update), DELETE (delete)
   - Status codes: 200 (OK), 201 (Created), 400 (Bad Request), 403 (Forbidden), 404 (Not Found)

2. **Consistent Response Format**
   ```json
   {
     "success": true,
     "data": {...},
     "message": "Operation successful"
   }
   ```

   ```json
   {
     "success": false,
     "errors": {"field": ["Error message"]},
     "message": "Validation failed"
   }
   ```

3. **Pagination** (for list endpoints)
   ```json
   {
     "count": 150,
     "next": "/api/tanks/?page=2",
     "previous": null,
     "results": [...]
   }
   ```

4. **Filtering & Search**
   - Query params: `?search=term&filter_field=value`
   - Example: `/api/tanks/?ownership_type=RENTAL&search=A123`

### API Endpoints (EPOCH 1)

**Authentication:**
- `POST /api/auth/login/` - User login (returns token)
- `POST /api/auth/logout/` - User logout
- `GET /api/auth/me/` - Get current user info

**Customers:**
- `POST /api/customers/` - Create customer
- `GET /api/customers/` - List customers (search, filter, paginate)
- `GET /api/customers/{id}/` - Get customer details (with tanks, orders)
- `PATCH /api/customers/{id}/` - Update customer
- `DELETE /api/customers/{id}/` - Delete customer (if no dependencies)

**Tanks:**
- `POST /api/tanks/` - Create tank
- `GET /api/tanks/` - List tanks (search, filter, paginate)
- `GET /api/tanks/{id}/` - Get tank details
- `PATCH /api/tanks/{id}/` - Update tank
- `DELETE /api/tanks/{id}/` - Delete tank
- `POST /api/tanks/bulk-update/` - Bulk location update
- `GET /api/tanks/export/` - Export CSV

**Orders:**
- `POST /api/orders/` - Create order
- `GET /api/orders/` - List orders (filter by status, date, customer)
- `GET /api/orders/{id}/` - Get order details
- `PATCH /api/orders/{id}/` - Update order
- `POST /api/orders/{id}/start/` - Start order (PENDING → IN_PROGRESS)
- `POST /api/orders/{id}/complete/` - Complete order (IN_PROGRESS → COMPLETED)
- `POST /api/orders/{id}/mark-paid/` - Mark paid (COMPLETED → PAID)
- `GET /api/orders/pending/` - Get pending orders
- `GET /api/orders/in-progress/` - Get in-progress orders
- `GET /api/orders/completed-today/` - Get today's completed orders
- `GET /api/orders/export/` - Export daily report CSV

**Dashboard:**
- `GET /api/dashboard/stats/` - Get dashboard summary stats

**Admin (Admin Role Only):**
- `GET /api/prices/` - List prices
- `PATCH /api/prices/{id}/` - Update price
- `GET /api/prices/{id}/history/` - Get price history
- `POST /api/users/` - Create user
- `GET /api/users/` - List users
- `PATCH /api/users/{id}/` - Update user
- `POST /api/users/{id}/deactivate/` - Deactivate user
- `GET /api/config/` - Get shop config
- `PATCH /api/config/` - Update shop config
- `GET /api/audit-log/` - Get audit log (with filters)
- `GET /api/audit-log/export/` - Export audit log CSV

**Total: ~45 endpoints for EPOCH 1**

### Authentication & Authorization

**Authentication Method**: Token-based (Django REST Framework Token Auth or JWT)

**EPOCH 1 (Simpler)**: Django REST Framework Token
- User logs in → Receives token
- Token stored in localStorage (frontend)
- Token sent in header: `Authorization: Token <token>`
- Token validated on every request

**EPOCH 2+ (More Secure)**: JWT (JSON Web Tokens)
- Access token (short-lived: 15 min)
- Refresh token (long-lived: 7 days)
- Auto-refresh mechanism

**Authorization**: Role-Based Access Control (RBAC)

**Permission Classes:**
```python
class IsAdmin(permissions.BasePermission):
    def has_permission(self, request, view):
        return request.user.is_authenticated and request.user.role == 'ADMIN'

class IsTechnician(permissions.BasePermission):
    def has_permission(self, request, view):
        return request.user.is_authenticated and request.user.role in ['ADMIN', 'TECHNICIAN']
```

**Applied to Views:**
```python
class OrderViewSet(viewsets.ModelViewSet):
    permission_classes = [IsTechnician]  # Techs and admins can access

class UserViewSet(viewsets.ModelViewSet):
    permission_classes = [IsAdmin]  # Only admins can manage users
```

---

## SECURITY ARCHITECTURE

### Security Measures (EPOCH 1)

**1. HTTPS Enforcement**
- All traffic over TLS/SSL
- HTTP redirects to HTTPS
- Hosting platform provides SSL cert (Let's Encrypt)

**2. Authentication Security**
- Passwords hashed with bcrypt (Django default)
- Minimum password requirements (8 chars, mixed case, numbers)
- Session timeout: 8 hours inactivity
- Logout on password change

**3. CSRF Protection**
- Django CSRF tokens for all POST/PATCH/DELETE
- CSRF cookie HttpOnly, SameSite=Strict

**4. XSS Prevention**
- Django auto-escapes templates
- React auto-escapes JSX
- Content Security Policy (CSP) headers

**5. SQL Injection Prevention**
- Django ORM (parameterized queries)
- No raw SQL (unless absolutely necessary, then parameterized)

**6. Access Control**
- Role-based permissions (ADMIN, TECHNICIAN)
- API endpoints enforce permissions
- Frontend hides UI, backend enforces access

**7. Rate Limiting**
- Login endpoint: 5 attempts per 15 minutes
- API endpoints: 100 requests per minute per user
- Uses Django Ratelimit or DRF Throttling

**8. Data Privacy**
- Audit logs immutable
- Passwords never logged
- Sensitive data not in error messages

**9. Dependency Security**
- Regular `pip audit` for vulnerabilities
- Automated dependency updates (Dependabot)

### Security Enhancements (EPOCH 2+)

- Multi-factor authentication (MFA)
- API key rotation
- IP whitelisting for admin access
- Penetration testing
- GDPR compliance (data export, right to deletion)

---

## DEPLOYMENT ARCHITECTURE

### Environments

**1. Development (Local)**
- Docker Compose: Django + PostgreSQL + Redis
- Hot reload for development
- Debug mode enabled
- Seed data for testing

**2. Staging (Cloud)**
- Identical to production (infrastructure)
- Used for UAT (User Acceptance Testing)
- Deploy on every merge to `develop` branch

**3. Production (Cloud)**
- High availability (auto-scaling if needed)
- Automated backups (daily)
- Monitoring and alerting
- Deploy on merge to `main` branch (with approval)

### Deployment Stack (Recommended: Railway/Render)

**Services:**
1. **Web (Django + Gunicorn)**
   - Container: Django app with Gunicorn WSGI server
   - Env vars: DATABASE_URL, REDIS_URL, SECRET_KEY, etc.
   - Auto-deploy on git push
   - SSL/HTTPS automatic

2. **Database (PostgreSQL)**
   - Managed PostgreSQL instance
   - Automated backups (daily, 7-day retention)
   - Connection pooling

3. **Cache/Queue (Redis)**
   - Managed Redis instance
   - Used for: Cache, Celery broker (EPOCH 2+)

4. **Static Files (S3 or CDN)**
   - Django static files served from S3/CloudFlare
   - `collectstatic` on deploy
   - Asset versioning for cache busting

5. **Background Workers (Celery)** - EPOCH 2+
   - Separate container for Celery workers
   - Same codebase as web, different command

### Deployment Process

**CI/CD Pipeline (GitHub Actions):**

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - Checkout code
      - Set up Python 3.11
      - Install dependencies
      - Run pytest (>95% coverage required)
      - Run black (code formatting check)
      - Run flake8 (linting)

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - Deploy to Railway/Render (automatic)
      - Run database migrations
      - Collect static files
      - Health check (GET /api/health/)
      - Notify team (Slack/Email)
```

**Rollback Strategy:**
- Railway/Render keep last 10 deployments
- One-click rollback if issues detected
- Database migrations are backward-compatible (no data loss)

---

## MONITORING & OBSERVABILITY

### Monitoring Stack (EPOCH 1 - Minimal)

**1. Application Monitoring**
- Django logging to files/stdout
- Error tracking: Sentry (free tier for MVP)
- Health check endpoint: `/api/health/` (returns 200 if DB connected)

**2. Infrastructure Monitoring**
- Platform built-in: Railway/Render dashboards
- Metrics: CPU, memory, request rate, response time
- Alerts: Email/Slack on service downtime

**3. Database Monitoring**
- Slow query log (queries >1 second)
- Connection pool monitoring
- Disk space alerts

### Monitoring Enhancements (EPOCH 2+)

- APM (Application Performance Monitoring): New Relic, DataDog
- Custom business metrics (orders/hour, revenue/day)
- User analytics: Mixpanel, Amplitude
- Uptime monitoring: UptimeRobot, Pingdom

---

## TESTING STRATEGY

### Test Pyramid (EPOCH 1)

**Unit Tests (70% of tests):**
- Model validation logic
- Business logic (certification status, order totals)
- Utility functions (phone normalization, order number generation)
- **Tools**: pytest, Django TestCase
- **Coverage**: >95% of models and utils

**Integration Tests (20% of tests):**
- API endpoints (CRUD operations)
- Authentication and permissions
- Database transactions
- **Tools**: DRF APITestCase, pytest-django
- **Coverage**: All API endpoints

**End-to-End Tests (10% of tests):**
- Critical user flows (create order, complete workflow)
- UI interactions (form submissions, navigation)
- **Tools**: Playwright or Cypress (EPOCH 2)
- **EPOCH 1**: Manual testing, automate in EPOCH 2

### Test Data Management

**Fixtures:**
- `customers.json` - Sample customers
- `tanks.json` - Sample tanks
- `prices.json` - Default price list
- `users.json` - Admin and technician test accounts

**Factory Pattern:**
```python
# factories.py
import factory
from .models import Tank, Customer

class CustomerFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Customer
    name = factory.Faker('name')
    email = factory.Faker('email')
    customer_type = 'INDIVIDUAL'

class TankFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Tank
    serial_number = factory.Sequence(lambda n: f'TANK{n:06d}')
    size_cuft = 80
    ownership_type = 'CLIENT_OWNED'
    owner = factory.SubFactory(CustomerFactory)
```

**Test Database:**
- Separate PostgreSQL database for tests
- Automatically created/destroyed per test run
- Isolated from development database

---

## PERFORMANCE CONSIDERATIONS

### Database Optimization (EPOCH 1)

**Query Optimization:**
- Use `select_related()` for foreign keys (avoid N+1)
- Use `prefetch_related()` for many-to-many
- Annotate for computed fields
- Example:
  ```python
  # Bad (N+1 query)
  tanks = Tank.objects.all()
  for tank in tanks:
      print(tank.owner.name)  # Hits DB each time

  # Good (1 query)
  tanks = Tank.objects.select_related('owner').all()
  for tank in tanks:
      print(tank.owner.name)  # Already loaded
  ```

**Caching Strategy (EPOCH 2+):**
- Cache dashboard stats (5-minute TTL)
- Cache price list (until updated)
- Cache shop configuration (until updated)

**Pagination:**
- List endpoints paginated (20-50 items per page)
- Infinite scroll or "Load More" on frontend

### Frontend Performance

**Code Splitting:**
- Lazy load routes (React.lazy)
- Separate bundles for admin vs technician views
- Vendor bundle separate from app bundle

**Asset Optimization:**
- Minified JS/CSS
- Compressed images (WebP format)
- CDN for static assets

**API Optimization:**
- Debounce search inputs (300ms)
- Batch API calls where possible
- Optimistic UI updates (update UI before API response)

---

## SCALABILITY ROADMAP

### EPOCH 1 (MVP) - Handles 100-200 tanks, 50 orders/day
- Single web server
- Managed PostgreSQL
- Synchronous processing

### EPOCH 2 - Handles 500 tanks, 200 orders/day
- Add Redis caching
- Add Celery for async tasks (notifications)
- WebSocket for real-time updates

### EPOCH 3 - Handles 1000+ tanks, 500+ orders/day
- Horizontal scaling (multiple web servers)
- Load balancer
- Read replicas for database
- CDN for static/media files

### EPOCH 4+ - Multi-location, 5000+ tanks
- Multi-tenant architecture
- Microservices (if needed)
- Message queue (RabbitMQ/Kafka)
- Advanced caching (Redis Cluster)

---

## TECHNOLOGY DECISIONS & TRADE-OFFS

### Django Monolith vs Microservices

**Decision**: Django Monolith (EPOCH 1-3)

**Rationale:**
- ✅ Faster development (all-in-one)
- ✅ Simpler deployment
- ✅ Easier testing
- ✅ Lower operational complexity
- ❌ Harder to scale specific services independently

**When to Reconsider**: If serving 10+ locations or 100k+ orders/year

### PostgreSQL vs MongoDB

**Decision**: PostgreSQL

**Rationale:**
- ✅ ACID compliance (critical for orders, payments)
- ✅ Relational data (customers, tanks, orders are highly relational)
- ✅ Mature Django ORM support
- ✅ JSON support for audit logs
- ❌ Less flexible schema (but we have clear schema)

**When to Reconsider**: Never for this use case (relational data is core)

### React vs Vue vs Svelte

**Decision**: React (or Vue if team prefers)

**Rationale:**
- ✅ Largest ecosystem
- ✅ Excellent mobile support
- ✅ Team familiarity (assumption)
- ✅ Great tooling (Create React App, Next.js)
- ❌ Steeper learning curve than Vue

**Alternative**: Vue is equally valid, simpler for smaller teams

### PWA vs Native Mobile Apps

**Decision**: PWA (EPOCH 2+)

**Rationale:**
- ✅ Single codebase
- ✅ No app store delays
- ✅ Instant updates
- ✅ Works offline
- ✅ 70% cheaper than native
- ❌ Limited native features (acceptable for our use case)

**When to Reconsider**: If need camera, GPS, or deep OS integration (EPOCH 4+)

---

## RISKS & MITIGATION

### Technical Risks

**Risk 1: Data Loss**
- **Mitigation**: Automated daily backups, point-in-time recovery, transaction safety

**Risk 2: Performance Degradation**
- **Mitigation**: Database indexes, query optimization, caching, monitoring

**Risk 3: Third-Party API Failures** (EPOCH 2+: Stripe, Twilio)
- **Mitigation**: Retry logic, fallback to manual, queue for later processing

**Risk 4: Security Breach**
- **Mitigation**: HTTPS, RBAC, rate limiting, security audits, penetration testing

**Risk 5: Deployment Failures**
- **Mitigation**: CI/CD, automated tests, health checks, rollback capability

---

## FUTURE ENHANCEMENTS (EPOCHS 2-4)

### EPOCH 2 Additions
- WebSocket for real-time dashboard
- Customer portal (separate auth)
- Visual inspection & hydro test tracking
- Email/SMS notifications (Twilio, SendGrid)

### EPOCH 3 Additions
- Delivery route optimization
- Mobile driver app (PWA)
- Gas blend logging & compliance
- Equipment maintenance tracking

### EPOCH 4 Additions
- Online payment processing (Stripe)
- Advanced analytics dashboard
- Multi-location support
- Accounting integration (QuickBooks)

---

## DEVELOPMENT SETUP (Quick Start)

**Prerequisites:**
- Python 3.11+
- Node.js 18+
- Docker & Docker Compose
- Git

**Setup Steps:**
```bash
# 1. Clone repository
git clone <repo-url>
cd divetank-pro

# 2. Start infrastructure (PostgreSQL, Redis)
docker-compose up -d

# 3. Backend setup
cd backend
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py loaddata fixtures/prices.json
python manage.py runserver

# 4. Frontend setup (separate terminal)
cd frontend
npm install
npm start

# 5. Access
# - Backend: http://localhost:8000
# - Frontend: http://localhost:3000
# - Admin: http://localhost:8000/admin
```

---

**Document Control:**
- Version: 1.0
- Last Updated: 2025-09-30
- Next Review: After EPOCH 1 completion
- Status: READY FOR DEVELOPMENT
