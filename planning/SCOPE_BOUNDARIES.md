# SCOPE BOUNDARIES

**Project**: DiveTank Pro
**Version**: Multi-Epoch Roadmap
**Last Updated**: 2025-09-30

---

## PURPOSE

This document clearly defines what IS and IS NOT included in each epoch to prevent scope creep, manage expectations, and ensure focused development.

---

## 🎯 EPOCH 1: CORE OPERATIONS (MVP)
**Estimate**: 20 hours | **Timeline**: 4-6 weeks

### ✅ IN SCOPE

**Tank Management:**
- ✅ Tank inventory CRUD (Create, Read, Update, Delete)
- ✅ Tank ownership tracking (Rental, Client-Owned, Operator-Owned)
- ✅ Tank location tracking (In Shop, Out for Service, With Customer, On Delivery)
- ✅ Tank certification status (Visual annual, Hydro 5-year)
- ✅ Tank search and filtering (serial, owner, status)
- ✅ Certification expiration alerts (30-day warning)
- ✅ Tank bulk operations (location update)
- ✅ Tank export (CSV)

**Service Orders:**
- ✅ Walk-in order creation (staff-initiated only)
- ✅ Service types: Air Fill, Nitrox 32% Fill ONLY
- ✅ Multi-tank orders (multiple line items)
- ✅ Order status workflow (Pending → In Progress → Completed → Paid)
- ✅ Order search (order #, customer, tank, date)
- ✅ Price list (configurable by admin)
- ✅ Invoice generation (manual payment tracking)
- ✅ Daily order export (CSV)

**Customer Management:**
- ✅ Customer database (Individual, Dive Shop, Operator)
- ✅ Customer CRUD operations
- ✅ Customer search (name, phone, email)
- ✅ Customer-tank relationship (ownership linking)
- ✅ Customer order history
- ✅ Quick customer add (from order form)

**Dashboard & Reporting:**
- ✅ Real-time order status dashboard
- ✅ Pending/In-Progress/Completed order queues
- ✅ Daily metrics (order count, revenue, avg order value)
- ✅ Order filtering (date, customer, service type)
- ✅ Quick actions (Start, Complete, Mark Paid)
- ✅ Dashboard auto-refresh (30-second polling)

**Admin & Configuration:**
- ✅ User management (Admin, Technician roles)
- ✅ Role-based permissions (RBAC)
- ✅ Price list management (update prices)
- ✅ Shop configuration (name, contact, delivery capacity, timezone)
- ✅ Audit logging (all user actions)
- ✅ Audit log filtering and export

**Security & Access:**
- ✅ User authentication (login/logout)
- ✅ Token-based API auth
- ✅ HTTPS/SSL encryption
- ✅ CSRF protection
- ✅ Password hashing (bcrypt)
- ✅ Rate limiting (login, API)

**Testing & Quality:**
- ✅ >95% test coverage (unit, integration)
- ✅ API documentation (OpenAPI/Swagger)
- ✅ User guides (tank, order, customer, admin)

### ❌ OUT OF SCOPE (Deferred to Later Epochs)

**NOT in EPOCH 1:**
- ❌ Customer portal (customer self-service) → EPOCH 2
- ❌ Customer online ordering → EPOCH 2
- ❌ Customer registration → EPOCH 2
- ❌ Email/SMS notifications → EPOCH 2
- ❌ Visual inspection service → EPOCH 2
- ❌ Hydrostatic testing service → EPOCH 2
- ❌ Inspector certification tracking → EPOCH 2
- ❌ Delivery scheduling → EPOCH 3
- ❌ Delivery route batching → EPOCH 3
- ❌ Mobile driver interface → EPOCH 3
- ❌ Gas blend logging → EPOCH 3
- ❌ Equipment maintenance tracking → EPOCH 3
- ❌ Compressor hour meter → EPOCH 3
- ❌ Air quality test scheduling → EPOCH 3
- ❌ Online payment processing → EPOCH 4
- ❌ Automated invoicing → EPOCH 4
- ❌ Payment reminders → EPOCH 4
- ❌ Analytics dashboard → EPOCH 4
- ❌ Advanced reporting → EPOCH 4

**Technical Limitations (EPOCH 1):**
- ❌ WebSocket (real-time) → Using short polling instead
- ❌ Celery (background jobs) → Synchronous processing only
- ❌ Redis caching → Django default cache
- ❌ PWA features → Basic responsive web only
- ❌ Advanced route optimization → Manual batching in EPOCH 3
- ❌ Multi-location support → Single location only

---

## 🎯 EPOCH 2: CUSTOMER ENGAGEMENT
**Estimate**: 20 hours | **Timeline**: 4-6 weeks

### ✅ IN SCOPE (EPOCH 2)

**Customer Portal:**
- ✅ Customer registration and login
- ✅ Customer authentication (separate from staff)
- ✅ Customer dashboard (tank status, order history)
- ✅ Self-service order placement (advance orders)
- ✅ Tank status visibility (real-time)

**Notifications:**
- ✅ Email notifications (order confirmation, completion)
- ✅ SMS notifications (optional, Twilio integration)
- ✅ Notification preferences (customer settings)

**Inspection Services:**
- ✅ Visual inspection service (PSI/PCI)
- ✅ Hydrostatic testing service
- ✅ Inspector role and permissions
- ✅ Inspector certification tracking (PSI/PCI credentials)
- ✅ Certification expiration alerts (inspectors)

**Enhanced Features:**
- ✅ Advance order scheduling (customer requests for future date)
- ✅ Order confirmation workflow
- ✅ Tank valve type tracking (DIN, Yoke)
- ✅ Tank material tracking (Steel, Aluminum)

**Technical Enhancements:**
- ✅ WebSocket for real-time updates (replace polling)
- ✅ Celery for async notifications
- ✅ Redis for caching

### ❌ OUT OF SCOPE (EPOCH 2)

**NOT in EPOCH 2:**
- ❌ Delivery scheduling → EPOCH 3
- ❌ Gas blend logging → EPOCH 3
- ❌ Equipment maintenance → EPOCH 3
- ❌ Online payments → EPOCH 4
- ❌ Advanced analytics → EPOCH 4

---

## 🎯 EPOCH 3: LOGISTICS & COMPLIANCE
**Estimate**: 20 hours | **Timeline**: 4-6 weeks

### ✅ IN SCOPE (EPOCH 3)

**Delivery Management:**
- ✅ Delivery scheduling (1-day advance notice)
- ✅ Delivery types (Pier, Route, Custom)
- ✅ Route batching (30-tank capacity, configurable)
- ✅ Delivery fee calculation (manual or by distance)
- ✅ Delivery confirmation (signature/photo)

**Mobile Driver Interface:**
- ✅ PWA for delivery drivers
- ✅ Route list and navigation
- ✅ Delivery status updates
- ✅ Offline mode (view route without internet)

**Gas Blend Logging:**
- ✅ Gas blend records (air, 32% nitrox)
- ✅ Fill pressure tracking (3000psi, 3442psi, etc.)
- ✅ Membrane compressor logging
- ✅ Blend compliance reports

**Equipment Maintenance:**
- ✅ Compressor hour meter tracking
- ✅ Filter/oil replacement schedules
- ✅ Parts hour limit alerts
- ✅ Air quality test scheduling
- ✅ Maintenance history logs

**Compliance:**
- ✅ Gas blend audit reports
- ✅ Equipment maintenance reports
- ✅ DOT compliance documentation (hydro tests)
- ✅ Air quality test reports

### ❌ OUT OF SCOPE (EPOCH 3)

**NOT in EPOCH 3:**
- ❌ AI route optimization (basic batching only)
- ❌ Online payment processing → EPOCH 4
- ❌ Automated invoicing → EPOCH 4
- ❌ Advanced analytics → EPOCH 4

---

## 🎯 EPOCH 4: FINANCIAL AUTOMATION (OPTIONAL)
**Estimate**: 15-18 hours | **Timeline**: 3-4 weeks

### ✅ IN SCOPE (EPOCH 4)

**Online Payments:**
- ✅ Stripe integration
- ✅ Credit card processing
- ✅ Payment receipts (automatic)
- ✅ Refund processing

**Automated Invoicing:**
- ✅ Auto-generate invoices on order completion
- ✅ Email invoice to customer
- ✅ Invoice payment tracking
- ✅ Payment reminders (automated)

**Dynamic Pricing:**
- ✅ Delivery fee automation (by distance)
- ✅ Discount codes
- ✅ Bulk order discounts
- ✅ Pricing rules engine

**Analytics & Reporting:**
- ✅ Revenue dashboard (daily, weekly, monthly)
- ✅ Service utilization charts
- ✅ Customer trends
- ✅ Tank utilization metrics
- ✅ Technician performance stats
- ✅ Export analytics (CSV, PDF)

### ❌ OUT OF SCOPE (EPOCH 4)

**NOT in EPOCH 4:**
- ❌ QuickBooks integration → EPOCH 5 (future)
- ❌ Multi-location support → EPOCH 5
- ❌ Loyalty/rewards program → EPOCH 5
- ❌ Native mobile apps (iOS/Android) → EPOCH 5
- ❌ AI-powered route optimization → EPOCH 5
- ❌ Advanced inventory (parts, supplies) → EPOCH 5

---

## 🚫 EXPLICITLY NOT INCLUDED (ANY EPOCH)

**Outside Business Scope:**
- ❌ Dive trip booking/management
- ❌ Dive certification tracking (c-cards) - unless specifically requested
- ❌ Equipment rental (BCDs, regulators, fins, etc.)
- ❌ Retail/e-commerce (selling gear)
- ❌ Social media integration
- ❌ Marketing automation
- ❌ CRM features (lead tracking, campaigns)
- ❌ Employee scheduling/payroll
- ❌ Dive shop POS system integration (unless specifically requested)
- ❌ Website/landing page creation
- ❌ Live chat/video support

**Technical Exclusions:**
- ❌ Blockchain/cryptocurrency
- ❌ AI/ML features (beyond basic recommendations)
- ❌ IoT device integration (smart sensors)
- ❌ Virtual reality (VR) features
- ❌ Augmented reality (AR) features

**Third-Party Integrations (Not Planned):**
- ❌ Mailchimp (email marketing)
- ❌ Salesforce (CRM)
- ❌ Zapier (automation)
- ❌ Shopify (e-commerce)

---

## 📋 FEATURE COMPARISON BY EPOCH

| Feature | EPOCH 1 | EPOCH 2 | EPOCH 3 | EPOCH 4 |
|---------|---------|---------|---------|---------|
| Tank Inventory | ✅ Full | ✅ Enhanced | ✅ Enhanced | ✅ Enhanced |
| Walk-In Orders | ✅ Full | ✅ Full | ✅ Full | ✅ Full |
| Customer Portal | ❌ No | ✅ Full | ✅ Full | ✅ Full |
| Notifications | ❌ No | ✅ Email/SMS | ✅ Email/SMS | ✅ Email/SMS |
| Inspections (Visual/Hydro) | ❌ No | ✅ Full | ✅ Full | ✅ Full |
| Delivery Management | ❌ No | ❌ No | ✅ Full | ✅ Full |
| Gas Blend Logging | ❌ No | ❌ No | ✅ Full | ✅ Full |
| Equipment Maintenance | ❌ No | ❌ No | ✅ Full | ✅ Full |
| Online Payments | ❌ No | ❌ No | ❌ No | ✅ Full |
| Advanced Analytics | ❌ No | ❌ No | ❌ No | ✅ Full |

---

## 🔄 SCOPE CHANGE MANAGEMENT

### How to Handle New Feature Requests

**During Active Epoch Development:**
1. **Document the request** in GitHub Issues with "feature-request" label
2. **Assess impact**:
   - Does it fit current epoch scope?
   - Will it delay current epoch?
   - Is it critical or can it wait?
3. **Decision Options**:
   - **Add to current epoch** (only if <2 hour impact AND critical)
   - **Defer to next epoch** (most common)
   - **Create new EPOCH 5** (if significant feature set)
4. **Get client approval** before adding to current epoch
5. **Update scope document** to reflect decision

### Scope Creep Prevention

**Red Flags (Scope Creep Indicators):**
- 🚩 "Can we just add..." (minimizing complexity)
- 🚩 "While you're at it..." (bundling unrelated features)
- 🚩 "This should be quick..." (underestimating effort)
- 🚩 "Just a small change..." (hidden dependencies)

**Response Protocol:**
1. Acknowledge the request
2. Estimate true effort (with dependencies)
3. Show impact on current timeline
4. Offer alternatives:
   - Defer to next epoch (recommended)
   - Replace current feature (trade-off)
   - Extend timeline + budget (explicit approval)
5. Document decision in scope change log

### Scope Change Log Template

**Change Request Format:**
```
Date: 2025-XX-XX
Requested By: [Client Name]
Feature: [Brief description]
Estimated Effort: [X hours]
Impact on Current Epoch: [Delay by X days / None]
Decision: [Approved for EPOCH X / Deferred to EPOCH Y / Rejected]
Rationale: [Why this decision was made]
Approved By: [Client Signature]
```

---

## 📊 SCOPE VALIDATION CHECKLIST

**Before Starting Each Epoch:**

**EPOCH Planning Validation:**
- [ ] All user stories align with epoch scope
- [ ] No features from future epochs leaked in
- [ ] Effort estimate matches ~20 hours
- [ ] All dependencies from previous epochs complete
- [ ] Client has reviewed and approved epoch scope

**During Development:**
- [ ] Each task references a planned user story
- [ ] No "bonus features" added without approval
- [ ] Any scope changes documented and approved
- [ ] Timeline impact of changes communicated

**Before SHIP:**
- [ ] All in-scope features delivered
- [ ] All out-of-scope features deferred (not forgotten)
- [ ] Scope boundaries document updated
- [ ] Next epoch scope reviewed with client

---

## 🎯 SUCCESS METRICS BY EPOCH

### EPOCH 1 Success Criteria
**Operational:**
- ✅ Zero paper tickets used (100% digital orders)
- ✅ Tank inventory accuracy >99%
- ✅ Order processing time <2 minutes
- ✅ Zero missed orders during test week

**Technical:**
- ✅ >95% test coverage
- ✅ All API endpoints documented
- ✅ Dashboard loads <1 second
- ✅ No critical bugs in production

**Business:**
- ✅ Staff adoption >90% (prefer digital over paper)
- ✅ Owner can manage prices without developer
- ✅ Audit trail for all operations

### EPOCH 2 Success Criteria
**Operational:**
- ✅ 50% customer self-service (order without calling)
- ✅ Customer service calls reduced 40%
- ✅ Inspection certifications tracked 100%

**Technical:**
- ✅ Real-time updates via WebSocket
- ✅ Email/SMS notifications 100% delivery
- ✅ Customer portal <3 second load

**Business:**
- ✅ Customer satisfaction >80%
- ✅ Reduced staff interruptions

### EPOCH 3 Success Criteria
**Operational:**
- ✅ 90% deliveries scheduled via system
- ✅ Drivers use mobile app (no paper manifests)
- ✅ Compliance documentation 100% complete

**Technical:**
- ✅ PWA works offline for drivers
- ✅ Route batching enforces capacity limits
- ✅ Gas blend logs exportable for audits

**Business:**
- ✅ Delivery efficiency improved 30%
- ✅ Compliance audit-ready anytime

### EPOCH 4 Success Criteria
**Operational:**
- ✅ 60% customers pay online
- ✅ Payment collection time reduced 70%
- ✅ Automated invoices sent <5 minutes

**Technical:**
- ✅ Stripe integration 100% reliable
- ✅ Analytics dashboard actionable
- ✅ Revenue tracking accurate to the cent

**Business:**
- ✅ Cash flow improved (faster payments)
- ✅ Data-driven decisions enabled

---

## 📝 SCOPE DECISION LOG (Example Entries)

### Example 1: Feature Request During EPOCH 1

**Date**: 2025-10-15
**Requested By**: Shop Owner
**Feature**: "Add ability to track tank valve types (DIN vs Yoke)"
**Estimated Effort**: 2 hours (model change, migration, UI update)
**Impact on Current Epoch**: Delay by 1 day (testing, documentation)
**Decision**: **Deferred to EPOCH 2**
**Rationale**: Not critical for MVP. EPOCH 1 goal is to eliminate paper tickets (achieved without valve type). Can add in EPOCH 2 along with tank material (steel/aluminum) as "Enhanced Tank Attributes" feature.
**Approved By**: Shop Owner (agreed to defer)

### Example 2: Critical Bug Fix

**Date**: 2025-10-20
**Requested By**: Technician
**Feature**: "Fix: Orders showing wrong price when price list updated"
**Estimated Effort**: 1 hour
**Impact on Current Epoch**: None (bug fix, not new feature)
**Decision**: **Fix immediately in EPOCH 1**
**Rationale**: Critical bug affecting invoicing accuracy. Price should snapshot at order creation, not reference current price. Fix required before SHIP.
**Approved By**: Automatic (bug fix, within scope)

### Example 3: Scope Expansion Request

**Date**: 2025-11-01
**Requested By**: Shop Owner
**Feature**: "Add inventory management for compressor oil, filters, spare parts"
**Estimated Effort**: 12 hours (new models, CRUD, stock alerts)
**Impact on Current Epoch**: Would delay EPOCH 2 by 2 weeks
**Decision**: **Deferred to EPOCH 5 (new epoch)**
**Rationale**: Out of scope for EPOCH 1-4. Equipment maintenance (EPOCH 3) tracks hours/schedules but not parts inventory. This is a separate inventory management system. Recommend as standalone EPOCH 5 or use existing inventory software.
**Approved By**: Shop Owner (agreed to defer, will revisit after EPOCH 3)

---

## ✅ FINAL SCOPE APPROVAL

**Client Acknowledgment:**

I understand and approve the scope boundaries as defined for each epoch:

**EPOCH 1 (Core Operations):**
- [ ] I understand what IS included (tank, orders, customers, dashboard, admin)
- [ ] I understand what is NOT included (customer portal, delivery, payments, etc.)
- [ ] I approve the 20-hour estimate and 4-6 week timeline

**EPOCH 2 (Customer Engagement):**
- [ ] I understand what IS included (portal, notifications, inspections)
- [ ] I understand what is NOT included (delivery, gas logging, payments)
- [ ] I approve proceeding to EPOCH 2 after EPOCH 1 validation

**EPOCH 3 (Logistics & Compliance):**
- [ ] I understand what IS included (delivery, mobile, gas logs, equipment)
- [ ] I understand what is NOT included (payments, advanced analytics)
- [ ] I approve proceeding to EPOCH 3 after EPOCH 2 validation

**EPOCH 4 (Financial Automation) - OPTIONAL:**
- [ ] I understand what IS included (payments, invoicing, analytics)
- [ ] I understand this epoch is optional (decision after EPOCH 3)
- [ ] I approve the total investment for EPOCHS 1-4 if proceeding

**Scope Change Understanding:**
- [ ] I understand any feature requests outside defined scope require:
  - Effort estimation
  - Timeline impact assessment
  - Explicit approval to add (or defer to next epoch)
- [ ] I agree to the scope change management process

**Client Signature**: ________________________
**Date**: ___________________________

**Developer Commitment**: I commit to delivering only the features defined in approved epoch scope, and will request approval for any changes.

**Developer Signature**: ________________________
**Date**: ___________________________

---

**Document Control:**
- Version: 1.0
- Last Updated: 2025-09-30
- Next Review: After each EPOCH completion
- Status: READY FOR CLIENT APPROVAL
