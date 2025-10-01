# S-003: Customer Database

**Story Type**: User Story
**Priority**: High
**Estimate**: 3 hours
**Epoch**: EPOCH 1 - Core Operations (MVP)
**Status**: ⏳ Pending Approval

---

## User Story

**As a** scuba tank fill station technician
**I want to** manage customer information in a digital database
**So that** I can quickly look up customer details and link tanks/orders to customers

---

## Business Context

**Current Pain Point:**
- Customer information scattered across paper forms, WhatsApp contacts, and memory
- Hard to track which customer owns which tanks
- Cannot easily find customer contact info
- No customer service history

**Business Value:**
- Centralized customer database
- Quick customer lookup for order creation
- Link tanks to owners automatically
- Foundation for customer portal (EPOCH 2)
- Contact customers for pickups/deliveries

---

## Acceptance Criteria

### AC-1: Customer CRUD Operations
- [ ] When I add a new customer, I can enter: name, email, phone, notes
- [ ] When I search by name, customer displays immediately
- [ ] When I search by phone, customer displays (partial match: "555-1234" finds "+1-555-1234-5678")
- [ ] When I update customer info, changes save and reflect instantly

### AC-2: Customer Quick Add (From Order)
- [ ] When creating an order, I can click "+ New Customer" without leaving order form
- [ ] When I enter basic info (name, phone), customer saves and auto-selects for order
- [ ] When I return to customer list, the new customer is there

### AC-3: Customer Details View
- [ ] When I view customer details, I see: contact info, owned tanks list, order history
- [ ] When customer has no tanks, message displays: "No tanks assigned"
- [ ] When customer has no orders, message displays: "No order history"
- [ ] When customer has tanks, list shows: serial #, size, last service date

### AC-4: Customer Search & Filter
- [ ] When I search by name, partial matches work (e.g., "John" finds "John Smith", "Johnny Doe")
- [ ] When I search by phone, system normalizes format (handles with/without dashes, country codes)
- [ ] When I search by email, exact and partial matches work
- [ ] When results exceed 20 customers, pagination works smoothly

### AC-5: Customer Type Classification
- [ ] When I add customer, I can select type: Individual Diver, Dive Shop, Operator
- [ ] When I filter by customer type, only those customers display
- [ ] When customer is Dive Shop, I can add company name and business details

---

## Technical Requirements

### Data Model: Customer

**Required Fields:**
- `customer_type` (enum: INDIVIDUAL, DIVE_SHOP, OPERATOR)
- `name` (string, indexed for search)
- `email` (string, nullable, unique where not null)
- `phone` (string, nullable, normalized)
- `company_name` (string, nullable - for DIVE_SHOP/OPERATOR)
- `notes` (text, nullable)
- `created_at` (datetime, auto)
- `updated_at` (datetime, auto)

**Computed Properties:**
- `tank_count` → Count of owned tanks (ForeignKey from Tank model)
- `order_count` → Count of orders (ForeignKey from ServiceOrder model)
- `last_order_date` → Most recent order date

**Optional Fields (EPOCH 1):**
- `address` (text - can defer to EPOCH 2 for delivery)
- `certification_number` (string - dive cert, defer to EPOCH 2)
- `emergency_contact` (string - defer to EPOCH 2)

### API Endpoints

**Required:**
- `POST /api/customers/` - Create customer
- `GET /api/customers/` - List customers (with search/filter)
- `GET /api/customers/{id}/` - Get customer details (with tanks & orders)
- `PATCH /api/customers/{id}/` - Update customer
- `DELETE /api/customers/{id}/` - Delete customer (prevent if has tanks/orders)

**Query Parameters:**
- `?search=<name_or_phone_or_email>`
- `?customer_type=INDIVIDUAL`
- `?has_tanks=true`

### UI Requirements

**Customer List View:**
- Table: Name, Type, Phone, Email, Tank Count, Last Order, Actions
- Search bar (searches name, phone, email simultaneously)
- Filter: Customer Type dropdown
- Add Customer button
- Pagination (20 per page)

**Customer Create/Edit Form:**
- Customer Type: Radio buttons (Individual / Dive Shop / Operator)
- Name: Text input (required)
- Company Name: Text input (show only if Dive Shop or Operator)
- Email: Text input with validation
- Phone: Text input with format normalization
- Notes: Textarea
- Save/Cancel buttons

