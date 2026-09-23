
---

### **Vision Statement**

To transform our local service bays from a commodity, price-driven transaction into a trusted, data-powered, and indispensable relationship, making our shops the undisputed first choice for vehicle maintenance in our community.

### **Mission Statement**

Our mission is to deploy a proprietary marketing platform that drives immediate, on-demand traffic to fill our idle bays. We will provide custom and direct incentives to customers, building loyalty and helping them choose our location among the sea of other choices in the area.

---

Product: "Shop Rewards" (Working Title)

Author: Mason Roberts

Stakeholders: Franchise Owner, Shop Managers

Date: October 25, 2025

Status: DRAFT V1.1

### 1. Introduction & Problem Statement

We are facing an $800,000 revenue loss across several franchise locations. This loss is not due to poor service but to the stretched economy and a critical failure in our business model. Our core problem is **Low Customer Flow:** We are paying full-time, skilled crews to stand and sweep empty bays. We are in a commodity price war with high local competition and have no effective weapon to generate on-demand traffic.

We have some automatic tools at our disposal. Our Sage POS will send out reminders on services we have recommended as well as provide a maintenance services checklist to the service advisor. We are looking over every car and presenting estimates using Napa ProLink that is often competitive when run through Repair Pal. We are offering discounts to try and get return customers. _I personally believe the shop is not the major concern but rather the competition in the area._

Customers have options and are exploring them. We need a dedicated path to show them we appreciate them and reward their commitment. This platform is not a "nice-to-have." It is a targeted surgical tool designed to solve this specific, and costly problem.

### 2. Goals & Business Case

The MVP's only goal is to prove this hypothesis in a 90-day pilot:

"We can fill our idle bays and build a loyal, returning customer base by using a simple, non-franchised PWA to send targeted offers and run a proprietary rewards program."

- **Goal 1 (Acquisition):** Fill at least one "idle bay" hour per day, per store, using the "Flash Sale" feature.
    
- **Goal 2 (Retention):** Generate measurable repeat business via the "Rewards Program," converting price-shoppers into loyal customers.
    
- **Business Case:** This MVP is not designed to increase Average Repair Order (ARO); it is designed to **stop the payroll bleed.** A full shop crew has a high fixed hourly cost. Every hour a bay sits empty, we are actively losing money. This app converts those idle, high-cost hours into _revenue-generating_ hours, even at a discount. A 50% off oil change is infinitely more profitable than paying a full crew to sweep an empty bay.
    

#### The Concept

This is a progressive web app (PWA) built with Rails. It is an app customers can have on their phones that sends alerts on specific, time-sensitive deals. This is a fancy website that functions like an app.

Effectively, when the shop is experiencing downtime and has empty bays, the shop manager can push a "Flash Sale" for service. "_For the next 2 hours get your oil change for 50% off!_"

This alert is pushed to customers who have signed up, allowing for a rewards program. "_Get 3 oil changes, get the fourth one free_". This creates dedicated customers as they now prefer "_collecting points_" to get "_free stuff_".

### 3. Key Assumptions & Critical Risks

This project is built on the following assumptions and includes significant, manageable risks.

- **Assumption:** A strong monetary incentive (e.g., "50% off _now_") is a powerful enough "lure" to drive customer downloads and immediate traffic. The added gamification with the points system will sponsor a dedicated customer foundation.
    
- **CRITICAL RISK: Franchise Agreement Breach**
    
    - **Description:** As a Speedee franchisee, I assume we are contractually limited in our use of software, branding, and customer data. Deploying a "Speedee app" or scraping the POS would be a critical breach.
        
    - **MVP Mitigation (Non-Negotiable):**
        
        1. **Brand Firewall:** The app _must not_ use the Speedee logo or name. It will be branded as a local program run by the owner's LLC (e.g., "Auto Perks").
            
        2. **Data Firewall:** There will be **zero (0)** integration with the Speedee / Sage POS. All customer and service data in our app will be 100% manually entered by our own staff and/or the customer on sign-up. This creates a legally defensible "second, proprietary dataset."
            
- **CRITICAL RISK: Data Rick and Security**
    
    - **Description:** We are collecting PII from the customer. We need to be clear with the customer up front.
        
    - **MVP Mitigation (Non-Negotiable):**
        
        1. **Clear Privacy Policy:** A public page detailing what data we collect, why we collect it, how we store it, and who we share it with (e.g., Twilio).
            
        2. **Terms of Service (ToS):** The user must actively accept a ToS that outlines the rules and limits our liability.
            
