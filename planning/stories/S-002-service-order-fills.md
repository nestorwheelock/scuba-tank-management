# S-002: Service Order Creation (Fills Only)

**Story Type**: User Story
**Priority**: Critical
**Estimate**: 5 hours
**Epoch**: EPOCH 1 - Core Operations (MVP)
**Status**: ⏳ Pending Approval

---

## User Story

**As a** scuba tank fill station technician
**I want to** create digital service orders for tank fills
**So that** I can eliminate paper tickets and track orders electronically

---

## Business Context

**Current Pain Point:**
- Orders managed via WhatsApp, phone calls, and paper tickets
- Easy to miss or lose orders
- No centralized order tracking
- Manual calculation of pricing

**Business Value:**
- Eliminate paper tickets completely
- Centralized order management (no more WhatsApp chaos)
- Automatic order tracking and history
- Accurate pricing every time
- Foundation for customer portal (EPOCH 2)

---

## Acceptance Criteria

### AC-1: Create Fill Order (Walk-In)
- [ ] When I create a new order, I select: tank (from inventory), service type (Air Fill or Nitrox 32%), customer
- [ ] When I select a tank, system auto-fills owner if assigned
- [ ] When I select service type, price displays automatically from price list
- [ ] When I save order, it gets unique order number (e.g., ORD-2025-00001)

### AC-2: Service Types (EPOCH 1 Scope)
- [ ] When I create order, I can select: "Air Fill" or "Nitrox 32% Fill"
- [ ] When I select "Air Fill", price is $X (from price list)
- [ ] When I select "Nitrox 32%", price is $Y (from price list)
- [ ] When I select service, tank certification status is validated (warn if expired)

### AC-3: Order Status Workflow
- [ ] When order is created, status is "Pending"
- [ ] When I start filling tank, I change status to "In Progress"
- [ ] When fill is complete, I change status to "Completed"
- [ ] When customer pays, I change status to "Paid" (payment tracking only - no processing in EPOCH 1)

### AC-4: Walk-In Order Processing
- [ ] When customer walks in, I can create order in <1 minute
- [ ] When customer has multiple tanks, I can add multiple line items to one order
- [ ] When order is saved, I can print order receipt/work ticket
- [ ] When order is completed, I can mark it paid and close the order

### AC-5: Order Search & History
- [ ] When I search by order number, order displays immediately
- [ ] When I search by customer name, all their orders display
- [ ] When I search by tank serial, orders for that tank display
- [ ] When I view order history, orders sorted by date (newest first)

### AC-6: Basic Reporting
- [ ] When I view "Today's Orders", all orders created today display
- [ ] When I view "Pending Orders", only orders with status "Pending" or "In Progress" display
- [ ] When I view "Completed Orders", only completed/paid orders display
- [ ] When I export daily report, CSV contains: order #, customer, tank, service, price, status

---

## Technical Requirements

### Data Model: ServiceOrder

**Required Fields:**
- `order_number` (unique, auto-generated: ORD-YYYY-NNNNN)
- `customer` (ForeignKey to Customer)
- `status` (enum: PENDING, IN_PROGRESS, COMPLETED, PAID)
- `order_type` (enum: WALK_IN, ADVANCE, DELIVERY - only WALK_IN in EPOCH 1)
- `subtotal` (decimal, calculated from line items)
- `notes` (text, nullable)
- `created_by` (ForeignKey to User - technician who created)
- `created_at` (datetime, auto)
- `updated_at` (datetime, auto)
- `completed_at` (datetime, nullable)
- `paid_at` (datetime, nullable)

### Data Model: ServiceOrderItem

**Required Fields:**
- `order` (ForeignKey to ServiceOrder)
- `tank` (ForeignKey to Tank)
- `service_type` (enum: AIR_FILL, NITROX_32_FILL)
- `price` (decimal, from price list)
- `notes` (text, nullable)

### Data Model: ServicePrice

**Required Fields (Pricing Configuration):**
- `service_type` (enum: AIR_FILL, NITROX_32_FILL, VISUAL_INSPECTION, HYDRO_TEST, etc.)
- `price` (decimal)
- `is_active` (boolean, default True)
- `effective_date` (date, when price takes effect)

### API Endpoints

**Required:**
- `POST /api/orders/` - Create order
- `GET /api/orders/` - List orders (with filters)
- `GET /api/orders/{id}/` - Get order details
- `PATCH /api/orders/{id}/` - Update order (status, notes)
- `POST /api/orders/{id}/complete/` - Mark order completed
- `POST /api/orders/{id}/mark-paid/` - Mark order paid
- `GET /api/orders/export/` - Export CSV (daily report)
- `GET /api/prices/` - Get current price list

**Query Parameters:**
- `?status=PENDING`
- `?customer=<customer_id>`
- `?created_date=2025-09-30`
- `?order_type=WALK_IN`

### UI Requirements

