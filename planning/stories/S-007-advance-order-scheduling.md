# S-007: Advance Order Scheduling

**Story Type**: User Story
**Priority**: High
**Estimate**: 3 hours
**Epoch**: EPOCH 2 - Customer Engagement
**Status**: ⏳ Pending Approval

---

## User Story

**As a** dive shop customer
**I want to** schedule tank fill orders for future dates and times
**So that** I can plan ahead and have tanks ready when I need them without making phone calls

---

## Business Context

**Current Pain Point:**
- Customers call to request "fills for Friday afternoon"
- No system to track scheduled orders
- Technicians forget scheduled orders (written on sticky notes)
- Customers show up expecting tanks ready, but fills not done

**Business Value:**
- Reduce phone calls for advance scheduling
- Never miss a scheduled order
- Automatic reminders to techs when orders are due
- Professional scheduling experience for customers
- Foundation for delivery scheduling (EPOCH 3)

---

## Acceptance Criteria

### AC-1: Schedule Order from Customer Portal
- [ ] When I place an order, I can optionally set "Schedule for later"
- [ ] When I click schedule, date/time picker appears
- [ ] When I select date, only future dates are allowed (no past dates)
- [ ] When I select time, I can choose specific time or time slot (Morning 8am-12pm, Afternoon 12pm-5pm, Evening 5pm-8pm)

### AC-2: Scheduled Order Confirmation
- [ ] When I schedule an order, confirmation shows: "Order #XXX scheduled for [date] at [time]"
- [ ] When order is scheduled, I receive email/SMS confirmation with scheduled date/time
- [ ] When I view my order history, scheduled orders show with badge "Scheduled for [date]"
- [ ] When scheduled date is today, order auto-appears in "Due Today" queue

### AC-3: Technician Scheduled Order Queue
- [ ] When I open dashboard, I see "Orders Due Today" section
- [ ] When I click "Orders Due Today", orders scheduled for today display sorted by time
- [ ] When an order is overdue (scheduled time passed), it shows in red/warning state
- [ ] When I filter orders, I can filter by "Scheduled Orders" vs "Walk-In Orders"

### AC-4: Rescheduling
- [ ] When customer views scheduled order, they can click "Reschedule"
- [ ] When rescheduling, new date/time picker appears pre-populated with current schedule
- [ ] When customer reschedules, they receive confirmation of new date/time
- [ ] When order is rescheduled, technician sees note: "Rescheduled from [old date] to [new date]"

### AC-5: Scheduled Order Notifications
- [ ] When order is scheduled, customer receives confirmation email/SMS
- [ ] When scheduled order is 24 hours away, technician receives reminder
- [ ] When scheduled order is today, technician sees it in "Due Today" queue
- [ ] When scheduled order is completed, customer receives completion notification

### AC-6: Calendar Integration (Optional Phase 1)
- [ ] When viewing scheduled orders, I can toggle to "Calendar View"
- [ ] When in calendar view, scheduled orders appear on their scheduled dates
- [ ] When I click on date in calendar, orders for that date display
- [ ] When I view week/month view, order count shows on each date

---

## Technical Requirements

### Data Model Changes

**ServiceOrder Model (Add Fields):**
```python
# Scheduling fields
scheduled_date = models.DateTimeField(null=True, blank=True,
    help_text="When customer wants order ready")
is_scheduled = models.BooleanField(default=False)
time_slot = models.CharField(max_length=20, choices=TIME_SLOT_CHOICES,
    null=True, blank=True)
reminder_sent = models.BooleanField(default=False,
    help_text="24-hour reminder sent to tech")

# Rescheduling tracking
original_scheduled_date = models.DateTimeField(null=True, blank=True)
reschedule_count = models.IntegerField(default=0)
reschedule_reason = models.TextField(null=True, blank=True)
```

**Time Slot Choices:**
```python
TIME_SLOT_CHOICES = [
    ('MORNING', 'Morning (8am-12pm)'),
    ('AFTERNOON', 'Afternoon (12pm-5pm)'),
    ('EVENING', 'Evening (5pm-8pm)'),
    ('SPECIFIC', 'Specific Time'),  # Uses scheduled_date time
]
```