**Customer Detail View:**
- Customer info card (name, type, contact)
- Owned Tanks section (table: Serial #, Size, Status, Last Service)
- Order History section (table: Order #, Date, Total, Status)
- Edit Customer button
- Back to List button

**Quick Add Modal (From Order Form):**
- Minimal fields: Name, Phone, Customer Type
- Save & Select button (closes modal, selects customer in order form)
- Cancel button

---

## Definition of Done

### Development Complete
- [ ] Customer model created with all required fields
- [ ] Database migration applied
- [ ] Customer CRUD API endpoints (5 endpoints)
- [ ] Customer list UI with search and filters
- [ ] Customer create/edit form with validation
- [ ] Customer detail view with tanks and orders
- [ ] Quick add modal for order workflow integration

### Testing Complete (>95% Coverage)
- [ ] Unit tests: Customer model validation (6 tests)
- [ ] Unit tests: Phone normalization (4 tests)
- [ ] API tests: CRUD operations (10 tests)
- [ ] API tests: Search functionality (name, phone, email) (8 tests)
- [ ] Integration tests: Customer with tanks and orders (5 tests)
- [ ] UI tests: Quick add from order form (3 tests)
- [ ] Edge case: Prevent delete if customer has tanks
- [ ] Edge case: Duplicate email validation

### Quality Assurance
- [ ] Can add 100 customers without performance issues
- [ ] Search returns results in <500ms
- [ ] Phone normalization handles all formats (US, international)
- [ ] Quick add modal works from order form
- [ ] Customer detail view loads tanks and orders efficiently (no N+1 queries)

### Documentation
- [ ] API endpoints documented
- [ ] Customer model schema documented
- [ ] User guide: "How to add a customer"
- [ ] User guide: "How to search customers"

---

## Test Scenarios

### Happy Path
1. **Add Individual Diver**
   - Click "Add Customer"
   - Select type "Individual"
   - Enter name "John Doe", phone "555-1234", email "john@example.com"
   - Save
   - Customer appears in list

2. **Add Dive Shop**
   - Click "Add Customer"
   - Select type "Dive Shop"
   - Enter name "Ocean Adventures", company name "Ocean Adventures LLC", phone "555-5678"
   - Save
   - Customer appears with company name displayed

3. **Search by Partial Name**
   - Enter "John" in search
   - All customers with "John" in name display ("John Doe", "Johnny Smith", "John's Dive Shop")

4. **Search by Phone**
   - Enter "555-1234" in search
   - Customer with phone "555-1234" or "+1-555-1234" displays

5. **View Customer with Tanks and Orders**
   - Click on "John Doe"
   - Detail view shows:
     - Contact info
     - 3 owned tanks (serial, size, status)
     - 5 recent orders (order #, date, total)

### Edge Cases
6. **Quick Add from Order Form**
   - On order creation, click "+ New Customer"
   - Modal opens
   - Enter "Jane Smith", "555-9999", type "Individual"
   - Click "Save & Select"
   - Modal closes, "Jane Smith" selected in order customer dropdown

7. **Prevent Delete with Dependencies**
   - Try to delete customer who owns tanks
   - System shows error: "Cannot delete customer with owned tanks. Reassign tanks first."
   - Customer not deleted

8. **Duplicate Email Validation**
   - Try to add customer with email "john@example.com" (already exists)
   - System shows error: "Email already in use"
   - Customer not created

9. **Phone Normalization**
   - Enter phone as "555-1234", saves as "555-1234"
   - Enter phone as "+1 (555) 123-4567", normalizes to "+15551234567"
   - Enter phone as "555.123.4567", normalizes to "5551234567"
   - All formats searchable by digits only

### Error Handling
10. **Missing Required Field**
    - Try to save customer without name
    - System shows error: "Name is required"
    - Form does not submit

---

## Dependencies

**Requires:**
- Django project initialized
- User authentication

**Blocks:**
- S-001: Tank Inventory Management (tanks need owners)
- S-002: Service Order Creation (orders need customers)

**Enables:**
- S-006: Customer Portal (EPOCH 2 - customers need login linked to account)

---

## Notes for Implementation

**Phone Normalization:**
```python
import re

def normalize_phone(phone):
    if not phone:
        return None
    # Remove all non-digit characters
    digits = re.sub(r'\D', '', phone)
    # Keep raw normalized format (can add country code logic later)
    return digits
```

**Business Rules:**
- Email is optional (some customers may not have email)
- Phone is optional (but recommended for contact)
- Name is required (minimum 2 characters)
- Company name required only for DIVE_SHOP and OPERATOR types
- Cannot delete customer if they have:
  - Owned tanks (must reassign tanks first)
  - Active orders (must complete/cancel orders first)

**Search Logic:**
- Search across name, email, phone simultaneously (OR condition)
- Partial match supported (case-insensitive)
- Phone search strips formatting before matching

**Future Enhancements (EPOCH 2+):**
- Customer address for delivery
- Dive certification tracking (c-card number, level, expiration)
- Emergency contact info
- Customer preferences (e.g., always Nitrox, delivery route preference)
- Customer notes history (timestamped technician notes)
- Customer loyalty/rewards tracking (EPOCH 4)

---

## Estimated Effort Breakdown

**Total: 3 hours**

- Model & Migration: 0.5 hours
- API Endpoints: 1 hour
- UI Components: 1 hour
- Testing: 0.5 hours

---

**Story Status**: Ready for SPEC approval
**Next Steps**: Awaiting client approval on EPOCH 1 scope
