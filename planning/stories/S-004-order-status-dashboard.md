# S-004: Order Status Dashboard

**Story Type**: User Story
**Priority**: High
**Estimate**: 3 hours
**Epoch**: EPOCH 1 - Core Operations (MVP)
**Status**: ⏳ Pending Approval

---

## User Story

**As a** scuba tank fill station technician
**I want to** view a real-time dashboard of order statuses
**So that** I can prioritize work, track progress, and ensure no orders are missed

---

## Business Context

**Current Pain Point:**
- No visibility into pending vs completed orders
- Hard to know what needs to be done next
- Cannot track daily/weekly productivity
- Difficult to report business metrics to owner

**Business Value:**
- At-a-glance view of workload (pending, in-progress, completed)
- Prioritize urgent orders
- Track daily revenue and order volume
- Reduce missed orders to zero
- Foundation for business analytics (future epochs)

---

## Acceptance Criteria

### AC-1: Real-Time Order Status View
- [ ] When I open the dashboard, I see order counts: Pending (X), In Progress (Y), Completed Today (Z)
- [ ] When an order status changes, dashboard updates without page refresh (WebSocket or polling)
- [ ] When I click on a status card, I see filtered list of those orders
- [ ] When no orders exist, dashboard shows "No orders yet today"

### AC-2: Pending Orders Queue
- [ ] When I view "Pending Orders", they display sorted by creation time (oldest first)
- [ ] When I click "Start" on a pending order, status changes to "In Progress" and moves to In Progress list
- [ ] When pending list is empty, message displays: "All caught up! No pending orders."
- [ ] When a new order is created, it appears in pending queue immediately

### AC-3: In-Progress Orders Tracking
- [ ] When I view "In Progress" orders, I see which technician is working on each
- [ ] When I click "Complete" on an in-progress order, status changes to "Completed"
- [ ] When in-progress order exceeds 30 minutes, visual indicator shows (highlight/badge)
- [ ] When I filter by technician, only their in-progress orders display

### AC-4: Daily Summary Metrics
- [ ] When I view dashboard, I see today's metrics: Total Orders, Total Revenue, Avg Order Value
- [ ] When I view completed orders count, it includes only today's completions
- [ ] When I view revenue, it sums all "Paid" orders for today
- [ ] When day changes (midnight), dashboard resets to new day automatically

### AC-5: Quick Actions
- [ ] When I hover over an order in dashboard, quick action buttons appear: View Details, Update Status
- [ ] When I click "View Details", order detail page opens in modal/sidebar (no full page reload)
- [ ] When I click "Update Status", dropdown shows valid next states only
- [ ] When I mark order paid from dashboard, it updates immediately

### AC-6: Order Filtering & Sorting
- [ ] When I apply date filter (Today, Yesterday, This Week), dashboard shows orders for that period
- [ ] When I apply customer filter, dashboard shows only that customer's orders
- [ ] When I apply service type filter (Air, Nitrox), dashboard shows only those orders
- [ ] When I sort by newest/oldest, order list rearranges accordingly

---

## Technical Requirements

### API Endpoints (Additions to S-002)

**Required:**
- `GET /api/dashboard/stats/` - Get dashboard summary stats
  - Response: `{ pending_count, in_progress_count, completed_today_count, total_revenue_today, avg_order_value }`
- `GET /api/orders/pending/` - Get pending orders (sorted by created_at ASC)
- `GET /api/orders/in-progress/` - Get in-progress orders
- `GET /api/orders/completed-today/` - Get today's completed orders
- `POST /api/orders/{id}/start/` - Move order from Pending → In Progress
- `POST /api/orders/{id}/complete/` - Move order from In Progress → Completed

**Query Parameters (Enhanced):**
- `?date_filter=today|yesterday|this_week`
- `?technician=<user_id>`
- `?service_type=AIR_FILL|NITROX_32_FILL`

### UI Requirements

**Dashboard Layout:**

**Top Section: Status Cards (3 cards side-by-side)**
- **Pending Card:**
  - Icon: Clock
  - Count: X orders
  - Click → Show pending list
  - Color: Orange/Warning