- **CRITICAL RISK: Marketing Compliance (TCPA)**
    
    - **Description:** The "Flash Sale" alerts (both push notifications and SMS) are legally considered "automated marketing messages." Sending these without permission is a direct violation of the **TCPA**, which carries fines of $500 to $1,500 _per message, per customer_.
        
    - **MVP Mitigation (Non-Negotiable):**
        
        1. **Prior Express Written Consent:** The sign-up process _must_ include a **separate, unticked checkbox**. The text next to it must be explicit: "I agree to receive automated marketing text messages and alerts from [Shop Name] at this number. Consent is not a condition of service."
            
        2. **A Clear Opt-Out:** Every marketing text _must_ include "Reply STOP to end" to comply with TCPA rules.
            

### 4. User Personas

- **Price-Driven Customer (User):** Owns a vehicle, shops for maintenance based on price and convenience. Will download an app if the monetary value is immediate and clear.
    
- **Shop Manager (Admin User):** Overwhelmed by high payroll and low traffic. Needs a simple, _fast_ tool to get cars in the door _right now_. Is not technical and has no patience for complex software.
    
- **Franchise Owner (Stakeholder):** Is losing $800k. Needs to stop the payroll bleed and increase revenue without triggering a corporate compliance battle.
    

### 5. MVP Feature Requirements

The MVP is a Progressive Web App (PWA) with two "sides" built on a single Rails 8 backend.

#### Module 1: Customer-Facing PWA (The "Lure")

This is the tool for customer acquisition and retention.

|**ID**|**Feature**|**Description & Justification**|
|---|---|---|
|**PWA-101**|Simple Auth|User signs up/logs in with an SMS/phone number. This is fast and captures the key data point for marketing.|
|**PWA-102**|"Digital Glovebox"|User can manually add their Vehicle(s) (Year, Make, Model). This builds our dataset for future targeting.|
|**PWA-103**|"Coupon Wallet"|A simple screen that displays all active "Flash Sale" coupons. This is the primary "lure" for the user.|
|**PWA-104**|Push Notification Services|The app must be able to receive push notifications. This is the core mechanism for the "Flash Sale" feature.|
|**PWA-105**|"View Rewards"|A simple 'punch card' screen showing the user their progress (e.g., "You have 2 of 3 points for your next free oil change").|
*PWA-102 - "Digital Glovebox" is not required for the goal of MVP but allows for a stepped progression of the app.*
*PWA-102 - "Digital Glovebox could include a VIN scanner for customers.*
#### Module 2: Admin App (The "Bay-Filler")

This is the internal tool for shop staff, accessed via a tablet or PC. It is designed for _speed_ and _simplicity_.

|**ID**|**Feature**|**Description & Justification**|
|---|---|---|
|**ADM-101**|Secure Admin Auth|Separate, secure login for staff/owner.|
|**ADM-102**|"Bay-Filler" Command Button|A large, simple "Send Flash Sale" button. Admin enters a title ("50% Off Oil Change") and duration ("2 Hours"), and it sends the `PWA-104` push notification.|
|**ADM-103**|Customer Lookup|A simple search for customers by phone number or name.|
|**ADM-104**|"Add Reward Point"|A simple button on the `ADM-103` lookup result to grant a "point" to the customer's account (e.g., after they pay for a service).|
|**ADM-105**|Reward Program Manager|(Owner-Only) A basic CRUD interface to define what the rewards are (e.g., "3 points = Free Oil Change").|
*ADM-104 - "Add Reward Point could be automatic with a QR code or Barcode*
#### Module 3: Non-Functional Requirements (NFR)

|**ID**|**Requirement**|**Justification**|
|---|---|---|
|**NFR-101**|Progressive Web App (PWA)|Must be built as a PWA, not a native app, to avoid App Store friction/fees and allow for rapid "vibe development" deployment.|
|**NFR-102**|Independent Hosting|Must be hosted on a standalone platform (e.g., Render) completely separate from any Speedee / Sage corporate infrastructure.|
|**NFR-103**|**NO POS INTEGRATION**|Re-stating for emphasis: This app _cannot_ read from, write to, or otherwise touch the franchise POS system.|
|**NFR-104**|Legal Compliance|Must include static pages for ToS and Privacy Policy, and the TCPA opt-in checkbox at sign-up.|
*NFR-102 - Independent Hosting could also be bare metal*
### 6. 90-Day Pilot: Success Metrics

