# S-005: Admin Configuration & User Management

**Story Type**: User Story
**Priority**: Medium
**Estimate**: 3 hours
**Epoch**: EPOCH 1 - Core Operations (MVP)
**Status**: ⏳ Pending Approval

---

## User Story

**As a** shop owner/administrator
**I want to** configure system settings and manage user accounts
**So that** I can control pricing, user access, and operational parameters without developer help

---

## Business Context

**Current Pain Point:**
- Cannot change prices without code changes
- Cannot add/remove technician accounts
- No role-based permissions (everyone is admin)
- Hard-coded business rules

**Business Value:**
- Self-service price list management
- Control over who can access the system
- Role-based permissions (admin vs technician)
- Operational flexibility without developer
- Audit trail of system changes

---

## Acceptance Criteria

### AC-1: Price List Management
- [ ] When I view price list, I see all service types with current prices
- [ ] When I update a price, I can set effective date (today or future)
- [ ] When I save price change, old price remains for historical orders
- [ ] When I view price history, I see all changes with dates and who made them

### AC-2: User Account Management
- [ ] When I add a new user, I can set: username, password, role (Admin or Technician)
- [ ] When I assign "Admin" role, user can access all features including settings
- [ ] When I assign "Technician" role, user can only access orders/tanks (no settings)
- [ ] When I deactivate a user, they cannot log in but their historical data remains

### AC-3: Role-Based Permissions
- [ ] When a Technician tries to access admin settings, they see "Access Denied"
- [ ] When a Technician creates an order, it logs their username
- [ ] When an Admin views audit log, they see who made each change
- [ ] When a user's role changes, permissions update on next login

### AC-4: System Configuration
- [ ] When I configure shop settings, I can set: shop name, contact info, timezone, delivery truck capacity (max tanks)
- [ ] When I set delivery truck capacity to 30, order system enforces this limit
- [ ] When I change timezone, all timestamps display in correct timezone
- [ ] When I save shop settings, changes apply immediately

### AC-5: Audit Logging
- [ ] When any user makes a change (order, tank, price, settings), it logs: who, what, when
- [ ] When I view audit log, I can filter by: user, action type, date range
- [ ] When I export audit log, CSV downloads with all filtered entries
- [ ] When critical action happens (price change, user deactivation), email alert sends to owner

---

## Technical Requirements

### Data Model: User (Extended Django User)

**Required Fields:**
- `username` (unique, from Django User model)
- `email` (from Django User model)
- `first_name`, `last_name` (from Django User model)
- `role` (enum: ADMIN, TECHNICIAN)
- `is_active` (boolean, from Django User model - for deactivation)
- `created_at` (datetime)
- `last_login` (datetime, from Django User model)

### Data Model: ShopConfiguration (Singleton)

**Required Fields:**
- `shop_name` (string)
- `contact_phone` (string)
- `contact_email` (string)
- `timezone` (string, e.g., "America/Los_Angeles")
- `delivery_truck_capacity` (integer, default 30)
- `updated_by` (ForeignKey to User)
- `updated_at` (datetime)

### Data Model: AuditLog

**Required Fields:**
- `user` (ForeignKey to User)
- `action_type` (enum: CREATE, UPDATE, DELETE, STATUS_CHANGE, PRICE_CHANGE, USER_CHANGE, CONFIG_CHANGE)
- `model_name` (string: ServiceOrder, Tank, Customer, etc.)
- `object_id` (integer, ID of affected object)
- `changes` (JSON: {"field": {"old": "value", "new": "value"}})
- `timestamp` (datetime, auto)

### Data Model: ServicePrice (Enhanced from S-002)

**Add Fields:**
- `updated_by` (ForeignKey to User)
- `updated_at` (datetime)

### API Endpoints

**Required:**
- `GET /api/prices/` - List all active prices
- `PATCH /api/prices/{id}/` - Update price (creates new version with effective_date)
- `GET /api/prices/{id}/history/` - Get price change history
- `POST /api/users/` - Create user (admin only)
- `GET /api/users/` - List users (admin only)
- `PATCH /api/users/{id}/` - Update user (admin only)
- `POST /api/users/{id}/deactivate/` - Deactivate user (admin only)
- `GET /api/config/` - Get shop configuration
- `PATCH /api/config/` - Update shop configuration (admin only)
- `GET /api/audit-log/` - Get audit log (admin only, with filters)
- `GET /api/audit-log/export/` - Export audit log CSV (admin only)

**Query Parameters (Audit Log):**
- `?user=<user_id>`
- `?action_type=PRICE_CHANGE`
- `?date_from=2025-09-01`
- `?date_to=2025-09-30`
- `?model_name=ServiceOrder`

### UI Requirements

**Admin Settings Page (Admin Only):**

