# NamNam: Food Delivery & Ordering Platform (PRD)

A scalable, multi-region platform that connects customers, restaurants, delivery agents, and admins. Orders and money must be strongly consistent, and every role has clear ownership.

## Roles

| Role | Can do |
|---|---|
| **Customer** | Browse restaurants and menus, manage cart, place order, pay (Online / COD), track order, view history |
| **Restaurant Owner** | Manage profile, menu, prices; view all orders, balance, payouts; assign/revoke staff roles |
| **Restaurant Manager** | Manage menu; accept/reject orders; update order status; view orders and basic analytics |
| **Restaurant Staff** | View incoming orders; update preparation status only |
| **Delivery Agent** | Accept/reject tasks; set status (Assigned, Picked up, Delivered); view history and earnings (read-only) |
| **Admin** | Manage restaurants and agents; view all orders; monitor payments; record payouts; reports; manual overrides |

> RBAC is enforced **per restaurant**, not globally.

## Order Lifecycle

1. Customer places order
2. Payment: **Online** (must be authorized first) or **COD** (no pre-payment)
3. Order sent to restaurant
4. Restaurant accepts or rejects
5. Order is prepared
6. Delivery agent is assigned
7. Order is picked up
8. Order is delivered
9. Financial settlement is recorded

## Delivery Assignment

- **Automatic (default):** based on proximity and agent availability.
- **Manual override (restaurant):** reassign agent, handle edge cases.
- Agents can accept or reject (limited retries).

## Payments & Money Flow

- **Online:** authorize before confirmation, idempotent handling, order created only after confirmed payment.
- **COD:** order created without payment, agent collects cash.
- **Restaurant balance:** grows when an order is delivered; platform commission is deducted automatically.
- **Payouts:** paid externally (bank transfer). Admin records the payout, balance is reduced, full history is kept.

## Non-Functional Requirements

| Area | Target |
|---|---|
| Availability | High (orders and payments are critical) |
| Latency | Browsing < 1s, checkout < 1-3s |
| Consistency | Strong for orders and payments; eventual for analytics |
| Scalability | Must handle peak (meal-time) traffic |
| Security | Strong authorization boundaries, secure payment handling |
| Multi-region | Active traffic in many regions, regional reads, strong consistency where required |

## Capacity (Back-of-the-Envelope)

| Metric | Value |
|---|---|
| MAU / DAU | 1M / 200K |
| Actions per user per day | 27 |
| Requests per day | ~5.4M |
| Avg / Peak RPS | ~62 / ~620 |
| Read / Write | 85% / 15% |
| Order storage growth | ~146 GB / year |
| Bandwidth | ~270 GB / day |

## Data Retention

- **Orders and payments:** kept long-term. Apps show the current year only; older data goes to separate storage.
- **Logs:** 30 days.
- **Analytics:** aggregated and archived.

## Multi-Region Assumptions

- Users are served from the nearest region.
- Each restaurant has a primary region.
- Orders are processed in the restaurant's region.
- Financial data requires strong consistency.

## Risks

Meal-time spikes, payment provider failures, delivery agent shortages, cross-region consistency, settlement reconciliation errors.

## Success Criteria

- Orders complete end-to-end without loss
- Payments are correct and auditable
- Restaurant balances are accurate
- Stable under peak load
- Clear ownership and authorization boundaries

## Out of Scope

Recommendation systems, loyalty programs, AI-based delivery optimization, reviews.