We will measure the pilot's success at the 30, 60, and 90-day marks against the baseline.

- **Primary Metric:** **Daily Car Count** (Proves we are filling empty bays).
    
- **Secondary Metrics:**
    
    - **Customer App Download/Sign-up Rate** (Proves the "lure" is working).
        
    - **"Flash Sale" Coupon Redemption Rate** (Proves the core acquisition feature works).
        
    - **Customer Return Rate** (Proves the "Rewards Program" is building loyalty).
        

---
## Scope and versions
### Phase 1
#### V1.0 (The MVP): "Shop Rewards"
- **Timeframe:** 6-Week Build + 90-Day Pilot
- **Focus:** 100% on **Acquisition & Retention**. The goal is to solve the "low flow" problem and build a loyal customer base.
    
- **Key Features:**
    
    - **For Customers:** A PWA with SMS login, a "Coupon Wallet" for Flash Sales, and a "Rewards" screen (like a digital punch card).
        
    - **For Admin:** A simple dashboard to send "Flash Sale" push notifications (the "Bay-Filler" button), look up customers, and grant reward points.
    
- **Focus:** **Acquisition & Basic Retention.**
    
- **Features:**
    
    - Flash Sale "Bay-Filler" Button (`ADM-102`)
        
    - Simple Coupon Wallet (`PWA-103`)
        
    - Simple "Punch Card" Rewards (`PWA-105` & `ADM-104`)
        
- **Goal:** Prove the hypothesis: "Can we get cars in the bays?"

---

### Phase 2: 
#### V1.1 The "Smart Glovebox" (The Data Engine)

- **Timeframe:** 4-Week "Vibe Development" Sprint (Post-MVP)
- **Focus:** **Data Collection & High-Value Retention.**
    
