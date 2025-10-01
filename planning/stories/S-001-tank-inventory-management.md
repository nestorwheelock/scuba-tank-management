# S-001: Tank Inventory Management

**Story Type**: User Story
**Priority**: Critical
**Estimate**: 6 hours
**Epoch**: EPOCH 1 - Core Operations (MVP)
**Status**: ⏳ Pending Approval

---

## User Story

**As a** scuba tank fill station technician
**I want to** manage a digital inventory of 100-200 scuba tanks
**So that** I can track tank ownership, location, and certification status without paper logs

---

## Business Context

**Current Pain Point:**
- Technicians track 100-200 tanks using paper logs and memory
- Easy to lose track of which tanks belong to whom
- Manual checking of certification dates is error-prone
- Cannot quickly find available tanks for service

**Business Value:**
- Eliminate paper tank logs
- Instant tank lookups by serial number, owner, or status
- Real-time inventory visibility
- Foundation for all other services (fills, inspections)

---

## Acceptance Criteria

### AC-1: Tank CRUD Operations
- [ ] When I add a new tank, I can enter: serial number, size (cu ft), ownership type, owner name, certification dates (visual, hydro)
- [ ] When I search by serial number, the tank displays immediately (<1 second)
- [ ] When I update tank information, changes save and reflect instantly
- [ ] When I delete a tank (rare), system asks for confirmation and removes it

### AC-2: Ownership Tracking
- [ ] When I add a tank, I can select ownership type: Rental, Client-Owned, Operator-Owned
- [ ] When I filter by ownership type, only tanks of that type display
- [ ] When I assign a tank to a customer, the owner field links to customer record
- [ ] When viewing a customer, I can see all their tanks

### AC-3: Tank Location & Status
- [ ] When I add a tank, I can set current location: In Shop, Out for Service, With Customer, On Delivery
- [ ] When I filter by status, tanks group accordingly
- [ ] When I update tank location, timestamp records the change
- [ ] When viewing tank history, I can see location changes over time

### AC-4: Certification Status
- [ ] When I add a tank, I enter last visual inspection date and last hydro date
- [ ] When viewing tank list, certification status displays: Valid, Expiring Soon (30 days), Expired
- [ ] When a tank visual cert is >1 year old, it shows as "Visual Expired"
- [ ] When a tank hydro cert is >5 years old, it shows as "Hydro Expired"

### AC-5: Tank Search & Filter
- [ ] When I search by serial number, exact matches appear first
- [ ] When I search by owner name, partial matches work (e.g., "John" finds "John Smith")
- [ ] When I apply multiple filters (owner + status), results match ALL criteria
- [ ] When I clear filters, full inventory displays

### AC-6: Bulk Operations
- [ ] When I select multiple tanks (checkbox), I can bulk update location
- [ ] When I export tank list, CSV file downloads with all visible tanks
- [ ] When I import tanks via CSV, system validates and creates records

---

## Technical Requirements

### Data Model: Tank

**Required Fields:**
- `serial_number` (unique, string, indexed)
- `size_cuft` (integer: 40, 63, 80, 100, etc.)
- `ownership_type` (enum: RENTAL, CLIENT_OWNED, OPERATOR_OWNED)
- `owner` (ForeignKey to Customer, nullable for rentals)
- `current_location` (enum: IN_SHOP, OUT_FOR_SERVICE, WITH_CUSTOMER, ON_DELIVERY)
- `last_visual_date` (date, nullable)
- `last_hydro_date` (date, nullable)
- `notes` (text, nullable)
- `created_at` (datetime, auto)
- `updated_at` (datetime, auto)

**Optional Fields (EPOCH 1):**
- `valve_type` (enum: DIN, YOKE - can defer to EPOCH 2)
- `material` (enum: STEEL, ALUMINUM - can defer)

**Computed Properties:**
- `visual_status` → Valid / Expiring Soon / Expired (based on last_visual_date)
- `hydro_status` → Valid / Expiring Soon / Expired (based on last_hydro_date)
- `is_available_for_service` → True if IN_SHOP and certifications valid

### API Endpoints

**Required:**
- `POST /api/tanks/` - Create tank
- `GET /api/tanks/` - List tanks (with filters)
- `GET /api/tanks/{id}/` - Get tank details
- `PATCH /api/tanks/{id}/` - Update tank
- `DELETE /api/tanks/{id}/` - Delete tank (soft delete preferred)
- `POST /api/tanks/bulk-update/` - Bulk location update
- `GET /api/tanks/export/` - Export CSV

**Query Parameters:**
- `?ownership_type=RENTAL`
- `?owner=<customer_id>`
- `?status=IN_SHOP`
- `?certification_status=EXPIRING_SOON`
- `?search=<serial_or_owner>`

