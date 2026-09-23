# Proposal Addendum: Version 1.1

**Project:** Shop Rewards - Proprietary Loyalty Platform
**Revision Date:** October 31, 2025
**Addendum Purpose:** Document proposal evolution and architectural enhancements through review process

---

## Purpose of This Document

This addendum serves as a **revision log** for the Shop Rewards proposal, documenting all changes made during the review and planning process. It ensures transparency with stakeholders and provides a clear audit trail of how the proposal evolved from initial concept to final submission.

**Why This Matters:**
- Demonstrates rigorous planning and review process
- Shows responsiveness to architectural insights and risk analysis
- Provides context for why certain decisions were made
- Serves as internal documentation for future reference

---

## Version History

| Version | Date | Status | Major Changes |
|---|---|---|---|
| **1.0** | November 2025 | Initial Draft | Original 8-document proposal submitted |
| **1.1** | October 31, 2025 | **Current** | Multi-franchise architecture enhancement |
| Future | TBD | Planned | Corporate feedback integration |

---

## Revision 1.1: Location-Based Multi-Tenant Architecture

**Revision Date:** October 31, 2025
**Trigger:** Comprehensive proposal review and architectural analysis
**Scope:** Major enhancement to support multi-franchise deployment

### Executive Summary of Changes

**What Changed:**
The proposal has been enhanced from a **single-franchise application** to a **location-based multi-tenant platform** that supports multiple franchise brands (Speedee and Grease Monkey) simultaneously within a single codebase.

**Why This Matters:**
Many franchise owners operate BOTH Speedee and Grease Monkey locations. The enhanced architecture allows one owner to manage all their shops (regardless of franchise brand) from a single owner dashboard, while maintaining complete data isolation between locations and franchise-specific branding.

**Impact:**
- **Increased value** for same $10,000 development cost
- **Broader market opportunity** (can serve mixed-franchise portfolios)
- **Future licensing potential** (platform can be licensed to other automotive franchises)
- **No timeline impact** (still deliverable within 6-week MVP timeline)

---

## Architectural Enhancements Detail

### 1. Database Schema Updates

**What Was Added:**

**New Tables:**
```sql
franchises
  - Stores franchise brand data (Speedee, Grease Monkey)
  - Contains branding assets (logo_url, primary_color, secondary_color)

shops
  - Individual shop locations linked to franchise
  - Contains location data (address, city, state, zip)
  - Includes qr_code_token for unique signup URLs

shop_assignments
  - Join table linking AdminUsers to Shops
  - Enables managers to access ONE shop
  - Enables owners to access MULTIPLE shops
```

**Updated Tables:**
```sql
users
  - Added: preferred_shop_id (links customer to one shop)

coupons (flash sales)
  - Added: shop_id (flash sales are shop-specific)
  - Added: created_by_admin_id (audit trail)

admin_users
  - Changed: role enum now includes :corporate
  - Removed: location_id (replaced by shop_assignments)

reward_transactions (new approach)
  - Replaced reward_redemptions table
  - More flexible transaction system for points
```

**Rationale:**
- Original schema assumed one brand, one owner
- Enhanced schema supports multi-shop, multi-franchise owners
- Maintains data isolation while enabling aggregate reporting

---

### 2. Three-Tier Authentication System

**Original Design:**
- Two tiers: Customers and Admins
- All admins had equal access

**Enhanced Design:**
- **Tier 1: Customers** (`/`) - One user account, one shop
- **Tier 2: Shop Managers** (`/manager`) - Assigned to ONE shop via shop_assignments
- **Tier 3: Owners** (`/owner`) - Can view MULTIPLE shops they own
- **Tier 4: Corporate** (`/corporate`) - Can view ALL pilot shops (for analytics)

**Key Features:**
- **Managers** see only their shop's customers, can only send flash sales to their shop
- **Owners** see aggregate metrics across all owned shops, can compare performance
- **Corporate** sees all shops across both franchises for pilot evaluation

