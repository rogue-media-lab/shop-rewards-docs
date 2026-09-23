# Carus — Strategic Direction Update
**Date:** March 24, 2026
**Status:** Active Planning
**Session:** Strategic pivot discussion with Claude

---

## Product Name

Working title: **Carus** (internally). "Shop Rewards" remains the name for the shop-facing side of the platform.

---

## The Pivot

The original Shop Rewards proposal was built for Speedee Oil Change corporate as a B2B franchise tool. **Speedee passed.** Rather than shelve the work, the decision was made to expand the concept and sell it independently — both to consumers and to shops directly.

This is not a setback. Corporate franchise decisions are slow and political. Going independent removes that bottleneck entirely and opens a larger market.

---

## The Expanded Vision

Carus is no longer just a shop loyalty tool. It is a **two-sided consumer vehicle platform with shop integration.**

### Consumer Side (free base, paid premium)
- **VIN decoder** — enter VIN, get year/make/model auto-filled (NHTSA free API)
- **Maintenance schedule** — universal service intervals seeded into the app (oil, brakes, belts, filters, battery, fluids, spark plugs, etc.)
- **Maintenance history log** — track what's been done, when, and at what mileage
- **Vehicle document store** — purchase your vehicle's OEM service PDF; AI uses it as context for vehicle-specific answers without a heavy AI interface
- **Curated coupon wallet** — major chain coupons (Valvoline, Jiffy Lube, Firestone, etc.) aggregated in one place
- **Flash sale alerts** — nearby participating shops can push time-sensitive offers to the consumer
- **AI vehicle assistant** — powered by the purchased PDF + Claude (Haiku-level, low cost); doesn't need to look like AI, just looks like accurate information about their specific car

### Shop Side (paid subscription)
- **Flash sale bay-filler** — already largely built
- **Customer vehicle visibility** — see what the customer drives and what's due
- **Targeted flash sales** — send offers based on actual maintenance gaps, not just "everyone gets 50% off"
- **Manager dashboard** — already built
- **Tech tools** — vehicle history, service notes (future)

---

## Why This Works

Every existing app does one side:
- FIXD: diagnostics only, requires hardware dongle
- Carfax Car Care: maintenance tracking, no shop connection
- RepairPal: shop finder, no loyalty or alerts
- Single-brand apps (Valvoline, Jiffy Lube): locked to one chain

**Carus does both.** The consumer brings their vehicle data. The shop sees what the car needs. Flash sales become targeted — not "50% off oil changes to everyone," but "your Civic is 800 miles overdue, here's 30% off." That is a fundamentally more useful offer.

### The Business Logic
```
Consumer installs free app → enters vehicle + tracks maintenance
         ↓
Consumer becomes valuable to shops (known vehicle + maintenance gaps)
         ↓
Shop pays for access to that consumer + flash sale tools
         ↓
Consumer gets better, targeted offers
         ↓
More consumers join because the free app is genuinely useful
```

This is a two-sided marketplace. Each side makes the other more valuable.

---

## Architecture Decision

**Single Rails app. One codebase. One database. Roles-based access.**

Reasons:
- One Heroku instance = one hosting bill
- Shared database = vehicle data instantly visible to shops, zero API calls
- Rails namespaced routes + Devise scopes handle separation cleanly
- PWA is already configured in the existing app
- Easier to build and maintain solo

The two user types already exist in the codebase:
- `User` — consumer (Devise, phone-based auth)
- `AdminUser` — shop manager (Devise, email auth, PIN for redemptions)

### Namespace Structure
```
controllers/
├── consumer/       ← new (vehicle, maintenance, coupons, alerts)
├── manager/        ← already exists (dashboard, customers, flash_alerts)
└── pages/          ← already exists (home, tos, privacy)
```

---

## Critical Schema Fix Required

**Current problem:** `users.shop_id` locks every consumer to one shop permanently.

**Carus problem:** A consumer is not "owned" by a shop. They are an independent car owner who may receive alerts from nearby shops based on proximity or preference.