### UI Requirements

**Tank List View:**
- Table with columns: Serial #, Size, Owner, Location, Visual Status, Hydro Status, Actions
- Filters sidebar: Ownership, Location, Certification Status
- Search bar at top
- Bulk select checkboxes
- Export CSV button
- Add Tank button

**Tank Detail/Edit Form:**
- All fields editable
- Owner dropdown (autocomplete from customer list)
- Date pickers for visual/hydro dates
- Save/Cancel buttons
- Validation: serial number unique, dates not in future

**Tank Creation Form:**
- Same as edit form
- Pre-populate ownership_type dropdown
- Auto-focus on serial number field

---

## Definition of Done

### Development Complete
- [ ] Tank model created with all required fields
- [ ] Database migration applied
- [ ] Tank CRUD API endpoints implemented (6 endpoints)
- [ ] Tank list UI with filters and search
- [ ] Tank create/edit form with validation
- [ ] Bulk update functionality working

### Testing Complete (>95% Coverage)
- [ ] Unit tests: Tank model validation (10 tests)
- [ ] API tests: All CRUD operations (15 tests)
- [ ] Integration tests: Filter/search logic (8 tests)
- [ ] UI tests: Form validation (5 tests)
- [ ] Edge case: Duplicate serial numbers rejected
- [ ] Edge case: Invalid dates rejected
- [ ] Edge case: Bulk update with 100 tanks succeeds

### Quality Assurance
- [ ] Can add 100 tanks without performance issues
- [ ] Search returns results in <500ms
- [ ] Certification status calculation is accurate
- [ ] Export CSV contains all filtered tanks
- [ ] UI is responsive on tablet/mobile

### Documentation
- [ ] API endpoints documented in OpenAPI spec
- [ ] Tank model schema documented
- [ ] User guide: "How to add a tank"
- [ ] User guide: "How to search tanks"

---

## Test Scenarios

### Happy Path
1. **Add New Tank**
   - Enter serial "A123456", size 80, owner "John Doe", dates valid
   - Tank saves successfully
   - Tank appears in inventory list

2. **Search Tank**
   - Search "A123456"
   - Tank appears immediately
   - Click to view details

3. **Update Tank Location**
   - Change location to "With Customer"
   - Save
   - Tank list reflects new location

4. **Filter Expiring Tanks**
   - Apply filter: "Expiring Soon"
   - Only tanks with certs expiring in 30 days show
   - Count matches filtered results

### Edge Cases
5. **Duplicate Serial Number**
   - Try to add tank with existing serial
   - System shows error: "Serial number already exists"
   - Tank not created

6. **Invalid Date**
   - Enter future date for visual inspection
   - System shows error: "Date cannot be in future"
   - Form does not submit

7. **Bulk Update 100 Tanks**
   - Select 100 tanks
   - Change location to "In Shop"
   - All 100 tanks update successfully in <5 seconds

### Error Handling
8. **Delete Tank with Active Orders**
   - Try to delete tank that has pending fill order (EPOCH 2 feature)
   - System shows error: "Cannot delete tank with active orders"
   - Tank not deleted (deferred to EPOCH 2 when orders exist)

---

## Dependencies

**Requires:**
- Django project initialized
- PostgreSQL database configured
- Customer model (minimal: name, contact)
- User authentication (admin/technician login)

**Blocks:**
- S-002: Service Order Creation (needs tank inventory)
- S-004: Order Status Tracking (needs tank reference)

---

## Notes for Implementation

**Performance Considerations:**
- Index `serial_number` for fast lookups
- Index `owner` for filtered queries
- Use `select_related('owner')` to avoid N+1 queries
- Paginate tank list (50 per page)

**Business Rules:**
- Visual inspection valid for 1 year (365 days)
- Hydrostatic test valid for 5 years (1825 days)
- "Expiring Soon" threshold: 30 days before expiration
- Soft delete preferred (set `deleted_at` instead of hard delete)

**Future Enhancements (EPOCH 2+):**
- Tank valve type tracking
- Tank material tracking (steel/aluminum)
- Tank weight tracking (empty/full)
- Tank pressure tracking (3000psi, 3442psi, etc.)
- Tank manufacturer/model
- Photo upload for tank condition

---

## Estimated Effort Breakdown

**Total: 6 hours**

- Models & Migrations: 1 hour
- API Endpoints: 2 hours
- UI Components: 2 hours
- Testing: 1 hour

---

**Story Status**: Ready for SPEC approval
**Next Steps**: Awaiting client approval on EPOCH 1 scope
