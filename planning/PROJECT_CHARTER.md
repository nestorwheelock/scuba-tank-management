# PROJECT CHARTER: Scuba Tank Fill Station Management System

**Project Name**: DiveTank Pro
**Version**: v1.0.0 (EPOCH 1)
**Date**: 2025-09-30
**Status**: SPEC Phase - Awaiting Client Approval

---

## EXECUTIVE SUMMARY

A comprehensive web and mobile-first management system for a scuba tank fill station that provides air/nitrox fills, hydrostatic testing, visual inspections, tank storage, and delivery services. The system will replace current paper-based processes, streamline communication, reduce missed orders, and improve operational efficiency.

---

## 1. WHAT WE'RE BUILDING

### Core System
A multi-tenant web application with mobile-responsive design providing:

**For Technicians/Staff:**
- Tank inventory management (100-200 tanks on-site)
- Service order management (fills, inspections, hydro tests)
- Delivery route planning and management
- Equipment maintenance tracking (compressor, filters, parts)
- Inspector certification tracking (PSI/PCI)
- Gas blend logging (air, 32% nitrox)
- Customer communication portal integration

**For Customers:**
- Self-service order portal
- Tank status tracking
- Service history access
- Delivery scheduling (1-day advance notice)
- Online payment options
- Order confirmation notifications

**For Delivery Drivers:**
- Mobile route management
- Delivery confirmation
- Real-time status updates

### Key Features
1. **Tank Management**: Track ownership (rental, client-owned, operator-owned), location, certification status
2. **Service Scheduling**: Fills, visual inspections (annual), hydrostatic testing (5-year)
3. **Delivery Logistics**: Route batching, pier delivery, custom routes, 30-tank max capacity per load
4. **Certification Tracking**: Inspector credentials, tank visual/hydro expiration alerts
5. **Equipment Maintenance**: Compressor hour meters, filter/oil replacement schedules, parts hour limits
6. **Pricing & Invoicing**: Standard price list, flexible payment timing, automatic invoicing
7. **Communication**: Automated order confirmations, SMS/email notifications, portal messaging
8. **Gas Blend Logging**: Membrane compressor logs for air and 32% nitrox (liability tracking)

---

## 2. WHY WE'RE BUILDING THIS

### Business Problems Being Solved

**Current Pain Points:**
1. **Disorganized Order Management**: Orders come via WhatsApp, phone, paper tickets - easy to miss or lose
2. **Communication Overhead**: Technicians constantly interrupted by customer calls/messages
3. **Manual Coordination**: Tracking 100-200 tanks on paper is error-prone and time-consuming
4. **Payment Collection**: Chasing customers for payment is inefficient
5. **No Order Confirmation**: Customers uncertain if orders received, leading to duplicate calls
6. **Service Tracking**: Visual/hydro expiration tracking is manual and unreliable
7. **Equipment Maintenance**: No automated alerts for compressor maintenance schedules

### Business Value Delivered

**Efficiency Gains:**
- Reduce technician interruptions by 70% (portal replaces calls/messages)
- Eliminate missed orders through centralized system
- Automate payment collection and invoicing
- Cut order processing time by 60%

**Customer Experience:**
- Self-service ordering 24/7
- Automatic order confirmations
- Real-time tank status tracking
- Delivery transparency

**Operational Excellence:**
- Automated certification expiration alerts
- Equipment maintenance scheduling
- Compliance documentation (gas logs, air tests)
- Delivery route optimization

**Financial Impact:**
- Reduce time spent on payment collection
- Minimize lost revenue from missed orders
- Improve cash flow with online payment options
- Better capacity planning with data analytics

---

## 3. HOW WE'RE BUILDING IT

### Technical Architecture