- **Features:**
    
    - **Evolves `PWA-102` ("Digital Glovebox"):**
        
        - Customers can now enter their **Current Mileage**.
            
        - Customers can log **Service History** (Service Type, Date, Notes). You'd even let them log service from _other shops_ to make it a "complete" record for them.
            
    - **The "Hook" (Why they'll do it):**
        
        - **Smart Reminders:** The app now sends _actually useful_ reminders. "Our records show your 2019 CR-V is at 58,000 miles. You're due for a 60k service. Here's a 15% off coupon for it."
            
        - **Bonus Points:** "Get 5 bonus reward points just for updating your mileage!"
            
- **Goal:** Prove the hypothesis: "Will customers give us their data in exchange for valuable, personalized reminders?"

---
### Phase 3: 
#### V2.0 The "Concierge & AI Moat"

- **Timeframe:** 3-Week Sprint
- **Focus:** **Building Trust & Brand Authority.** A fast-follow-up to build high-touch loyalty without AI complexity.
    
- **Key Features:**
    
    - A "Text-a-Mechanic" button is added to the customer app.
        
    - When a customer sends a message, it simply **forwards as an email/SMS** to the shop manager.
        
    - The app sends an instant, canned auto-reply like, "Got it! One of our expert service writers will text you back in just a minute."
        
- **Key Detail:** This "simulated bot" provides the _feel_ of an advanced assistant with zero AI cost or liability.

---

#### **V2.1 (The "Brand Moat")**

* **Timeline**: 3-Week Sprint
- **Focus:** Establishing the shop as a "tech-enabled authority" by introducing real AI.
    
- **Key Features:**
    
    - Replaces the V2.1 "simulated" bot with a **full AI chatbot** (powered by Claude).
        
    - This requires building a **RAG (Retrieval-Augmented Generation)** system.
        
    - The bot would be fed your shop's FAQs, policies, and warranty info to answer questions.
        
    - It would be programmed with "guardrails" to **deflect diagnostic questions** and turn them into appointments (e.g., "A noise is worth checking. I can book you a free inspection...").
        
    - Handle automated tasks like setting appointments to include gathering required information, ensuring date and time available, setting appointment, sending reminders and verifying.
        
* **Goal:** Make the app indispensable and establish the shop as a "tech-enabled expert."

---

#### **Phase 4: The Full POS (The "V-Future" / The Asset)**

- **Timeframe:** 1-2 Year Build
- **Focus:** **Unifying Operations & Maximizing Profitability.**
- *Further discussion only if concept is proven.*

---

### **Comprehensive Sprints Schedule (6-Week "Vibe Development" Plan)**

This schedule is aggressive and designed for a solo developer leveraging AI assistants (Gemini/Claude) and the Rails 8 framework. It is a high level concept of the sprints.

#### **Sprint 1: The Foundation (1 Week)**

- **Goal:** Set up the project, database, and all authentication.
    
- **Tasks:**
    
    - `rails new shop_rewards --css=tailwind --database=postgresql`
        
    - **Models:** Create models and migrations for `User` (phone, name), `Vehicle` (make, model, year, `user_id`), `AdminUser` (devise), `Coupon`, and `RewardProgram` (e.g., `name`, `points_needed`). Create a `user.points` integer column.
        
    - **Auth:** Set up `devise` for `AdminUser`. Set up SMS-based auth (e.g., using Twilio, AWS) for `User` (`PWA-101`).
        
    - **PWA Setup:** Configure the app as a basic PWA with a manifest file.
        

#### **Sprint 2: The "Bay-Filler" (Core Loop) (1 Week)**

- **Goal:** Build the single most important feature: the "Flash Sale" button.
    
- **Tasks:**
    
    - **Backend:** Build the `ADM-102` "Bay-Filler" feature. This is a simple form on the admin dashboard.
        
    - **Integration:** Integrate OneSignal for push notifications.
        
    - **Frontend:** Write the "send" logic (e.g., a background job) that creates a `Coupon` record and sends a push notification (`PWA-104`) to all users.
        
    - **Test:** Test the full loop: Admin hits button -> Phone gets notification.
        

#### **Sprint 3: The Admin Panel (Controls) (1 Week)**

- **Goal:** Build the complete, simple dashboard for the Shop Manager.
    
- **Tasks:**
    
    - **UI:** Build the basic admin dashboard layout.
        
    - **Lookup:** Build the `ADM-103` Customer Lookup (a simple search box and results page).
        
    - **Reward Action:** Build the `ADM-104` "Add Reward Point" button on the lookup result (e.g., `user.increment!(:points)`).
        
    - **Reward Management:** Build the `ADM-105` CRUD interface (scaffold) for the Owner to create/edit the `RewardPrograms`.
        

#### **Sprint 4: The Customer PWA (The "Lure") (1 Week)**

- **Goal:** Build the entire customer-facing experience.
    
- **Tasks:**
    
    - **UI:** Build the main customer-facing UI (e.g., a simple tab bar).
        
    - **Glovebox:** Build the `PWA-102` "Digital Glovebox" (a simple form for a `User` to add their `Vehicles`).
        
    - **Wallet:** Build the `PWA-103` "Coupon Wallet" (a screen that just lists active `Coupons`).
        
    - **Rewards:** Build the `PWA-105` "View Rewards" screen (e.g., "You have 2 of 3 points for your next Free Oil Change").
        

#### **Sprint 5: Legal & Deployment (1 Week)**

- **Goal:** Implement all legal requirements and deploy the app to a staging server.
    
- **Tasks:**
    
    - **TCPA:** Implement the `NFR-104` **unticked checkbox** on the `PWA-101` sign-up form. Add logic to prevent marketing to users who haven't checked it.
        
    - **Legal:** Create static pages for the Terms of Service and Privacy Policy.
        
    - **Testing:** Conduct end-to-end testing of the full user and admin flows.
        
    - **Deploy:** Deploy the app to Render (or your preferred host) (`NFR-102`).
        

#### **Sprint 6: Buffer & Pilot Prep (1 Week)**

- **Goal:** A "catch-all" week for bugs, polish, and preparing for the 90-day pilot.
    
- **Tasks:**
    
    - Fix bugs found in Sprint 5 testing.
        
    - Polish the CSS / mobile responsiveness.
        
    - Create the simple in-store marketing materials (e.g., "Scan this QR code for 25% off _today_ and join our new Rewards Club!").
        
    - Set up the pilot store manager with their `AdminUser` login and train them on the 3-minute process (Log in, lookup customer, add point. Or, Log in, hit 'Bay-Filler' button).

---

### Visual concepts
These are some rough ideas of what the app could look like.
**Concept 1** - General overall concept, not specific to auto shop.
![[Gemini_Generated_Image_moak92moak92moak.png]]

Concept 2 - more focused on the auto shop but still a general concept
![[Gemini_Generated_Image_c9fmx5c9fmx5c9fm.png]]

Concept 3 - another general concept, blending the two.
![[Gemini_Generated_Image_1z4xwa1z4xwa1z4x.png]]