### API Endpoints

**Required:**
- `GET /api/orders/scheduled/` - List all scheduled orders (with date filtering)
- `GET /api/orders/due-today/` - Get orders scheduled for today
- `GET /api/orders/overdue/` - Get orders past their scheduled time
- `POST /api/orders/{id}/reschedule/` - Reschedule an order
- `GET /api/calendar/orders/` - Get orders for calendar view (month range)

**Query Parameters:**
- `?scheduled_date__gte=2025-10-01` - Orders scheduled after date
- `?scheduled_date__lte=2025-10-31` - Orders scheduled before date
- `?time_slot=MORNING` - Filter by time slot
- `?is_overdue=true` - Only overdue orders

### UI Requirements

**Customer Portal - Order Form:**
```
┌─────────────────────────────────────────┐
│  New Order                              │
├─────────────────────────────────────────┤
│  Tank: [Dropdown: My Tanks]             │
│  Service: [Dropdown: Air / Nitrox]      │
│                                         │
│  ☐ Schedule for later                  │
│                                         │
│  [When checked, shows:]                 │
│  ┌─────────────────────────────────┐   │
│  │ Date: [📅 10/15/2025]           │   │
│  │ Time: ⦿ Morning (8am-12pm)      │   │
│  │       ○ Afternoon (12pm-5pm)    │   │
│  │       ○ Evening (5pm-8pm)       │   │
│  │       ○ Specific: [🕐 3:00 PM]  │   │
│  └─────────────────────────────────┘   │
│                                         │
│  [Place Order]  [Cancel]                │
└─────────────────────────────────────────┘
```

**Technician Dashboard - Due Today Widget:**
```
┌─────────────────────────────────────────┐
│  Orders Due Today (3)                   │
├─────────────────────────────────────────┤
│  🔴 OVERDUE                             │
│  ORD-2025-00042 | John Doe              │
│  Scheduled: Today 10:00 AM              │
│  [Start Now]                            │
│  ─────────────────────────────────────  │
│  🟡 DUE SOON                            │
│  ORD-2025-00055 | Ocean Divers          │
│  Scheduled: Today 2:00 PM (in 3 hrs)    │
│  [Start]                                │
│  ─────────────────────────────────────  │
│  🟢 SCHEDULED                           │
│  ORD-2025-00060 | Jane Smith            │
│  Scheduled: Today 6:00 PM               │
│  [View]                                 │
└─────────────────────────────────────────┘
```

**Calendar View (Optional - Phase 1):**
```
┌────────────────────────────────────────────────────────┐
│  October 2025                          [Month][Week]    │
├───┬───┬───┬───┬───┬───┬───────────────────────────────┤
│Sun│Mon│Tue│Wed│Thu│Fri│Sat                            │
├───┼───┼───┼───┼───┼───┼───────────────────────────────┤
│   │   │   │ 1 │ 2 │ 3 │ 4                             │
│   │   │   │   │ • │ • │ •                             │
│   │   │   │   │ 2 │ 1 │ 3 ← Order count               │
├───┼───┼───┼───┼───┼───┼───────────────────────────────┤
│ 5 │ 6 │ 7 │ 8 │ 9 │10 │11                             │
│ • │   │ • │ • │ • │ • │ •                             │
│ 1 │   │ 3 │ 2 │ 5 │ 4 │ 2                             │
├───┼───┼───┼───┼───┼───┼───────────────────────────────┤
│Click date to see orders scheduled for that day          │
└────────────────────────────────────────────────────────┘
```

### Notifications (Integration with EPOCH 2)

**Email Templates:**
1. **Order Scheduled Confirmation** (to customer)
   - Subject: "Order #XXX Scheduled for [Date]"
   - Body: "Your order for [service] on [tanks] is scheduled for [date] at [time]"

2. **Reschedule Confirmation** (to customer)
   - Subject: "Order #XXX Rescheduled"
   - Body: "Your order has been rescheduled from [old date] to [new date]"

3. **Reminder to Technician** (24 hours before)
   - Subject: "[X] Orders Due Tomorrow"
   - Body: List of scheduled orders for tomorrow