**Stack:**
- **Backend**: Django 5.x (Python 3.11+) - REST API
- **Frontend**: React (mobile-responsive SPA) or Next.js
- **Mobile**: Progressive Web App (PWA) for driver access
- **Database**: PostgreSQL (relational data integrity for critical operations)
- **Cache**: Redis (real-time updates, background jobs)
- **Background Jobs**: Celery (notifications, maintenance alerts)
- **File Storage**: S3-compatible (inspection reports, certificates)
- **Deployment**: Docker containers, cloud-hosted (AWS/GCP/Azure)

**Key Design Patterns:**
- RESTful API architecture
- Role-based access control (RBAC)
- Event-driven notifications
- Audit logging for compliance
- Multi-tenant data isolation

### Integration Points

**Required Integrations:**
1. **Payment Processing**: Stripe/PayPal API
2. **SMS Notifications**: Twilio API
3. **Email**: SendGrid/AWS SES
4. **Calendar**: Google Calendar API (delivery scheduling)
5. **Mapping**: Google Maps API (route optimization)

**Future Integrations (v2.0):**
- Accounting software (QuickBooks)
- Point of Sale (POS) system
- Mobile app (native iOS/Android)

### Security & Compliance

**Security Requirements:**
- HTTPS/TLS encryption
- Secure password hashing (bcrypt)
- Role-based permissions
- Session management with CSRF protection
- API rate limiting
- Data backup and disaster recovery

**Compliance Needs:**
- Gas blend logging (liability protection)
- Inspector certification documentation
- Tank hydrostatic test records (DOT requirements)
- Air quality test reports (compressor maintenance)
- Equipment maintenance logs (safety compliance)

---

## 4. SUCCESS CRITERIA

### Functional Success (Must Achieve)

**For Staff:**
- ✅ Manage 100-200 tank inventory with real-time status
- ✅ Process service orders without paper tickets
- ✅ Track all certifications with automated expiration alerts
- ✅ Schedule deliveries with route batching (30-tank capacity)
- ✅ Log gas blends for compliance
- ✅ Track compressor maintenance schedules

**For Customers:**
- ✅ Place orders online 24/7
- ✅ Receive automatic order confirmations
- ✅ Track tank status and service history
- ✅ Schedule deliveries 1-day advance
- ✅ Pay online or at time of service

**For Drivers:**
- ✅ Access delivery routes on mobile device
- ✅ Confirm deliveries with digital signature/photo
- ✅ Update delivery status in real-time

### Performance Metrics

**System Performance:**
- Page load time: <2 seconds
- API response time: <200ms (95th percentile)
- Mobile PWA responsiveness: 60fps scrolling
- Uptime: 99.5% availability

**Business KPIs:**
- Reduce missed orders to <1% (from current ~10%)
- Cut customer service calls by 70%
- Reduce payment collection time by 50%
- Process 100+ orders/day without performance degradation

### Quality Metrics

**Code Quality:**
- >95% test coverage
- Zero critical security vulnerabilities
- <5% bug rate in production
- Full API documentation

**User Adoption:**
- 80% of customers using portal within 3 months
- <30 minute training time for staff
- <5 minute customer onboarding

---

## 5. RISKS & MITIGATION

### Technical Risks

**Risk 1: Data Migration from Paper**
- **Impact**: High - Lost historical data
- **Probability**: Medium
- **Mitigation**: Start fresh with clean data, optionally import critical tank records manually

**Risk 2: Third-Party API Failures**
- **Impact**: Medium - SMS/payment disruptions
- **Mitigation**: Graceful degradation, fallback to manual processes, queue retry logic

**Risk 3: Mobile Connectivity Issues**
- **Impact**: Medium - Drivers can't update delivery status
- **Mitigation**: Offline-first PWA, sync when connection restored

**Risk 4: Concurrent Order Processing**
- **Impact**: High - Double-booking tanks
- **Mitigation**: Database transactions, optimistic locking, real-time inventory checks

### Business Risks

**Risk 5: User Adoption Resistance**
- **Impact**: High - Staff/customers prefer old methods
- **Probability**: Medium
- **Mitigation**: Phased rollout, comprehensive training, maintain manual backup for 60 days