**Rationale:**
- Original design didn't account for multi-location owners
- Enhanced design matches real-world franchise ownership structure
- Enables proper data isolation and security

---

### 3. Location-Based Signup Flow

**Original Flow:**
1. Visit website
2. Enter phone number and password
3. Complete signup

**Enhanced Flow:**
1. Visit website OR scan shop-specific QR code
2. Enter phone number and password
3. **If from website**: Enter zip code → See nearby shops → Select preferred shop
4. **If from QR code**: Shop auto-selected (skip step 3)
5. TCPA opt-in checkbox shows shop name
6. Complete signup → Branding adapts to shop's franchise

**Key Features:**
- **QR Code System**: Each shop gets unique URL (`/signup?shop=abc123xyz`)
- **Nearby Shop Finder**: Uses zip code to show shops within radius
- **Instant Shop Selection**: QR codes eliminate selection friction
- **Dynamic Branding**: User sees Speedee colors if they select Speedee shop

**Rationale:**
- Original design didn't explain how users choose their shop
- QR codes provide highest conversion rate for in-store signups
- Location-based approach prevents franchise branding conflicts

---

### 4. Dynamic Franchise Branding

**Original Design:**
- Generic "Shop Rewards" or "Auto Perks" branding
- Same look for all users

**Enhanced Design:**
- Customer sees their shop's franchise branding
- CSS variables injected based on `user.shop.franchise_id`
- Speedee customer sees Speedee logo and purple theme
- Grease Monkey customer sees Grease Monkey logo and brand colors
- Same codebase, franchise-specific experience

**Implementation:**
```ruby
# Simplified example
user.shop.franchise.primary_color   # "#8B5CF6" (Speedee)
user.shop.franchise.logo_url        # "/assets/speedee-logo.png"
```

**Rationale:**
- Customers expect to see their shop's branding
- Dynamic approach avoids franchise agreement violations
- One codebase serves multiple brands (efficient)

---

### 5. Shop-Scoped Data Isolation

**What Changed:**
ALL data queries are now scoped to shops:

- **Flash Sales**: Created by manager → Sent only to customers of that shop
- **Customer Lookup**: Manager searches → Sees only their shop's customers
- **Rewards**: Earned at Shop A → Cannot be redeemed at Shop B (MVP)
- **Dashboard Metrics**: Manager sees their shop only, Owner sees their shops only

**Example:**
```ruby
# Manager at Speedee Main Street sends flash sale
# Only customers where user.preferred_shop_id == speedee_main_st_id receive notification
# Grease Monkey customers never see this flash sale
```

**Rationale:**
- Prevents data leakage between shops
- Enables franchise-specific loyalty programs
- Maintains competitive separation (Speedee vs Grease Monkey)

---

## Documents Updated in Revision 1.1

### Document 01: Executive Summary

**Lines Updated:** 66-71, 73-79, 89-95, 97-127, 140-203, 211-222

**Key Changes:**
- Added shop selection to signup flow (step 2-4)
- Added "Location-Based Multi-Tenancy" to unique features
- Added "QR Code Instant Signup" to unique features
- Added "Three-Tier Admin Access" to unique features
- Updated all customer features to emphasize "YOUR shop" scoping
- Updated all manager features to clarify "THIS shop" scoping
- **Added new section**: Owner/Corporate Features (Multi-Shop Management)
- Added cross-shop rewards to excluded MVP features with post-MVP note

**Impact:** Stakeholders now understand this is a multi-franchise platform, not a single-brand app

---

### Document 02: Market Analysis

**Lines Added:** Section to be added in this revision after line 264

**Key Changes:**
- **Adding**: Multi-franchise pilot validation section
- **Adding**: Explanation of why pilot includes both Speedee and Grease Monkey
- **Adding**: Strategic value of multi-franchise architecture
- **Adding**: Potential to license to other automotive franchises

**Impact:** Corporate understands the broader market opportunity and strategic value