**SMS Templates:**
- "Order #XXX scheduled for [date] at [time]. See details: [link]"
- "Reminder: Order #XXX due tomorrow at [time]"

---

## Definition of Done

### Development Complete
- [ ] ServiceOrder model updated with scheduling fields
- [ ] Database migration created and applied
- [ ] Scheduled order API endpoints (5 new endpoints)
- [ ] Customer portal: Schedule order form with date/time picker
- [ ] Technician dashboard: "Due Today" widget
- [ ] Order list: Filter by scheduled/walk-in
- [ ] Reschedule functionality working
- [ ] Overdue order detection and display

### Testing Complete (>95% Coverage)
- [ ] Unit tests: Scheduling model validation (6 tests)
- [ ] Unit tests: Overdue detection logic (4 tests)
- [ ] API tests: Schedule order endpoint (8 tests)
- [ ] API tests: Reschedule order (6 tests)
- [ ] API tests: Due today / overdue queries (6 tests)
- [ ] Integration tests: Schedule → notification → due today workflow (5 tests)
- [ ] UI tests: Date picker validation (3 tests)
- [ ] Edge case: Schedule for past date (rejected)
- [ ] Edge case: Reschedule multiple times
- [ ] Edge case: Overdue order still completable

### Quality Assurance
- [ ] Date picker only allows future dates
- [ ] Time slots display correctly in customer's timezone
- [ ] Due today widget auto-refreshes (shows new scheduled orders)
- [ ] Overdue orders visually distinct (red/warning)
- [ ] Notifications sent at correct times (24h before, on completion)
- [ ] Calendar view (if implemented) loads in <2 seconds

### Documentation
- [ ] API endpoints documented (scheduling endpoints)
- [ ] User guide: "How to schedule an order"
- [ ] User guide: "How to reschedule an order"
- [ ] Technician guide: "Managing scheduled orders"
- [ ] Scheduling workflow diagram

---

## Test Scenarios

### Happy Path
1. **Schedule Order from Portal**
   - Customer logs into portal
   - Creates new order (1 tank, nitrox)
   - Checks "Schedule for later"
   - Selects date: October 15, 2025
   - Selects time slot: Afternoon (12pm-5pm)
   - Clicks "Place Order"
   - Order created with status=PENDING, is_scheduled=true
   - Confirmation email/SMS sent to customer

2. **Technician Sees Scheduled Order**
   - October 15, technician opens dashboard
   - "Due Today" widget shows order
   - Technician clicks order
   - Starts filling tank
   - Completes order
   - Customer receives completion notification

3. **Customer Reschedules**
   - Customer views scheduled order (Oct 15)
   - Clicks "Reschedule"
   - Changes date to October 17
   - Confirms reschedule
   - Receives confirmation: "Rescheduled to Oct 17"
   - Technician sees note in order history

### Edge Cases
4. **Try to Schedule Past Date**
   - Customer selects yesterday's date
   - System shows error: "Date must be in future"
   - Order not created

5. **Overdue Order**
   - Order scheduled for Today 10:00 AM
   - Current time: Today 2:00 PM
   - Order appears in "Due Today" with RED overdue indicator
   - Technician can still complete order
   - Overdue flag clears when order starts

6. **Reschedule Multiple Times**
   - Customer reschedules order 3 times
   - System tracks: reschedule_count=3
   - Each reschedule sends new confirmation
   - History shows: "Original: Oct 10 → Oct 12 → Oct 15 → Oct 17"

### Notification Tests
7. **24-Hour Reminder**
   - Order scheduled for Tomorrow 2:00 PM
   - At Today 2:00 PM, reminder job runs
   - Technician receives email: "1 Order Due Tomorrow"
   - reminder_sent flag set to true (prevents duplicate)

8. **Due Today Auto-Queue**
   - Order scheduled for Today (any time)
   - At midnight, scheduled orders become "due today"
   - Appear in "Due Today" widget
   - Remain until completed or day ends

---

## Dependencies

**Requires:**
- EPOCH 1 complete (ServiceOrder model exists)
- EPOCH 2 in progress (Customer portal, notifications)
- Email/SMS notification system (from S-008)