**Risk 6: Complex Pricing Rules**
- **Impact**: Medium - Hard-coded logic becomes inflexible
- **Mitigation**: Admin-configurable pricing rules, not hard-coded

**Risk 7: Delivery Route Optimization Complexity**
- **Impact**: Low - Manual routing still required
- **Probability**: Low
- **Mitigation**: Start with simple batching, add AI route optimization in v2.0

### Operational Risks

**Risk 8: Equipment Downtime During Implementation**
- **Impact**: Critical - Business interruption
- **Probability**: Low
- **Mitigation**: Implement during off-peak season, keep paper backup

**Risk 9: Payment Processing Issues**
- **Impact**: High - Cash flow disruption
- **Probability**: Low
- **Mitigation**: Multiple payment gateways, manual invoice fallback

**Risk 10: Data Privacy/Security Breach**
- **Impact**: Critical - Legal liability, reputation damage
- **Probability**: Low
- **Mitigation**: Security audit, penetration testing, encryption at rest/transit, GDPR compliance

---

## 6. SCOPE BOUNDARIES

### ✅ IN SCOPE (v1.0.0 - EPOCH 1)

**Core Functionality:**
- Tank inventory management (100-200 capacity)
- Service order management (fills, visual, hydro)
- Customer portal (order, track, pay)
- Delivery scheduling and route batching
- Inspector certification tracking (PSI/PCI)
- Equipment maintenance scheduling (compressor, filters, parts)
- Gas blend logging (air, 32% nitrox)
- Standard pricing engine
- SMS/email notifications
- Online payment processing (Stripe)
- Mobile-responsive web (PWA for drivers)
- Role-based permissions (admin, tech, driver, customer)
- Audit logging and compliance reports

**User Roles:**
1. Admin (owner/manager)
2. Technician (service provider)
3. Delivery Driver
4. Customer (diver/operator)
5. Inspector (certified PSI/PCI)

**Service Types:**
- Air fills
- Nitrox 32% fills
- Visual inspections (PSI/PCI)
- Hydrostatic testing
- Tank storage
- Delivery (pier, route, custom)
- Walk-in service

### ❌ OUT OF SCOPE (Future Versions)

**Deferred to v2.0:**
- Advanced gas blending (trimix, custom mixes)
- AI-powered route optimization
- Native mobile apps (iOS/Android)
- Multi-location support (franchise model)
- Inventory management (parts, supplies)
- Employee scheduling/payroll
- Advanced analytics/BI dashboards
- Integration with dive shop POS systems
- Customer loyalty/rewards program
- Equipment rental management (BCDs, regs, etc.)
- Dive trip booking integration

**Explicitly NOT Included:**
- Accounting software (QuickBooks integration is v2.0)
- CRM features (marketing campaigns, lead tracking)
- Website/e-commerce integration
- Social media integration
- Live chat support
- Video call support
- Dive certification tracking (outside business scope)

---

## 7. CONSTRAINTS & ASSUMPTIONS

### Technical Constraints
1. Must be cloud-hosted (no on-premise servers)
2. Must work on tablets/phones (technicians use mobile devices)
3. Must support offline mode for delivery drivers
4. Must integrate with Stripe for payments
5. Must use PostgreSQL (data integrity requirements)

### Business Constraints
1. Budget: $X,XXX (to be determined)
2. Timeline: 12-16 weeks to v1.0 launch
3. Staff training time: <2 hours per person
4. Must maintain paper backup for 60 days during transition
5. No business disruption during implementation

### Assumptions
1. Internet connectivity is reliable at shop location
2. Customers have email/phone for notifications
3. Staff have basic computer literacy
4. Delivery drivers have smartphones
5. Shop has digital scale for tank weights (if needed)
6. Compressor has hour meter or can be retrofitted
7. Historical data migration is optional (start fresh acceptable)
8. Current price list is stable and documented

