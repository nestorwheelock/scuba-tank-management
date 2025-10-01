# ACCEPTANCE TEST PLAN - EPOCH 1

**Project**: DiveTank Pro
**Version**: EPOCH 1 - Core Operations (MVP)
**Test Date**: [To be scheduled after development]
**Tester**: Shop Owner / Designated Staff
**Last Updated**: 2025-09-30

---

## PURPOSE

This acceptance test plan ensures EPOCH 1 delivers all promised functionality and meets business requirements BEFORE deployment to production.

**Test Approach:**
- Hands-on testing by actual users (shop staff)
- Real-world scenarios (not just happy path)
- Combination of guided walkthrough + free exploration
- All tests must PASS before shipping to production

---

## PRE-TEST SETUP

### Test Environment Preparation

**Required Before Testing:**
- [ ] Staging environment deployed (identical to production)
- [ ] Test database populated with realistic data:
  - 50 sample tanks (mix of rental, client-owned, operator-owned)
  - 20 sample customers (individuals, dive shops)
  - 10 historical orders (various statuses)
  - 2 user accounts: 1 Admin, 1 Technician
  - Price list configured (Air Fill: $15, Nitrox 32%: $20)
- [ ] Admin credentials provided to tester
- [ ] Technician credentials provided to tester
- [ ] Mobile device available for responsive testing (tablet/phone)
- [ ] Printed copy of this acceptance test plan

### Test Data Scenarios

**Tank Test Data:**
- Tank A: Rental, in shop, valid certifications
- Tank B: Client-owned (John Doe), in shop, visual expiring in 15 days
- Tank C: Client-owned (Jane Smith), with customer, hydro expired
- Tank D: Operator-owned, out for service, valid certifications
- Tank E: Rental, on delivery, valid certifications

**Customer Test Data:**
- John Doe (Individual, 3 owned tanks)
- Jane Smith (Individual, 1 owned tank)
- Ocean Adventures (Dive Shop, 5 owned tanks)
- Pro Divers Inc (Operator, 10 owned tanks)

**Order Test Data:**
- Order #1: Pending, single tank, air fill
- Order #2: In Progress, 3 tanks, nitrox fills
- Order #3: Completed today, 2 tanks, mixed fills
- Order #4: Paid, yesterday, single tank

---

## ACCEPTANCE TEST SCENARIOS

### TEST GROUP 1: Tank Inventory Management (S-001)

#### Test 1.1: Add New Tank
**User Story**: S-001 (Tank Inventory Management)
**Priority**: Critical
**Estimated Duration**: 3 minutes

**Steps:**
1. Log in as Admin
2. Navigate to Tank Inventory
3. Click "Add Tank"
4. Enter:
   - Serial Number: TEST-001
   - Size: 80 cu ft
   - Ownership: Client-Owned
   - Owner: John Doe (select from dropdown)
   - Location: In Shop
   - Last Visual Date: 30 days ago
   - Last Hydro Date: 1 year ago
5. Click Save

**Expected Results:**
- ✅ Tank saves successfully
- ✅ Tank appears in inventory list immediately
- ✅ Tank shows "Valid" for both visual and hydro status
- ✅ Tank is linked to John Doe

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 1.2: Search Tank by Serial Number
**Priority**: Critical
**Estimated Duration**: 1 minute

**Steps:**
1. In Tank Inventory, use search bar
2. Enter "TEST-001"
3. Press Enter or click Search

**Expected Results:**
- ✅ Search returns in <1 second
- ✅ Only Tank TEST-001 displays
- ✅ Tank details are correct

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 1.3: Filter Tanks by Ownership Type
**Priority**: High
**Estimated Duration**: 2 minutes

**Steps:**
1. In Tank Inventory, locate filter section
2. Select "Ownership Type: Rental"
3. Apply filter

**Expected Results:**
- ✅ Only rental tanks display
- ✅ Count matches expected rental tank count
- ✅ Can clear filter to see all tanks again

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 1.4: Identify Expiring Certifications
**Priority**: Critical
**Estimated Duration**: 2 minutes

**Steps:**
1. In Tank Inventory, apply filter: "Certification Status: Expiring Soon"
2. Review tanks displayed