**Tab 1: Price List Management**
- Table: Service Type, Current Price, Effective Date, Last Updated By, Actions
- Edit button per price → Modal with: New Price, Effective Date picker
- Price History button → Modal showing all price changes for that service
- Validation: Price must be > 0, effective date cannot be in past (except today)

**Tab 2: User Management**
- Table: Username, Name, Role, Last Login, Status, Actions
- Add User button → Form: Username, Password, Email, First Name, Last Name, Role dropdown
- Edit button → Form to update user info (cannot change username)
- Deactivate button → Confirmation modal, then deactivate
- Password reset button → Generates reset link or temporary password

**Tab 3: Shop Configuration**
- Form: Shop Name, Contact Phone, Contact Email, Timezone dropdown, Delivery Truck Capacity
- Save button
- Preview section showing how settings affect system (e.g., "Delivery orders limited to 30 tanks")

**Tab 4: Audit Log**
- Table: Timestamp, User, Action, Model, Changes (expandable JSON view)
- Filters: User dropdown, Action Type, Date Range picker
- Export CSV button
- Pagination (50 entries per page)
- Expandable row to show full changes JSON

**Permissions Enforcement:**
- When non-admin user tries to access `/admin/settings`, redirect to dashboard with error: "Admin access required"
- When technician tries to access API admin endpoints, return 403 Forbidden
- When technician views order, show "Created by" field but no edit access to other users' orders (EPOCH 2 refinement)

---

## Definition of Done

### Development Complete
- [ ] User model extended with role field
- [ ] ShopConfiguration model (singleton pattern)
- [ ] AuditLog model with automatic logging
- [ ] ServicePrice model updated with audit fields
- [ ] Admin API endpoints (11 endpoints)
- [ ] Admin settings UI (4 tabs)
- [ ] Role-based permission middleware
- [ ] Audit logging integrated into all CRUD operations

### Testing Complete (>95% Coverage)
- [ ] Unit tests: Permission checks (admin vs technician) (10 tests)
- [ ] Unit tests: Audit log creation on changes (8 tests)
- [ ] Unit tests: Price versioning (6 tests)
- [ ] API tests: Admin endpoints (15 tests)
- [ ] API tests: Permission denials (403 responses) (8 tests)
- [ ] Integration tests: Price update creates audit log (5 tests)
- [ ] Integration tests: User deactivation prevents login (4 tests)
- [ ] UI tests: Admin settings forms (6 tests)
- [ ] Edge case: Concurrent price updates (optimistic locking)
- [ ] Edge case: Deactivate currently logged-in user

### Quality Assurance
- [ ] Admin can update all settings
- [ ] Technician cannot access admin features
- [ ] Audit log captures all critical changes
- [ ] Price history is accurate and immutable
- [ ] User deactivation is immediate (logout enforced)
- [ ] Shop configuration changes apply without restart

### Documentation
- [ ] API endpoints documented (admin endpoints)
- [ ] Permission model documented (RBAC diagram)
- [ ] Admin guide: "Managing users and permissions"
- [ ] Admin guide: "Updating price list"
- [ ] Admin guide: "Viewing audit logs"

---

## Test Scenarios

### Happy Path (Admin User)
1. **Update Service Price**
   - Admin logs in
   - Goes to Settings → Price List
   - Clicks "Edit" on "Nitrox 32% Fill"
   - Changes price from $20 to $22
   - Sets effective date to today
   - Saves
   - New orders use $22, old orders keep $20
   - Audit log shows: "Admin changed Nitrox 32% price from $20 to $22"

2. **Add New Technician User**
   - Admin goes to Settings → Users
   - Clicks "Add User"
   - Enters: username "tech_john", password "SecurePass123", role "Technician"
   - Saves
   - New user appears in list
   - Audit log shows: "Admin created user tech_john (Technician)"

3. **Update Shop Configuration**
   - Admin goes to Settings → Shop Config
   - Changes delivery truck capacity from 30 to 35
   - Saves
   - Delivery system now allows up to 35 tanks per route
   - Audit log shows: "Admin changed delivery_truck_capacity from 30 to 35"

4. **View Audit Log**
   - Admin goes to Settings → Audit Log
   - Filters by action type "PRICE_CHANGE"
   - Sees all price changes with timestamps and users
   - Exports CSV with filtered entries

### Permission Enforcement (Technician User)
5. **Technician Tries to Access Admin Settings**
   - Technician logs in
   - Tries to navigate to /admin/settings
   - Redirected to dashboard
   - Error message: "Admin access required"
   - Audit log shows: "tech_john attempted to access admin settings (denied)"

6. **Technician API Permission Denied**
   - Technician makes API request: `PATCH /api/prices/1/`
   - Response: 403 Forbidden
   - Error: "You do not have permission to perform this action"

### Edge Cases
7. **Concurrent Price Update**
   - Admin A starts editing price at 10:00:00
   - Admin B starts editing same price at 10:00:05
   - Admin A saves at 10:00:10 (price changes to $22)
   - Admin B saves at 10:00:15 (tries to change to $23)
   - System detects conflict: "Price was updated by Admin A. Please refresh and try again."
   - Admin B refreshes, sees $22, can then update to $23