---

## 8. DELIVERY TIMELINE (ESTIMATED)

### EPOCH 1: v1.0.0 - Core System (12-16 weeks)

**Sprint 1 (Weeks 1-2): Foundation**
- Django project setup
- Database schema design
- User authentication & RBAC
- API framework

**Sprint 2 (Weeks 3-4): Tank Management**
- Tank inventory CRUD
- Ownership tracking
- Certification expiration alerts
- Tank search/filter

**Sprint 3 (Weeks 5-6): Service Management**
- Service order creation
- Pricing engine
- Order status workflow
- Invoice generation

**Sprint 4 (Weeks 7-8): Customer Portal**
- Customer dashboard
- Order placement
- Tank status tracking
- Service history

**Sprint 5 (Weeks 9-10): Delivery & Logistics**
- Delivery scheduling
- Route batching
- Driver mobile interface
- Delivery confirmation

**Sprint 6 (Weeks 11-12): Compliance & Maintenance**
- Gas blend logging
- Equipment maintenance schedules
- Inspector certification tracking
- Compliance reports

**Sprint 7 (Weeks 13-14): Integration & Notifications**
- Payment processing (Stripe)
- SMS/email notifications
- Calendar integration
- Admin configuration

**Sprint 8 (Weeks 15-16): Testing & Launch**
- User acceptance testing
- Staff training
- Data migration (if needed)
- Production deployment

### Post-Launch (Weeks 17-20): Support & Iteration
- Bug fixes
- User feedback incorporation
- Performance optimization
- Documentation refinement

---

## 9. ACCEPTANCE TEST PLAN

### Test Scenarios (Must Pass Before Ship)

**Tank Management:**
- ✅ Add 100 tanks with varying ownership types
- ✅ Search/filter tanks by owner, status, certification
- ✅ Receive alert for tanks expiring in 30 days
- ✅ Update tank status through service workflow

**Service Orders:**
- ✅ Create walk-in fill order (air, nitrox)
- ✅ Create advance inspection order
- ✅ Process 50 orders in a day without errors
- ✅ Generate invoice for flexible payment timing

**Customer Portal:**
- ✅ Customer registers and logs in
- ✅ Customer places order with 1-day advance notice
- ✅ Customer receives confirmation email/SMS
- ✅ Customer tracks tank status in real-time
- ✅ Customer pays online via Stripe

**Delivery Management:**
- ✅ Schedule delivery to pier with 1-day notice
- ✅ Batch 30 tanks into single route
- ✅ Driver accesses route on mobile device
- ✅ Driver confirms delivery with signature/photo
- ✅ Customer notified of delivery completion

**Compliance & Maintenance:**
- ✅ Log gas blend for 32% nitrox fill
- ✅ Track compressor hours and trigger oil change alert
- ✅ Verify inspector PSI/PCI certification before assigning inspection
- ✅ Generate compliance report for gas logs

**Integration:**
- ✅ Process payment through Stripe
- ✅ Send SMS notification via Twilio
- ✅ Send email via SendGrid
- ✅ Sync delivery to Google Calendar

**Performance:**
- ✅ Load 200 tanks in inventory without lag
- ✅ Process order in <2 seconds
- ✅ Mobile interface scrolls at 60fps
- ✅ API responds in <200ms

**Security:**
- ✅ Customer cannot access admin functions
- ✅ Driver cannot modify pricing
- ✅ Technician cannot delete audit logs
- ✅ Passwords are hashed and secure

---

## 10. STAKEHOLDERS & ROLES

### Project Team
- **Product Owner**: [Shop Owner/Manager]
- **Technical Lead**: [Senior Developer - You]
- **AI Development Partner**: Claude Code
- **QA/Tester**: [Shop Staff or External]

### Business Stakeholders
- **Shop Owner**: Final approval authority
- **Technicians**: Primary users (3-5 people)
- **Delivery Drivers**: Mobile app users (1-3 people)
- **Customers**: Portal users (50-200 active)