---

### Document 03: Product Overview

**Lines Updated:** 81-86, 115-220, 230-239

**Key Changes:**
- Updated signup flow to include shop selection (steps 3-4) and QR code option
- Rewrote Role 2 (Shop Manager) to clarify ONE shop scope with `/manager` portal
- Rewrote Role 3 (Franchise Owner) → "Multi-Shop Manager" with example dashboard showing Speedee vs Grease Monkey metrics
- Added PWA-102 (Shop Selection), PWA-103 (QR Code Signup), renumbered subsequent features
- Updated feature descriptions to emphasize shop-scoping

**Impact:** Product design now reflects location-based multi-tenant architecture

---

### Document 04: Technical Specifications

**Lines Updated:** 72-180, 182-284

**Key Changes:**
- **Completely revised database schema** with Franchise, Shop, ShopAssignment tables
- **Added comprehensive new section**: Multi-Tenant Architecture: Location-Based Isolation (90+ lines)
- Documented three-tier admin tenancy (Manager/Owner/Corporate)
- Documented QR code system with examples
- Documented franchise branding implementation
- Documented scaling path and post-MVP enhancements
- Updated performance optimization indexes

**Impact:** Technical architecture is now accurately documented for development

---

### Document 05: Cost Breakdown

**Lines to Update:** 72-89, 266-274

**Key Changes:**
- **Updating**: Itemized development hours to reflect multi-tenant complexity
- **Updating**: Revised hour estimate (154 → 220 hours) with justification
- **Maintaining**: $10,000 fixed price (increased value, not increased cost)
- **Adding**: Multi-franchise ROI scenarios (Speedee vs Grease Monkey performance)
- **Adding**: Post-pilot pricing examples for multi-shop owners

**Impact:** Cost justification is accurate and transparent about increased complexity

---

### Document 06: Implementation Plan

**Lines to Update:** Throughout Weeks 1-6, demo script, pilot setup

**Key Changes:**
- **Week 1**: Adding Franchise and Shop model development
- **Week 2**: Clarifying shop-scoped admin dashboard
- **Week 3**: Adding shop selection flow implementation
- **Week 4**: No changes (VIN scanner and push notifications)
- **Week 5**: Adding dynamic branding system implementation
- **Week 6**: Updating demo script to showcase multi-franchise capability
- **Pilot Setup**: Adding owner vs manager account creation distinction
- **Demo Script**: Showcasing shop-scoped flash sales and owner dashboard

**Impact:** Timeline remains 6 weeks but with accurate task breakdown

---

### Document 07: Risk Analysis

**Lines to Update:** 24-68, adding new risks

**Key Changes:**
- **Updating**: RISK 1.1 (Franchise Agreement) mitigation to reflect dynamic branding
- **Adding**: RISK 2.5: Shop data isolation failure
- **Adding**: RISK 2.6: Authorization bug (manager accessing wrong shops)
- **Adding**: RISK 3.5: Branding conflict between franchises
- **Adding**: RISK 3.6: Cross-shop reward redemption attempts

**Impact:** Risk mitigation now covers multi-tenant security and data isolation

---

### Document 08: Conclusion

**Lines to Update:** Demo script, success criteria, Q&A

**Key Changes:**
- Updating demo script to showcase multi-franchise capability
- Adding success criterion: "Validate multi-franchise architecture"
- Updating Q&A to address dual-franchise questions
- Emphasizing strategic value of multi-franchise platform

**Impact:** Final ask now reflects the enhanced value proposition

---

### Document 00: README

**Lines to Update:** 135-150, 206-214

**Key Changes:**
- Updating "What Makes This Unique" to mention multi-franchise support
- Updating "What's Included in MVP" to mention shop selection and QR codes
- Adding version history section referencing this addendum
- Adding note about architectural enhancements in v1.1

**Impact:** Navigation document accurately describes enhanced proposal

---

## Development Complexity Analysis