- **In Progress Card:**
  - Icon: Wrench/Tool
  - Count: Y orders
  - Click → Show in-progress list
  - Color: Blue/Info

- **Completed Today Card:**
  - Icon: Checkmark
  - Count: Z orders
  - Click → Show completed list
  - Color: Green/Success

**Middle Section: Daily Metrics (3 metrics side-by-side)**
- Total Orders Today: XX
- Total Revenue Today: $XXX
- Avg Order Value: $XX

**Bottom Section: Order Lists (Tabs)**
- Tab 1: Pending Orders (default active)
- Tab 2: In Progress Orders
- Tab 3: Completed Today

**Order List Table:**
- Columns: Order #, Customer, Tank(s), Service, Total, Time Elapsed, Actions
- Actions: Quick status update buttons (Start / Complete / Mark Paid)
- Row highlighting: Orders >30min in progress are highlighted

**Filters Bar (Above Table):**
- Date Filter: Dropdown (Today, Yesterday, This Week)
- Customer Filter: Autocomplete search
- Service Type Filter: Checkboxes (Air Fill, Nitrox 32%)
- Technician Filter: Dropdown (if in-progress view)
- Clear Filters button

### Real-Time Updates

**Approach (Choose one based on scale):**
1. **WebSocket (Preferred for production):**
   - Django Channels for WebSocket connection
   - Push updates when order status changes
   - Dashboard subscribes to "order_updates" channel

2. **Short Polling (Simpler for EPOCH 1):**
   - Poll `/api/dashboard/stats/` every 30 seconds
   - Poll active list endpoint every 30 seconds
   - Client-side state management (React/Vue)

**For EPOCH 1: Use short polling (simpler, faster to implement)**

---

## Definition of Done

### Development Complete
- [ ] Dashboard API endpoints implemented (6 new endpoints)
- [ ] Dashboard UI with status cards
- [ ] Daily metrics calculation and display
- [ ] Order lists with filtering and sorting
- [ ] Quick action buttons (Start, Complete, Mark Paid)
- [ ] Short polling for real-time updates (30-second interval)
- [ ] Order detail modal/sidebar

### Testing Complete (>95% Coverage)
- [ ] Unit tests: Dashboard stats calculation (8 tests)
- [ ] Unit tests: Date filtering logic (today, yesterday, this week) (6 tests)
- [ ] API tests: Dashboard stats endpoint (5 tests)
- [ ] API tests: Filtered order lists (10 tests)
- [ ] Integration tests: Status transitions from dashboard (8 tests)
- [ ] UI tests: Quick action buttons (6 tests)
- [ ] Edge case: Dashboard with zero orders
- [ ] Edge case: Dashboard at midnight (day rollover)
- [ ] Edge case: Concurrent status updates (two techs, same order)

### Quality Assurance
- [ ] Dashboard loads in <1 second
- [ ] Status cards update within 30 seconds of order change
- [ ] Order lists support 100+ orders without lag
- [ ] Quick actions update status in <500ms
- [ ] Mobile-responsive layout (tablet/phone)
- [ ] Dashboard auto-refreshes every 30 seconds

### Documentation
- [ ] API endpoints documented
- [ ] Dashboard metrics calculation logic documented
- [ ] User guide: "Using the dashboard"
- [ ] User guide: "Quick order management"

---

## Test Scenarios

### Happy Path
1. **View Dashboard with Orders**
   - Open dashboard
   - See: Pending (5), In Progress (2), Completed Today (10)
   - See metrics: Total Orders (17), Revenue ($340), Avg Value ($20)
   - Click "Pending" card → Pending orders list displays

2. **Start Pending Order**
   - View pending orders list
   - Click "Start" on order ORD-2025-00001
   - Order moves to "In Progress" list
   - Pending count decreases by 1
   - In Progress count increases by 1

3. **Complete In-Progress Order**
   - View in-progress orders list
   - Click "Complete" on order ORD-2025-00001
   - Order moves to "Completed Today" list
   - In Progress count decreases by 1
   - Completed count increases by 1

4. **Mark Order Paid**
   - View completed orders list
   - Click "Mark Paid" on order ORD-2025-00001
   - Order status changes to "Paid"
   - Total Revenue Today increases by order amount