**Enables:**
- EPOCH 3 delivery time slot scheduling (similar pattern)
- Future recurring order feature (v2.0)
- Calendar integration (EPOCH 4 or later)

---

## Notes for Implementation

### Date/Time Handling
- **Always use timezone-aware datetimes** (Django timezone.now())
- **Store in UTC**, display in shop's configured timezone
- **Customer timezone**: Use shop timezone (assume local customers)
- **Time slots map to ranges**:
  - MORNING: 08:00-12:00
  - AFTERNOON: 12:00-17:00
  - EVENING: 17:00-20:00

### Overdue Detection
```python
@property
def is_overdue(self):
    """Order is overdue if scheduled_date has passed and not completed"""
    if not self.is_scheduled or not self.scheduled_date:
        return False
    if self.status in ['COMPLETED', 'PAID']:
        return False
    return timezone.now() > self.scheduled_date
```

### Due Today Query
```python
def get_due_today():
    """Get all orders scheduled for today"""
    today = timezone.now().date()
    return ServiceOrder.objects.filter(
        is_scheduled=True,
        scheduled_date__date=today,
        status__in=['PENDING', 'IN_PROGRESS']
    ).order_by('scheduled_date')
```

### Reminder Job (Celery)
```python
@shared_task
def send_scheduled_order_reminders():
    """Run daily at 2pm: Send reminders for tomorrow's orders"""
    tomorrow = timezone.now() + timedelta(days=1)
    orders = ServiceOrder.objects.filter(
        is_scheduled=True,
        scheduled_date__date=tomorrow.date(),
        reminder_sent=False,
        status='PENDING'
    )

    for order in orders:
        send_reminder_email(order)
        order.reminder_sent = True
        order.save()
```

### Calendar View Data (Optional)
```python
# API endpoint for calendar
@api_view(['GET'])
def calendar_orders(request):
    """Get orders for calendar view (month range)"""
    start_date = request.query_params.get('start')  # 2025-10-01
    end_date = request.query_params.get('end')      # 2025-10-31

    orders = ServiceOrder.objects.filter(
        is_scheduled=True,
        scheduled_date__range=[start_date, end_date]
    ).values('id', 'order_number', 'scheduled_date', 'customer__name')

    # Format for calendar library (FullCalendar.js)
    events = [{
        'id': order['id'],
        'title': f"{order['order_number']} - {order['customer__name']}",
        'start': order['scheduled_date'],
        'url': f'/orders/{order["id"]}/'
    } for order in orders]

    return Response(events)
```

### Frontend Libraries
- **Date Picker**: react-datepicker (simple, lightweight)
- **Calendar View** (optional): FullCalendar.js or react-big-calendar
- **Time Picker**: Built into react-datepicker

---

## Future Enhancements (Post-EPOCH 2)

**EPOCH 3+:**
- Recurring scheduled orders (e.g., "Every Friday 3pm")
- Capacity limits per time slot (max 10 orders/hour)
- Automated tank assignment (system assigns available tanks)
- Technician availability scheduling
- Drag-and-drop calendar rescheduling

**EPOCH 4+:**
- Full calendar dashboard (month/week/day views)
- Google Calendar sync (export scheduled orders)
- iCal export
- Conflict detection (double-booking prevention)

---

## Estimated Effort Breakdown

**Total: 3 hours**

- Model changes & migrations: 0.5 hours
- API endpoints (5 new): 1 hour
- Customer UI (date picker, reschedule): 1 hour
- Technician UI (due today widget): 0.5 hours
- Testing: Included in overall EPOCH 2 testing

---

**Story Status**: Ready for SPEC approval
**Next Steps**: Include in EPOCH 2 scope (adjust to 23 hours total or trim elsewhere)

---

## Integration with Other EPOCH 2 Stories

**Works With:**
- **S-006 (Customer Portal)**: Scheduling form integrated into portal
- **S-008 (Notifications)**: Uses same email/SMS infrastructure
- **S-007 (Self-Service Orders)**: Adds scheduling option to order placement

**Calendar feature complements:**
- Advance order placement (customer convenience)
- Reduces phone call volume (business goal)
- Professional scheduling UX (competitive advantage)