### Original Estimate (Version 1.0)
- **Total Hours:** 154 hours
- **Itemized Cost:** $6,160
- **Rounded Price:** $10,000
- **Buffer:** $3,840 (62%)

### Revised Estimate (Version 1.1)
- **Additional Hours for Multi-Tenant Architecture:**
  - Franchise model & seed data: 6 hours
  - Shop model with QR code generation: 8 hours
  - ShopAssignment join table & CRUD: 6 hours
  - Shop selection signup flow: 8 hours
  - Shop-scoped queries (all controllers): 12 hours
  - Dynamic branding system: 10 hours
  - Owner dashboard (multi-shop view): 12 hours
  - Three-portal routing: 4 hours
- **Additional Hours Total:** 66 hours
- **New Total Hours:** 220 hours
- **New Itemized Cost:** $8,800
- **Maintained Price:** $10,000
- **New Buffer:** $1,200 (14%)

### Why Price Remains $10,000

**Value Proposition:**
- Corporate receives a **multi-franchise platform** for the price of a single-brand app
- Platform can serve mixed Speedee/Grease Monkey portfolios (broader market)
- Owner dashboard capability adds significant value
- Future licensing potential increases ROI

**Developer Perspective:**
- Original buffer (62%) was conservative
- Standard industry buffer is 20-30%
- New buffer (14%) is reasonable for fixed-price project
- Reduced buffer is offset by AI-assisted development efficiency
- Price increase would delay decision/approval process

**Strategic Decision:**
- Maintaining $10k price point demonstrates good faith
- Enhanced value without price increase strengthens proposal
- Competitive advantage: Still 70-90% cheaper than alternatives

---

## Impact on Timeline

### Original Timeline (Version 1.0)
- 6 weeks to MVP (30 business days)
- Demo: End of Week 6
- Pilot Launch: Week 9 (2 months from contract approval)

### Revised Timeline (Version 1.1)
- **No change**: Still 6 weeks to MVP
- **No change**: Demo still at end of Week 6
- **No change**: Pilot launch still Week 9 (2 months from contract approval)

### How Timeline Remains Unchanged

**Efficiency Gains:**
- AI-assisted development (Cursor, GitHub Copilot) accelerates coding
- Rails 8 generators reduce boilerplate
- Standard patterns for multi-tenancy are well-documented

**Task Parallelization:**
- Franchise/Shop models built simultaneously with User models (Week 1)
- Dynamic branding developed during UI polish phase (Week 5)
- Owner dashboard shares code with Manager dashboard (Week 2)

**Reduced Scope Elsewhere:**
- VIN scanner complexity already in timeline
- Push notification system unchanged
- Testing phase has built-in buffer (Week 6)

---

## Impact on Success Criteria

### Added Success Criterion (Revision 1.1)

**New Criterion:**
✅ **Multi-Franchise Validation:** Both Speedee AND Grease Monkey pilot shops show positive metrics (minimum 10% increase in car count, 15% flash sale redemption rate)

**Why This Matters:**
- Proves architecture works for multiple franchise brands
- Demonstrates cross-franchise scalability
- De-risks enterprise rollout to both franchise networks

**What If One Franchise Performs Better?**
- Expected: Different franchises may have different baseline performance
- Acceptable: One franchise shows 20% lift, other shows 8% lift (still validates concept)
- Unacceptable: One franchise shows 0% or negative impact (requires investigation)

**Reporting:**
- Pilot reports will show franchise-specific metrics
- Owner dashboard will enable real-time comparison
- Post-pilot analysis will include franchise-level ROI breakdown

---

## Risk Profile Changes

### New Risks Introduced (Revision 1.1)

**Technical Risks:**
- **RISK 2.5**: Shop data isolation failure (Severity: HIGH, Probability: LOW)
  - Mitigation: Comprehensive integration tests, authorization checks in all controllers