**Expected Results:**
- ✅ Tanks with certifications expiring in 30 days display
- ✅ Tank B (visual expiring in 15 days) is included
- ✅ Visual indicator (color, icon, badge) shows expiring status
- ✅ Expired tanks (Tank C) show "Expired" status, not "Expiring Soon"

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 1.5: Bulk Update Tank Location
**Priority**: Medium
**Estimated Duration**: 3 minutes

**Steps:**
1. In Tank Inventory, select 5 tanks (checkboxes)
2. Click "Bulk Update Location"
3. Select "With Customer"
4. Click Apply

**Expected Results:**
- ✅ All 5 tanks update to "With Customer"
- ✅ Update completes in <5 seconds
- ✅ Tanks display new location immediately
- ✅ Audit log records the change (check in Admin > Audit Log)

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 1.6: Export Tank Inventory
**Priority**: Low
**Estimated Duration**: 2 minutes

**Steps:**
1. In Tank Inventory, click "Export CSV"
2. Download file
3. Open in Excel/Google Sheets

**Expected Results:**
- ✅ CSV file downloads successfully
- ✅ File contains all visible tanks (respects filters if applied)
- ✅ Columns include: Serial, Size, Owner, Location, Visual Status, Hydro Status
- ✅ Data is accurate

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

### TEST GROUP 2: Service Order Creation (S-002)

#### Test 2.1: Create Walk-In Order (Single Tank)
**User Story**: S-002 (Service Order - Fills Only)
**Priority**: Critical
**Estimated Duration**: 3 minutes

**Steps:**
1. Log in as Technician
2. Navigate to Orders
3. Click "Create Order"
4. Select Customer: John Doe
5. Add Line Item:
   - Tank: TEST-001 (from dropdown)
   - Service: Nitrox 32% Fill
6. Verify price auto-fills: $20
7. Add notes: "Customer requested 3000 psi"
8. Click Save

**Expected Results:**
- ✅ Order saves successfully
- ✅ Order number auto-generated (e.g., ORD-2025-00005)
- ✅ Order status is "Pending"
- ✅ Price is $20
- ✅ Order appears in Pending queue on dashboard
- ✅ Total time to create order: <2 minutes

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 2.2: Create Multi-Tank Order
**Priority**: Critical
**Estimated Duration**: 4 minutes

**Steps:**
1. Click "Create Order"
2. Select Customer: Ocean Adventures
3. Add 3 line items:
   - Tank A: Air Fill ($15)
   - Tank B: Air Fill ($15)
   - Tank C: Nitrox 32% ($20)
4. Verify total price: $50
5. Click Save

**Expected Results:**
- ✅ Order saves with 3 line items
- ✅ Total price calculates correctly: $50
- ✅ All 3 tanks linked to order
- ✅ Order appears in Pending queue

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 2.3: Order Status Workflow
**Priority**: Critical
**Estimated Duration**: 5 minutes

**Steps:**
1. Open pending order (from Test 2.1)
2. Click "Start" (or change status to "In Progress")
3. Verify status changes
4. Wait 1 minute
5. Click "Complete"
6. Verify completed_at timestamp is set
7. Click "Mark Paid"
8. Verify paid_at timestamp is set

**Expected Results:**
- ✅ Status transitions: Pending → In Progress → Completed → Paid
- ✅ Timestamps record each transition
- ✅ Dashboard updates immediately (within 30 seconds if using polling)
- ✅ Order moves between dashboard queues correctly

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 2.4: Order with Expired Tank Certification
**Priority**: High
**Estimated Duration**: 3 minutes

**Steps:**
1. Create new order
2. Select Customer: Jane Smith
3. Select Tank C (hydro expired)
4. Select Service: Air Fill
5. Attempt to save

**Expected Results:**
- ✅ Warning message displays: "Tank hydro expired, fill at risk"
- ✅ Order can still be created (with warning acknowledgment)
- ✅ Warning is visible on order details

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 2.5: Search Orders
**Priority**: High
**Estimated Duration**: 3 minutes