**Fix:** Make `users.shop_id` nullable via migration. The shop relationship for consumers becomes optional/preference-based, not required at signup.

---

## New Models Required

```ruby
Vehicle
  - user_id (belongs_to user)
  - vin (string, optional)
  - year, make, model (string)
  - current_mileage (integer)
  - nickname (string, optional — "My Truck")
  - has_one_attached :document (PDF via Active Storage)

MaintenanceItem
  - name (string — "Oil Change", "Brake Flush", etc.)
  - interval_miles (integer)
  - interval_months (integer)
  - description (text)
  - icon_name (string)
  # Seeded data — universal schedule, ~15-20 core services

MaintenanceRecord
  - vehicle_id
  - maintenance_item_id (optional — can be free-form)
  - service_name (string)
  - performed_at (date)
  - mileage_at_service (integer)
  - notes (text)
  - shop_name (string, optional)

ExternalCoupon
  - chain_name (string — "Valvoline", "Jiffy Lube", etc.)
  - title (string)
  - description (text)
  - discount_value (string)
  - terms (text)
  - expires_at (datetime, nullable)
  - source_url (string)
  # Manually curated, seeded and maintained
```

The existing models (`FlashAlert`, `Redemption`, `Service`, `Shop`, `AdminUser`) remain unchanged.

---

## Revenue Model

| Tier | Who | Price | What They Get |
|---|---|---|---|
| Free | Consumer | $0 | VIN decode, maintenance schedule, history log, chain coupons |
| Premium | Consumer | $4–8/month | Multi-vehicle, AI assistant, advanced reminders |
| Vehicle PDF | Consumer | $3–5 one-time | OEM service manual for their specific vehicle |
| Shop Basic | Shop | $75–100/month | Flash sales, consumer alerts |
| Shop Pro | Shop | $150–200/month | Vehicle history visibility, targeted flash sales |

---

## Build Order

### Phase 1 — Consumer Core (launches first)
1. Migration: make `users.shop_id` nullable
2. `Vehicle` model + consumer namespace controller
3. Seed `MaintenanceItem` table (universal schedule)
4. `MaintenanceRecord` model + log UI
5. NHTSA VIN decoder API integration (free)
6. Consumer dashboard — one vehicle, at a glance
7. Active Storage PDF on Vehicle (document store)
8. `ExternalCoupon` table seeded with major chain coupons

### Phase 2 — Connect the Two Sides
9. Flash alerts visible to consumers (nearby shops)
10. Push notifications via OneSignal
11. Shop visibility into consumer vehicle + maintenance history
12. Targeted flash sales based on maintenance gaps

### Phase 3 — Monetization
13. Consumer premium tier (Stripe subscription)
14. Shop paid subscription
15. PDF store checkout (Stripe one-time)

---

## Design Philosophy

**"More Steve Jobs, less Linus Torvalds."**

Design is a genuine competitive moat here. Carfax Car Care, FIXD, and most automotive apps have mediocre UI. A beautifully built PWA with clear visual hierarchy, satisfying progress indicators, and a calm, purposeful aesthetic would stand out immediately.

Consumer apps win or lose on feel. The maintenance tracking space has never had an app that people actually *want* to open.

---

## What's Already Built (Current State)

The existing `shop-rewards` Rails app has:
- Devise auth for both User and AdminUser
- Shop model with branding (primary/secondary color)
- FlashAlert model — solid (barcode generation, expiration, active scope, redemption tracking)
- Redemption model — polymorphic
- Service model (shop service catalog)
- Manager namespace controllers (dashboard, customers, flash_alerts)
- Countdown Stimulus controller
- PWA manifest and service worker configured
- Pages: home, ToS, privacy

**The foundation is real. This is an extension, not a rebuild.**

---

## Next Session Agenda

- Finalize `Vehicle` model schema
- Write the `users.shop_id` nullable migration
- Decide on NHTSA VIN API integration approach
- Scaffold consumer namespace and vehicle controller
- Discuss `MaintenanceItem` seed data structure