**Order Creation Form:**
- Customer select (autocomplete dropdown)
- Add line item: Tank (dropdown with serial #), Service Type (dropdown), Price (auto-fill, display)
- Multiple line items (+ Add Another Tank button)
- Notes field
- Total price display
- Submit button creates order with status=PENDING

**Order List View:**
- Table: Order #, Customer, Date, Total, Status, Actions
- Filters: Status, Date Range, Customer
- Search bar: Order # or Customer name
- Quick actions: View Details, Mark Completed, Mark Paid

**Order Detail View:**
- Order number, customer, date, status
- Line items table: Tank, Service, Price
- Total price
- Notes
- Action buttons: Update Status, Print Receipt, Edit (if Pending)
- Status timeline: Created → In Progress → Completed → Paid (with timestamps)

**Daily Dashboard (Quick View):**
- Today's Orders count
- Pending Orders list (top 10)
- Completed Today count
- Total Revenue Today (sum of paid orders)

---

## Definition of Done

### Development Complete
- [ ] ServiceOrder model with order number generation
- [ ] ServiceOrderItem model for line items
- [ ] ServicePrice model for price list
- [ ] Order CRUD API endpoints (7 endpoints)
- [ ] Price list API endpoint
- [ ] Order creation UI with multi-item support
- [ ] Order list UI with filters
- [ ] Order detail view with status updates
- [ ] Daily dashboard with summary stats

### Testing Complete (>95% Coverage)
- [ ] Unit tests: Order model validation (8 tests)
- [ ] Unit tests: Order number generation (unique, sequential)
- [ ] API tests: Create order with multiple items (12 tests)
- [ ] API tests: Status transitions (5 tests)
- [ ] API tests: Price lookup from price list (4 tests)
- [ ] Integration tests: Order workflow (create → complete → paid) (6 tests)
- [ ] UI tests: Multi-item order creation (4 tests)
- [ ] Edge case: Order with expired tank certification (warning displayed)

### Quality Assurance
- [ ] Can create order in <1 minute
- [ ] Multi-item orders (5+ tanks) work smoothly
- [ ] Order number is unique and sequential
- [ ] Price auto-fills correctly from price list
- [ ] Status workflow prevents invalid transitions (e.g., PAID → PENDING)
- [ ] Daily export contains all today's orders

### Documentation
- [ ] API endpoints documented
- [ ] Order status workflow diagram
- [ ] User guide: "How to create a walk-in order"
- [ ] User guide: "How to process daily orders"
- [ ] Price list configuration guide

---

## Test Scenarios

### Happy Path
1. **Create Single-Tank Walk-In Order**
   - Select customer "John Doe"
   - Add tank "A123456", service "Nitrox 32%"
   - Price auto-fills $20
   - Save order
   - Order number generated: ORD-2025-00001
   - Status: Pending

2. **Create Multi-Tank Order**
   - Select customer "Dive Shop XYZ"
   - Add 5 tanks with air fills
   - Total price calculates correctly (5 × $15 = $75)
   - Save order
   - Order created with 5 line items

3. **Complete Order Workflow**
   - Open order ORD-2025-00001
   - Mark "In Progress" → completed_at timestamp NULL
   - Mark "Completed" → completed_at timestamp set
   - Mark "Paid" → paid_at timestamp set
   - Order in final state

4. **Daily Operations**
   - View dashboard: 10 orders today, 3 pending, 7 completed
   - Export daily report: CSV downloads with all 10 orders
   - Search by customer: All orders for "John Doe" display

### Edge Cases
5. **Order with Expired Tank Certification**
   - Select tank with expired visual cert
   - System shows warning: "Tank visual expired, fill at risk"
   - Allow order creation with warning acknowledgment

6. **Price List Update**
   - Admin updates Nitrox price from $20 to $22
   - New orders use $22
   - Old orders keep $20 (historical accuracy)

7. **Status Transition Validation**
   - Try to mark "Paid" order as "Pending"
   - System prevents invalid transition
   - Error: "Cannot reopen paid order"

### Error Handling
8. **Missing Required Fields**
   - Try to create order without customer
   - System shows error: "Customer is required"
   - Order not created

9. **Duplicate Order Number (Race Condition)**
   - Two technicians create orders simultaneously
   - Order numbers are unique (database constraint)
   - No collisions

---

## Dependencies

**Requires:**
- S-001: Tank Inventory Management (completed first)
- Customer model (minimal: name, contact)
- User authentication (technician login)
- Price list pre-populated (admin task)

**Blocks:**
- S-004: Order Status Tracking (needs orders to track)
- S-006: Customer Portal (EPOCH 2 - customer-facing orders)

---

## Notes for Implementation

**Order Number Generation:**
```python
def generate_order_number():
    year = timezone.now().year
    last_order = ServiceOrder.objects.filter(
        order_number__startswith=f"ORD-{year}-"
    ).order_by('-order_number').first()

    if last_order:
        last_num = int(last_order.order_number.split('-')[-1])
        new_num = last_num + 1
    else:
        new_num = 1

    return f"ORD-{year}-{new_num:05d}"
```

**Business Rules:**
- Walk-in orders default to WALK_IN type
- Price pulled from ServicePrice where is_active=True and effective_date <= today
- Status transitions: PENDING → IN_PROGRESS → COMPLETED → PAID (linear, no skips)
- Cannot edit order after status is COMPLETED or PAID
- Order total is sum of all line items

**Price List (Initial Data):**
- Air Fill: $15
- Nitrox 32% Fill: $20
- Visual Inspection: $25 (EPOCH 2)
- Hydrostatic Test: $45 (EPOCH 2)

**Future Enhancements (EPOCH 2+):**
- Advance orders (schedule for future date)
- Delivery orders (EPOCH 3)
- Online payment integration (EPOCH 4)
- Discount codes
- Partial payments
- Order cancellation
- Order refunds

---

## Estimated Effort Breakdown

**Total: 5 hours**

- Models & Migrations: 1 hour
- API Endpoints: 1.5 hours
- UI Components: 2 hours
- Testing: 0.5 hours

---

**Story Status**: Ready for SPEC approval
**Next Steps**: Awaiting client approval on EPOCH 1 scope