**Steps:**
1. Navigate to Orders list
2. Test search by order number (enter order # from Test 2.1)
3. Test search by customer name (enter "John Doe")
4. Test search by tank serial (enter "TEST-001")

**Expected Results:**
- ✅ Search by order number returns exact match
- ✅ Search by customer returns all customer's orders
- ✅ Search by tank serial returns orders containing that tank
- ✅ Search completes in <1 second

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

### TEST GROUP 3: Customer Database (S-003)

#### Test 3.1: Add Individual Customer
**User Story**: S-003 (Customer Database)
**Priority**: High
**Estimated Duration**: 2 minutes

**Steps:**
1. Navigate to Customers
2. Click "Add Customer"
3. Enter:
   - Customer Type: Individual
   - Name: Test Customer
   - Phone: 555-1234
   - Email: test@example.com
4. Click Save

**Expected Results:**
- ✅ Customer saves successfully
- ✅ Customer appears in customer list
- ✅ Phone normalization works (display format consistent)

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 3.2: Add Dive Shop Customer
**Priority**: Medium
**Estimated Duration**: 2 minutes

**Steps:**
1. Click "Add Customer"
2. Select Customer Type: Dive Shop
3. Enter:
   - Name: Test Dive Shop
   - Company Name: Test Dive Shop LLC
   - Phone: 555-5678
   - Email: shop@example.com
4. Click Save

**Expected Results:**
- ✅ Company Name field appears when Dive Shop selected
- ✅ Customer saves with company name
- ✅ Customer list displays company name

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 3.3: Search Customers
**Priority**: High
**Estimated Duration**: 2 minutes

**Steps:**
1. In customer list, search "Test"
2. Verify both "Test Customer" and "Test Dive Shop" appear
3. Search "555-1234"
4. Verify "Test Customer" appears

**Expected Results:**
- ✅ Partial name search works
- ✅ Phone search works (with/without formatting)
- ✅ Email search works

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 3.4: View Customer Details (Tanks & Orders)
**Priority**: High
**Estimated Duration**: 3 minutes

**Steps:**
1. Click on "John Doe" customer
2. View customer detail page

**Expected Results:**
- ✅ Contact info displays correctly
- ✅ Owned tanks section shows John's 3 tanks
- ✅ Order history section shows John's orders
- ✅ No N+1 query issues (page loads fast)

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 3.5: Quick Add Customer from Order Form
**Priority**: Medium
**Estimated Duration**: 3 minutes

**Steps:**
1. Start creating new order
2. Click "+ New Customer" (from customer dropdown)
3. Quick add modal opens
4. Enter: Name "Quick Test", Phone "555-9999", Type "Individual"
5. Click "Save & Select"

**Expected Results:**
- ✅ Modal opens without leaving order form
- ✅ Customer saves successfully
- ✅ Modal closes
- ✅ "Quick Test" is auto-selected in order customer dropdown
- ✅ Can continue creating order without interruption

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

### TEST GROUP 4: Dashboard & Reporting (S-004)

#### Test 4.1: Dashboard Overview
**User Story**: S-004 (Order Status Dashboard)
**Priority**: Critical
**Estimated Duration**: 2 minutes

**Steps:**
1. Log in as Technician
2. View dashboard (should be default landing page)
3. Observe status cards and metrics

**Expected Results:**
- ✅ Pending count matches pending orders
- ✅ In Progress count matches in-progress orders
- ✅ Completed Today count matches today's completions
- ✅ Total Revenue Today sums paid orders from today
- ✅ Avg Order Value calculates correctly
- ✅ Dashboard loads in <1 second

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 4.2: Pending Order Queue
**Priority**: Critical
**Estimated Duration**: 3 minutes

**Steps:**
1. Click "Pending" status card
2. View pending orders list
3. Click "Start" on oldest pending order

**Expected Results:**
- ✅ Pending orders sorted by creation time (oldest first)
- ✅ Clicking "Start" changes status to "In Progress"
- ✅ Order moves from Pending to In Progress list
- ✅ Counts update: Pending -1, In Progress +1
- ✅ Update happens in <30 seconds (polling interval)

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 4.3: In-Progress Order Warning (>30 min)
**Priority**: Medium
**Estimated Duration**: 2 minutes (if test order already >30 min old)

**Steps:**
1. View In Progress orders list
2. Locate order that's been in progress >30 minutes (or use test order started 31 min ago)

**Expected Results:**
- ✅ Order row is highlighted (yellow/orange)
- ✅ Time elapsed shows "31 min" (or actual time)
- ✅ Visual indicator (icon, badge) shows warning

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 4.4: Filter Orders by Date
**Priority**: High
**Estimated Duration**: 2 minutes

**Steps:**
1. On dashboard, apply date filter: "Yesterday"
2. Verify orders displayed are from yesterday
3. Apply date filter: "This Week"
4. Verify orders displayed are from this week

**Expected Results:**
- ✅ Date filter works correctly
- ✅ Metrics recalculate for selected period
- ✅ Order counts match filtered date range

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 4.5: Export Daily Report
**Priority**: Medium
**Estimated Duration**: 2 minutes

**Steps:**
1. On dashboard, click "Export Daily Report"
2. Download CSV
3. Open file

**Expected Results:**
- ✅ CSV downloads successfully
- ✅ Contains today's orders only (respects date filter)
- ✅ Columns: Order #, Customer, Tank, Service, Price, Status
- ✅ Data is accurate

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 4.6: Dashboard Auto-Refresh
**Priority**: Medium
**Estimated Duration**: 3 minutes

**Steps:**
1. Open dashboard in Browser Tab 1
2. Open new tab (Browser Tab 2), log in as different technician
3. In Tab 2, create new order (status: Pending)
4. Switch to Tab 1, wait up to 30 seconds

**Expected Results:**
- ✅ Tab 1 dashboard updates automatically (within 30 seconds)
- ✅ Pending count increases by 1
- ✅ New order appears in pending list
- ✅ No page refresh required

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

### TEST GROUP 5: Admin & Configuration (S-005)

#### Test 5.1: Update Price List
**User Story**: S-005 (Admin Configuration)
**Priority**: Critical
**Estimated Duration**: 4 minutes

**Steps:**
1. Log in as Admin
2. Navigate to Admin > Settings > Price List
3. Click "Edit" on "Nitrox 32% Fill"
4. Change price from $20 to $22
5. Set effective date to today
6. Click Save
7. Create new order with Nitrox 32% service

**Expected Results:**
- ✅ Price updates successfully
- ✅ New orders use $22
- ✅ Old orders keep $20 (historical accuracy)
- ✅ Price history shows change with timestamp and admin who made it
- ✅ Audit log records: "Admin changed Nitrox 32% price from $20 to $22"

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 5.2: Add New Technician User
**Priority**: High
**Estimated Duration**: 3 minutes

**Steps:**
1. As Admin, navigate to Admin > Settings > Users
2. Click "Add User"
3. Enter:
   - Username: test_tech
   - Password: SecurePass123
   - Email: tech@example.com
   - First Name: Test
   - Last Name: Technician
   - Role: Technician
4. Click Save
5. Log out
6. Log in as test_tech

**Expected Results:**
- ✅ User saves successfully
- ✅ User appears in user list
- ✅ Can log in with new credentials
- ✅ Technician role applied (no access to admin settings)
- ✅ Audit log records: "Admin created user test_tech (Technician)"

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 5.3: Technician Permission Denial
**Priority**: Critical
**Estimated Duration**: 2 minutes

**Steps:**
1. Log in as Technician (test_tech or existing tech)
2. Attempt to navigate to Admin > Settings (use URL if link hidden)

**Expected Results:**
- ✅ Access denied (redirect to dashboard or 403 error page)
- ✅ Error message: "Admin access required"
- ✅ Audit log records: "test_tech attempted to access admin settings (denied)"

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 5.4: Update Shop Configuration
**Priority**: Medium
**Estimated Duration**: 3 minutes

**Steps:**
1. Log in as Admin
2. Navigate to Admin > Settings > Shop Configuration
3. Update:
   - Shop Name: "DiveTank Pro Test"
   - Delivery Truck Capacity: 35 (from 30)
4. Click Save

**Expected Results:**
- ✅ Configuration saves successfully
- ✅ Changes apply immediately (no restart required)
- ✅ Delivery capacity of 35 is noted (will be enforced in EPOCH 3)
- ✅ Audit log records configuration change

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 5.5: View Audit Log
**Priority**: High
**Estimated Duration**: 3 minutes

**Steps:**
1. As Admin, navigate to Admin > Settings > Audit Log
2. Apply filter: Action Type = "PRICE_CHANGE"
3. Locate price change from Test 5.1

**Expected Results:**
- ✅ Audit log displays all recorded actions
- ✅ Filter by action type works
- ✅ Price change entry shows: Timestamp, User (Admin), Action (PRICE_CHANGE), Changes ({"price": {"old": "$20", "new": "$22"}})
- ✅ Can export audit log to CSV

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 5.6: Deactivate User
**Priority**: Medium
**Estimated Duration**: 3 minutes

**Steps:**
1. As Admin, navigate to Admin > Settings > Users
2. Click "Deactivate" on test_tech user
3. Confirm deactivation
4. Log out
5. Attempt to log in as test_tech

**Expected Results:**
- ✅ User is deactivated (is_active = False)
- ✅ Cannot log in (error: "Account deactivated")
- ✅ User still appears in user list (soft delete)
- ✅ Historical data (orders created by test_tech) remains
- ✅ Audit log records deactivation

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

### TEST GROUP 6: Responsive Design & Performance

#### Test 6.1: Mobile Tablet Responsiveness
**Priority**: High
**Estimated Duration**: 5 minutes

**Steps:**
1. Open app on tablet (or resize browser to tablet size: 768px width)
2. Navigate through: Dashboard, Orders, Tanks, Customers
3. Create an order on tablet
4. Repeat on mobile phone (or resize to 375px width)

**Expected Results:**
- ✅ All pages are readable (no horizontal scroll)
- ✅ Navigation menu adapts (hamburger menu on mobile)
- ✅ Tables are responsive (stack columns or horizontal scroll)
- ✅ Forms are usable (large touch targets, proper input types)
- ✅ Can complete full order workflow on mobile

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 6.2: Performance with 100+ Tanks
**Priority**: Medium
**Estimated Duration**: 3 minutes

**Steps:**
1. Navigate to Tank Inventory (should have 100+ tanks from setup)
2. Scroll through list
3. Apply filters
4. Search for tank

**Expected Results:**
- ✅ Page loads in <2 seconds
- ✅ Scrolling is smooth (60fps, no lag)
- ✅ Search returns results in <500ms
- ✅ Pagination or infinite scroll works (no loading 100+ tanks at once)

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 6.3: Performance with 50+ Orders
**Priority**: Medium
**Estimated Duration**: 2 minutes

**Steps:**
1. Navigate to Orders (or Dashboard)
2. View order list with 50+ orders
3. Apply filters
4. Create new order

**Expected Results:**
- ✅ Order list loads in <1 second
- ✅ Dashboard stats calculate in <500ms
- ✅ Order creation form is responsive
- ✅ No noticeable lag

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

### TEST GROUP 7: Edge Cases & Error Handling

#### Test 7.1: Duplicate Tank Serial Number
**Priority**: High
**Estimated Duration**: 2 minutes

**Steps:**
1. Attempt to add new tank with serial number "TEST-001" (already exists from Test 1.1)
2. Try to save

**Expected Results:**
- ✅ Validation error displays: "Serial number already exists"
- ✅ Tank is not created
- ✅ Form shows error near serial number field

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 7.2: Missing Required Fields (Order)
**Priority**: Medium
**Estimated Duration**: 2 minutes

**Steps:**
1. Start creating new order
2. Leave customer field empty
3. Try to save

**Expected Results:**
- ✅ Validation error: "Customer is required"
- ✅ Order is not created
- ✅ Form highlights missing field

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 7.3: Invalid Date (Future Certification Date)
**Priority**: Medium
**Estimated Duration**: 2 minutes

**Steps:**
1. Add new tank
2. Enter last visual date as tomorrow (future date)
3. Try to save

**Expected Results:**
- ✅ Validation error: "Date cannot be in future"
- ✅ Tank is not created

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 7.4: Delete Customer with Dependencies
**Priority**: Medium
**Estimated Duration**: 2 minutes

**Steps:**
1. Attempt to delete "John Doe" customer (has owned tanks and orders)
2. Confirm deletion

**Expected Results:**
- ✅ Error message: "Cannot delete customer with owned tanks or active orders"
- ✅ Customer is not deleted
- ✅ Suggests reassigning tanks first

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

#### Test 7.5: Logout and Session Timeout
**Priority**: Low
**Estimated Duration**: 3 minutes

**Steps:**
1. Log in as Technician
2. Perform some actions (create order, view tanks)
3. Click "Logout"
4. Verify logged out (redirect to login page)
5. Use browser back button
6. Attempt to access protected page

**Expected Results:**
- ✅ Logout redirects to login page
- ✅ Session is destroyed
- ✅ Cannot access protected pages after logout (redirect to login)
- ✅ No data leakage via browser cache

**Actual Result**: [ PASS / FAIL ]
**Notes**: ___________________________________

---

## FREE EXPLORATION TESTING

**Duration**: 20 minutes
**Tester**: Shop Owner or Lead Technician

**Instructions:**
1. Use the system freely as you would in daily operations
2. Try to "break" things (unusual workflows, rapid clicks, etc.)
3. Note anything that feels confusing, slow, or broken

**Areas to Explore:**
- Create multiple orders rapidly
- Search and filter combinations
- Navigate between pages frequently
- Use browser back/forward buttons
- Resize browser window while using app
- Copy/paste data into forms
- Try edge cases (empty search, select all tanks, etc.)

**Findings:**
___________________________________________
___________________________________________
___________________________________________
___________________________________________

---

## ACCEPTANCE CRITERIA SUMMARY

**EPOCH 1 Must-Pass Checklist:**

### Tank Management (S-001)
- [ ] Can add, view, update, delete tanks
- [ ] Tank ownership tracking works
- [ ] Certification status calculated correctly
- [ ] Expiration alerts work (30-day warning)
- [ ] Search and filter tanks work
- [ ] Bulk operations work

### Service Orders (S-002)
- [ ] Can create walk-in orders (air, nitrox)
- [ ] Multi-tank orders work
- [ ] Order status workflow works (Pending → In Progress → Completed → Paid)
- [ ] Pricing auto-fills from price list
- [ ] Order search works
- [ ] Daily export works

### Customer Database (S-003)
- [ ] Can add, view, update customers
- [ ] Customer types work (Individual, Dive Shop, Operator)
- [ ] Customer search works (name, phone, email)
- [ ] Customer details show tanks and orders
- [ ] Quick add from order form works

### Dashboard (S-004)
- [ ] Dashboard shows real-time stats
- [ ] Order queues work (Pending, In Progress, Completed)
- [ ] Metrics calculate correctly
- [ ] Auto-refresh works (30-second polling)
- [ ] Filters and date range work
- [ ] Quick actions work

### Admin & Config (S-005)
- [ ] Price list management works
- [ ] User management works (add, deactivate)
- [ ] Role-based permissions work (Admin vs Technician)
- [ ] Shop configuration works
- [ ] Audit log captures all actions
- [ ] Audit log export works

### Performance & Quality
- [ ] All pages load in <2 seconds
- [ ] API responses in <500ms
- [ ] Mobile responsive (tablet, phone)
- [ ] No critical bugs
- [ ] Error messages are clear
- [ ] Validation prevents bad data

---

## FINAL ACCEPTANCE DECISION

**Test Summary:**
- Total Tests: 50+
- Tests Passed: _____
- Tests Failed: _____
- Critical Failures: _____

### Issues Found (If Any)

**Critical (Must Fix Before Ship):**
1. ___________________________________
2. ___________________________________

**Major (Should Fix Before Ship):**
1. ___________________________________
2. ___________________________________

**Minor (Can Fix in EPOCH 2):**
1. ___________________________________
2. ___________________________________

### Client Decision

**[ ] APPROVED FOR PRODUCTION**
- All critical tests passed
- System meets business requirements
- Ready to replace paper-based workflow
- Ship immediately

**[ ] APPROVED WITH KNOWN ISSUES**
- Critical tests passed
- Minor issues documented (will fix in EPOCH 2)
- System is usable, benefits outweigh issues
- Ship with documented limitations

**[ ] CONDITIONAL APPROVAL**
- Fix critical issues listed above
- Re-test specific scenarios
- No full re-test needed
- Ship after fixes confirmed

**[ ] FIX AND RE-TEST**
- Critical issues found
- Fix required before production use
- Schedule new acceptance test after fixes

**[ ] REJECTED**
- System does not meet requirements
- Major rework needed
- Return to BUILD or SPEC phase

---

## CLIENT SIGN-OFF

**I have tested DiveTank Pro EPOCH 1 and:**

- [ ] I understand what was tested
- [ ] I have explored the system freely
- [ ] I have documented all issues found
- [ ] I have made a decision (above)

**Client Name**: ___________________________
**Client Signature**: ______________________
**Date**: __________________________________

**Developer Acknowledgment:**

- [ ] I have witnessed the acceptance test
- [ ] I have documented all feedback
- [ ] I agree with the decision
- [ ] I commit to fixing approved issues

**Developer Name**: ________________________
**Developer Signature**: ___________________
**Date**: __________________________________

---

**Document Control:**
- Version: 1.0
- Last Updated: 2025-09-30
- Next Review: During acceptance test session
- Status: READY FOR TESTING (after development complete)