8. **Deactivate Currently Logged-In User**
   - Admin A deactivates Admin B's account
   - Admin B is currently logged in
   - On Admin B's next action, session is invalidated
   - Admin B is logged out with message: "Your account has been deactivated"

9. **Price Effective Date in Future**
   - Admin updates price with effective date = tomorrow
   - Today's orders still use current price
   - Tomorrow, orders automatically use new price
   - System handles date transition smoothly

### Audit Logging
10. **Comprehensive Audit Trail**
    - Admin creates order ORD-2025-00001
    - Audit log: "Admin created ServiceOrder #1"
    - Admin updates order status to "Completed"
    - Audit log: "Admin changed ServiceOrder #1 status from PENDING to COMPLETED"
    - Admin marks order paid
    - Audit log: "Admin changed ServiceOrder #1 status from COMPLETED to PAID"

---

## Dependencies

**Requires:**
- S-002: Service Order Creation (price list exists)
- Django User authentication configured

**Enables:**
- Operational flexibility for shop owner
- Security and access control
- Compliance (audit trail)
- All future features (require RBAC)

---

## Notes for Implementation

**Singleton Pattern for ShopConfiguration:**
```python
class ShopConfiguration(models.Model):
    # ... fields ...

    def save(self, *args, **kwargs):
        self.pk = 1  # Force single instance
        super().save(*args, **kwargs)

    @classmethod
    def get_config(cls):
        obj, created = cls.objects.get_or_create(pk=1, defaults={
            'shop_name': 'Dive Tank Pro',
            'delivery_truck_capacity': 30
        })
        return obj
```

**Automatic Audit Logging (Django Signal):**
```python
from django.db.models.signals import post_save, pre_delete
from django.dispatch import receiver

@receiver(post_save, sender=ServiceOrder)
def log_order_change(sender, instance, created, **kwargs):
    action_type = 'CREATE' if created else 'UPDATE'
    # Detect field changes and log
    AuditLog.objects.create(
        user=instance.updated_by,  # Must track this in save
        action_type=action_type,
        model_name='ServiceOrder',
        object_id=instance.id,
        changes={...}  # Compare with previous state
    )
```

**Permission Middleware:**
```python
class RolePermissionMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        if request.path.startswith('/admin/settings'):
            if not request.user.is_authenticated or request.user.role != 'ADMIN':
                return redirect('dashboard')
        return self.get_response(request)
```

**Business Rules:**
- Only ADMIN users can access admin settings and APIs
- TECHNICIAN users can access: orders, tanks, customers, dashboard
- Audit logs are immutable (no edit/delete)
- Price changes create new ServicePrice record (versioning, not overwrite)
- User deactivation is soft delete (is_active=False)
- Shop configuration changes apply immediately (no restart)

**Email Alert for Critical Actions (Optional for EPOCH 1, can defer to EPOCH 2):**
- Price change >20% → Email to owner
- User deactivation → Email to owner
- Multiple failed login attempts → Email to owner

**Future Enhancements (EPOCH 2+):**
- Role expansion: Inspector, Driver, Customer Admin
- Fine-grained permissions (e.g., "can view but not edit orders")
- API key management for integrations
- Multi-factor authentication (MFA)
- Session timeout configuration
- IP whitelisting for admin access
- Data retention policies (auto-archive old audit logs)

---

## Estimated Effort Breakdown

**Total: 3 hours**

- Models & Migrations: 1 hour
- API Endpoints & Permissions: 1 hour
- Admin UI: 0.75 hours
- Testing: 0.25 hours

---

**Story Status**: Ready for SPEC approval
**Next Steps**: Awaiting client approval on EPOCH 1 scope

---

## EPOCH 1 SUMMARY

**Total Stories: 5**
**Total Estimate: 20 hours**

- S-001: Tank Inventory Management (6 hours)
- S-002: Service Order Creation - Fills Only (5 hours)
- S-003: Customer Database (3 hours)
- S-004: Order Status Dashboard (3 hours)
- S-005: Admin Configuration & User Management (3 hours)

**EPOCH 1 Delivers:**
✅ Complete tank inventory system
✅ Digital order management (walk-in fills)
✅ Customer database
✅ Real-time dashboard
✅ Admin controls (pricing, users, settings)
✅ Role-based permissions
✅ Audit logging

**What's NOT in EPOCH 1:**
❌ Customer portal (EPOCH 2)
❌ Inspections/hydro testing (EPOCH 2)
❌ Delivery management (EPOCH 3)
❌ Gas blend logging (EPOCH 3)
❌ Equipment maintenance (EPOCH 3)
❌ Online payments (EPOCH 4)

**This is a production-ready MVP** that eliminates paper tickets and organizes operations.