- **RISK 2.6**: Authorization bug (Severity: HIGH, Probability: LOW)
  - Mitigation: Role-based access control (RBAC) with Pundit gem, automated tests

**Business Risks:**
- **RISK 3.5**: Branding conflict (Severity: MEDIUM, Probability: LOW)
  - Mitigation: Dynamic CSS variables, franchise-specific asset loading, QA testing per brand

- **RISK 3.6**: Cross-shop reward attempts (Severity: LOW, Probability: MEDIUM)
  - Mitigation: Shop-scoped reward lookups, clear error messages, customer education

### Overall Risk Assessment

**Pre-Revision:** 2 MEDIUM risks, 8 LOW risks, 0 unmitigated critical risks
**Post-Revision:** 4 MEDIUM risks, 10 LOW risks, 0 unmitigated critical risks

**Verdict:** Risk profile remains **favorable**. Added complexity introduces new risks, but all are mitigated through architecture, testing, and established patterns.

---

## Stakeholder Communication

### What Corporate Needs to Know

**Summary for Decision-Makers:**
> "During proposal review, we identified an opportunity to enhance the platform to support BOTH Speedee and Grease Monkey franchises within a single application. This architectural improvement increases the platform's strategic value and market opportunity without increasing the $10,000 development cost or 6-week timeline. The enhanced design allows owners with mixed-franchise portfolios to manage all their shops from one unified dashboard."

**Key Talking Points:**
1. **Same price, more value**: $10k still gets you a multi-franchise platform
2. **Same timeline**: Still deliverable within 6-week MVP timeline
3. **Broader opportunity**: Can serve mixed Speedee/Grease Monkey owners
4. **Future-proof**: Architecture supports additional franchise brands
5. **Licensing potential**: Platform can be sold to other automotive franchises

### Questions Corporate May Ask

**Q: "Why didn't the original proposal include this?"**
**A:** The original proposal focused on single-brand simplicity. During architectural review, we realized many franchise owners operate both brands, and supporting both simultaneously would significantly increase strategic value at minimal additional complexity.

**Q: "Does this increase risk?"**
**A:** It introduces additional technical complexity (multi-tenancy, data isolation), but these are well-understood patterns with established mitigation strategies. The risk profile remains favorable with no unmitigated critical risks.

**Q: "Will this delay the demo?"**
**A:** No. The 6-week timeline accounts for this complexity through AI-assisted development efficiency and task parallelization. MVP delivery remains at end of Week 6.

**Q: "What if we only want to pilot Speedee?"**
**A:** The architecture supports that. We can pilot only Speedee shops initially, but the platform will be ready to add Grease Monkey locations whenever corporate decides. The flexibility is built in.

---

## Future Revisions (Placeholder)

This section will document future proposal changes as they occur.

### Revision 1.2 (If Needed)
**Date:** TBD
**Trigger:** Corporate feedback after proposal submission
**Changes:** TBD

### Revision 2.0 (If Needed)
**Date:** TBD
**Trigger:** Major scope change or architectural pivot
**Changes:** TBD

---

## Conclusion

**What This Addendum Demonstrates:**
- Rigorous proposal review process
- Responsiveness to architectural insights
- Willingness to enhance value without price increase
- Transparent documentation of changes and rationale

**What Corporate Receives:**
- Enhanced multi-franchise platform for single-brand price
- Clear audit trail of proposal evolution
- Confidence in developer's planning and analysis rigor
- Strategic platform with broader market opportunity

**Next Steps:**
1. Review updated proposal documents (01-08 + README)
2. Corporate stakeholder review and feedback
3. Address any questions or concerns
4. Proceed to contract signature and development kickoff

---

**Addendum Prepared By:**
Mason Roberts
Rogue Media Lab
October 31, 2025

**For:**
Speedee Oil Change & Auto Service Corporate
Grease Monkey Franchise Owners

**Re:** Shop Rewards Proposal - Version 1.1 Architectural Enhancements

---

**END OF ADDENDUM**