5. **Filter by Date**
   - Select "Yesterday" from date filter
   - Dashboard shows yesterday's orders
   - Metrics recalculate for yesterday

### Edge Cases
6. **Dashboard with Zero Orders**
   - New day, no orders yet
   - Dashboard shows: Pending (0), In Progress (0), Completed (0)
   - Message: "No orders yet today. Create your first order!"

7. **Order In Progress >30 Minutes**
   - Order started 35 minutes ago
   - Row highlighted in yellow/orange
   - Time elapsed shows "35 min" in warning color

8. **Midnight Rollover**
   - Dashboard open at 11:59 PM
   - Clock strikes midnight
   - Dashboard auto-resets to new day (all counts zero)
   - Yesterday's orders no longer in "Today" view

9. **Concurrent Status Update**
   - Tech A marks order "In Progress" at 10:00:01
   - Tech B tries to mark same order "In Progress" at 10:00:02
   - System prevents duplicate transition
   - Tech B sees "Order already in progress by Tech A"

### Real-Time Update
10. **Auto-Refresh Dashboard**
    - Dashboard open, polling every 30 seconds
    - New order created in another tab/device
    - Within 30 seconds, dashboard shows new order in pending count
    - No page refresh needed

---

## Dependencies

**Requires:**
- S-002: Service Order Creation (orders must exist to track)
- S-003: Customer Database (for customer filter)

**Enables:**
- Real-time operations awareness
- Data-driven decision making
- Foundation for analytics (EPOCH 4)

---

## Notes for Implementation

**Dashboard Stats Calculation:**
```python
from django.utils import timezone
from datetime import timedelta

def get_dashboard_stats():
    today = timezone.now().date()
    today_start = timezone.make_aware(timezone.datetime.combine(today, timezone.datetime.min.time()))
    today_end = timezone.make_aware(timezone.datetime.combine(today, timezone.datetime.max.time()))

    pending_count = ServiceOrder.objects.filter(status='PENDING').count()
    in_progress_count = ServiceOrder.objects.filter(status='IN_PROGRESS').count()
    completed_today_count = ServiceOrder.objects.filter(
        status__in=['COMPLETED', 'PAID'],
        completed_at__range=[today_start, today_end]
    ).count()

    paid_today = ServiceOrder.objects.filter(
        status='PAID',
        paid_at__range=[today_start, today_end]
    ).aggregate(total=Sum('subtotal'))['total'] or 0

    avg_order_value = paid_today / completed_today_count if completed_today_count > 0 else 0

    return {
        'pending_count': pending_count,
        'in_progress_count': in_progress_count,
        'completed_today_count': completed_today_count,
        'total_revenue_today': paid_today,
        'avg_order_value': round(avg_order_value, 2)
    }
```

**Business Rules:**
- "Today" defined as current calendar day (00:00:00 to 23:59:59 in shop timezone)
- "Completed Today" includes both COMPLETED and PAID orders
- "Revenue Today" only includes PAID orders (not COMPLETED but unpaid)
- Time elapsed starts from order.created_at, calculates to now()
- Orders >30 min in IN_PROGRESS status are highlighted (potential bottleneck)

**Short Polling Implementation (React example):**
```javascript
useEffect(() => {
  const fetchDashboardStats = async () => {
    const response = await fetch('/api/dashboard/stats/');
    const data = await response.json();
    setStats(data);
  };

  fetchDashboardStats(); // Initial fetch
  const interval = setInterval(fetchDashboardStats, 30000); // Poll every 30s

  return () => clearInterval(interval); // Cleanup on unmount
}, []);
```

**Future Enhancements (EPOCH 2+):**
- WebSocket for instant updates (EPOCH 2)
- Technician performance dashboard (orders/hour, avg completion time)
- Weekly/monthly analytics
- Customer order trends
- Service type popularity charts
- Revenue forecasting
- Export dashboard data to CSV/PDF

---

## Estimated Effort Breakdown

**Total: 3 hours**

- API Endpoints: 1 hour
- Dashboard UI: 1.5 hours
- Testing: 0.5 hours

---

**Story Status**: Ready for SPEC approval
**Next Steps**: Awaiting client approval on EPOCH 1 scope