### Decision Authority
- **Technical Decisions**: Technical Lead
- **Business Logic**: Shop Owner
- **UX/UI**: Collaborative (Lead + Owner)
- **Scope Changes**: Shop Owner (requires re-approval)

---

## 11. PRICING ESTIMATE

### Development Effort Estimate

**Total Human-Equivalent Hours**: 280 hours

**Breakdown by Sprint:**
- Sprint 1 (Foundation): 30 hours
- Sprint 2 (Tank Management): 35 hours
- Sprint 3 (Service Management): 40 hours
- Sprint 4 (Customer Portal): 35 hours
- Sprint 5 (Delivery & Logistics): 40 hours
- Sprint 6 (Compliance): 30 hours
- Sprint 7 (Integration): 40 hours
- Sprint 8 (Testing & Launch): 30 hours

### Pricing Calculation

**Traditional Development Cost:**
- 280 hours × $50/hour = $14,000

**AI Automation Discount (80%):**
- Complexity: High (multi-tenant, compliance, integrations)
- Discount: 80%
- Savings: $14,000 × 0.80 = $11,200

**Final Client Price: $2,800**

**Client Saves: $11,200 (80% discount)**

### What Client Gets for $2,800
- ✅ Production-ready Django application
- ✅ Mobile-responsive customer portal
- ✅ Delivery driver PWA
- ✅ >95% test coverage
- ✅ Complete API documentation
- ✅ Deployment configuration
- ✅ Staff training materials
- ✅ 60-day post-launch support

**Actual AI Execution Time**: 15-20 hours
**Human-Equivalent Effort**: 280 hours
**Client Value**: $14,000 worth of work
**Client Cost**: $2,800

### Optional Add-Ons (Separate Pricing)
- Advanced route optimization AI: +$500
- Native mobile apps (iOS/Android): +$1,500
- Multi-location support: +$800
- Accounting integration (QuickBooks): +$400

---

## 12. NEXT STEPS

### Immediate Actions (This Week)
1. **Review this charter** with shop owner
2. **Review user stories** (see planning/stories/)
3. **Clarify any assumptions** or missing requirements
4. **Approve scope** or request changes

### SPEC Approval Process
1. Shop owner reviews all SPEC documents
2. Questions/changes via GitHub Issue #1
3. Formal approval closes Issue #1
4. Development begins (BUILD phase)

### Post-Approval
1. Set up development environment
2. Create GitHub repository
3. Initialize Django project
4. Begin Sprint 1 (Foundation)

---

## 13. OPEN QUESTIONS

**For Shop Owner to Answer:**
1. Do you want customers to see pricing before ordering, or only after quote?
2. Should delivery fee be calculated automatically by distance, or manual per order?
3. Do you want to track tank valve types (DIN, Yoke, etc.)?
4. Should system send daily summary reports to owner?
5. Do you want to track customer dive certifications (c-cards)?
6. Should walk-in customers be able to create anonymous orders, or must register?
7. Do you want to track tank fill pressure (e.g., 3000psi, 3300psi, 3442psi)?
8. Should system enforce minimum advance notice (e.g., block same-day delivery orders)?

---

## APPROVAL SIGNATURES

**Client Approval (Shop Owner):**

I have reviewed and approve this project charter and understand:
- What IS included in v1.0.0
- What is NOT included (future versions)
- Estimated timeline (12-16 weeks)
- Estimated cost ($2,800)
- Success criteria and acceptance tests

Signature: ________________________
Date: _____________________________

**Developer Acceptance:**

I commit to delivering the scope as defined with >95% test coverage and production-ready quality.

Signature: ________________________
Date: _____________________________

---

**Document Control:**
- Version: 1.0
- Last Updated: 2025-09-30
- Next Review: Upon client feedback
- Status: AWAITING CLIENT APPROVAL
