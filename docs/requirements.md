# Fullbar: functional and non-functional requirements

> **Fullbar is a fictional company created for the Project Build Room. Its customers (Northlane Parcels, Kestrel Couriers), staff and data are all fake.**

**Status:** Draft v0.1 (product-owner). Not yet challenged by secure-design or approved by Kyle. Numbers marked *(proposed)* are starting targets to confirm, not agreed facts.

**Priority key:** **M** = Must, **S** = Should, **C** = Could, **W** = Won't for now.
**Roles:** Driver (DRV), Fleet manager (FM), Site host (SH), Fullbar admin (ADM), Map app partner (PTR, a system, not a person).

---

## 1. Functional requirements

### 1.1 Accounts, login and roles

| ID | Requirement | Role(s) | Priority | Acceptance criterion (how we test it) | Tenant note |
|---|---|---|---|---|---|
| FR-01 | Users sign in with email and password. | All | M | Given a valid account, when the user signs in, then they reach the home screen for their role; a wrong password shows a generic error. | Session records the user's courier. |
| FR-02 | Each user has exactly one role: driver, fleet manager, site host or Fullbar admin. | All | M | Given a user, when they sign in, then only the menus and actions for their role are shown and allowed by the server. | — |
| FR-03 | Drivers and fleet managers belong to exactly one courier. | DRV, FM | M | Given a Northlane user, when they use any screen, then they only ever see Northlane data. | Core isolation rule. |
| FR-04 | Fleet managers invite, deactivate and reactivate drivers in their own courier. | FM | M | Given a fleet manager, when they deactivate a driver, then that driver can no longer sign in or book. | Can only manage own courier's drivers. |
| FR-05 | Users can reset a forgotten password by email link. | All | M | Given a reset request, when the link is used once within its time limit, then the password changes; a reused or expired link fails. | — |
| FR-06 | Users can sign out, ending their session. | All | M | Given a signed-in user, when they sign out, then the old session can't be reused. | — |
| FR-07 | Fullbar admins sign in to a separate admin portal with multi-factor authentication. | ADM | M | Given an admin, when they sign in without the second factor, then access is refused. | — |

### 1.2 Finding and booking a bay

| ID | Requirement | Role(s) | Priority | Acceptance criterion | Tenant note |
|---|---|---|---|---|---|
| FR-08 | Drivers see charging sites on a map and a list, with live bay availability. | DRV | M | Given a driver, when they open the map, then each site shows free van-sized bays for the next slots. | Availability is shared; who booked is never shown. |
| FR-09 | Drivers filter bays by van size (bay length and width), connector type and charger power. | DRV | M | Given a long-wheelbase van, when the driver filters, then only bays that fit it are shown. | — |
| FR-10 | Drivers book a bay for a fixed time slot (e.g. 30, 45 or 60 minutes). | DRV | M | Given a free bay, when the driver books it, then it is reserved for that slot and no one else can book it. | Booking belongs to the driver's courier. |
| FR-11 | The system prevents double booking of a bay and slot. | DRV | M | Given two drivers booking the same bay and slot at once, then exactly one succeeds and the other sees a clear message. | — |
| FR-12 | Drivers see, change and cancel their own upcoming bookings, up to a cut-off before the slot. | DRV | M | Given a booking 30+ minutes away *(proposed cut-off)*, when the driver cancels, then the bay becomes free again. | Own bookings only. |
| FR-13 | Drivers get a booking confirmation and a reminder before the slot. | DRV | S | Given a booking, then a confirmation shows immediately and a reminder arrives 15 minutes before *(proposed)*. | — |
| FR-14 | A booking is released if the driver doesn't arrive within a grace period. | DRV | S | Given no charge started 10 minutes into the slot *(proposed)*, then the booking is marked no-show and the bay freed. | — |
| FR-15 | Fleet managers can book on behalf of their drivers. | FM | C | Given a fleet manager, when they book for a driver in their courier, then the booking shows in that driver's list. | Own courier's drivers only. |

### 1.3 Charging

| ID | Requirement | Role(s) | Priority | Acceptance criterion | Tenant note |
|---|---|---|---|---|---|
| FR-16 | Drivers start a charge at their booked bay from the app. | DRV | M | Given a booking for now at that bay, when the driver starts, then a charge session begins; starting at a bay they didn't book is refused. | — |
| FR-17 | Drivers see live charge progress (energy, time, estimated cost). | DRV | S | Given an active session, then progress updates at least every 30 seconds *(proposed)*. | Own session only. |
| FR-18 | Each charge session is recorded with bay, van, driver, start and end time, energy and cost. | System | M | Given a finished session, then a record exists with all fields and the courier it belongs to. | Recorded against the courier. |
| FR-19 | Charger readings (status, power, energy) are received and stored. Readings are simulated in this project. | System | M | Given the simulator, when it sends a reading, then the bay's status updates. | Not courier data. |

### 1.4 Fleet dashboard and costs

| ID | Requirement | Role(s) | Priority | Acceptance criterion | Tenant note |
|---|---|---|---|---|---|
| FR-20 | Fleet managers see all their fleet's bookings and charge sessions, filterable by driver, van, site and date. | FM | M | Given a Northlane fleet manager, then they see every Northlane booking and none from Kestrel. | Critical isolation point. |
| FR-21 | Fleet managers see costs per day, week and month, by van and driver. | FM | M | Given sessions in a month, then totals match the sum of session costs. | Own courier only. |
| FR-22 | Fleet managers export bookings and costs to CSV. | FM | S | Given a date range, when exported, then the file contains only their courier's rows. | Export must be scoped. |
| FR-23 | Fleet managers manage their vans (registration, model, battery size, bay size needed). | FM | M | Given a new van, when added, then drivers can select it when booking. | Own courier's vans only. |
| FR-24 | Fleet managers see a monthly statement of charges and the per-van fee. | FM | S | Given a month end, then a statement lists energy charges and per-van fees. | — |
| FR-25 | Fleet managers see the bay reliability figure for bookings their drivers made. | FM | C | Given past bookings, then the % that were free and working on arrival is shown. | Own courier's bookings only. |

### 1.5 Fault reporting

| ID | Requirement | Role(s) | Priority | Acceptance criterion | Tenant note |
|---|---|---|---|---|---|
| FR-26 | Drivers report a fault at a bay (charger not working, bay blocked, damage), with an optional photo. | DRV | M | Given a fault report, then it is saved with bay, time, type and reporter, and admins can see it. | Report belongs to the driver's courier. |
| FR-27 | Fault photos are limited in type and size. | DRV | M | Given a file that isn't JPEG/PNG or is over 10 MB *(proposed)*, then upload is refused. | — |
| FR-28 | When a fault is confirmed, the bay is taken out of service and affected bookings are told. | ADM | M | Given a confirmed fault, then the bay shows as unavailable and drivers with bookings there are notified. | Notifications go only to each booking's owner. |
| FR-29 | Drivers see the status of faults they reported. | DRV | S | Given a reported fault, then the driver sees Open, Confirmed or Fixed. | Own reports only. |

### 1.6 AI chatbot (AWS Bedrock)

| ID | Requirement | Role(s) | Priority | Acceptance criterion | Tenant note |
|---|---|---|---|---|---|
| FR-30 | Drivers ask the chatbot questions in plain language (e.g. "Is bay 3 at Leith working?", "How do I cancel?"). | DRV | M | Given a question about charger status, then the answer matches live status. | — |
| FR-31 | The chatbot can read live charger and bay status. | DRV | M | Given a status question, then the chatbot calls the status tool and reports the result. | Status only, never who booked. |
| FR-32 | The chatbot can log a fault on the driver's behalf, after confirming the details with them. | DRV | M | Given "charger 2 at Leith isn't working", then the bot confirms and creates a fault report attributed to that driver. | Uses the driver's own identity. |
| FR-33 | The chatbot refuses requests outside its job (e.g. other couriers' bookings, cancelling bookings, controlling chargers). | DRV | M | Given "list all bookings at Leith today", then it declines and shows nothing about other users. | Critical isolation point. |
| FR-34 | Drivers can hand over to a human support contact if the chatbot can't help. | DRV | S | Given an unresolved question, then the driver is shown how to contact support. | — |

### 1.7 Site hosts

| ID | Requirement | Role(s) | Priority | Acceptance criterion | Tenant note |
|---|---|---|---|---|---|
| FR-35 | Site hosts see usage of bays at their own site (occupancy, energy, faults). | SH | S | Given a site host, then they see their site's figures and no other site's. | Never shows driver or courier names. |
| FR-36 | Site hosts see their revenue share each month. | SH | S | Given a month, then the revenue share matches the agreed percentage of charging income at their site. | — |
| FR-37 | Site hosts can mark a bay temporarily unavailable (e.g. car park works), with a reason. | SH | C | Given a closure, then new bookings are blocked and affected drivers notified. | — |

### 1.8 Admin portal

| ID | Requirement | Role(s) | Priority | Acceptance criterion | Tenant note |
|---|---|---|---|---|---|
| FR-38 | Admins onboard and manage couriers (tenants), fleet managers and site hosts. | ADM | M | Given a new courier, when created, then its first fleet manager can sign in and sees an empty fleet. | New tenant starts isolated. |
| FR-39 | Admins manage sites, bays (size, connector, power) and tariffs. | ADM | M | Given a new bay, then drivers can see and book it. | — |
| FR-40 | Admins review, confirm and close fault reports. | ADM | M | Given a fault, when confirmed, then FR-28 happens. | — |
| FR-41 | Admins see a network-wide view of bays, bookings and the bay reliability metric. | ADM | M | Given the dashboard, then reliability % for the month is shown. | Admin access is logged. |
| FR-42 | Admin actions are recorded in an audit log. | ADM | M | Given any admin change, then an audit entry records who, what, when and which courier. | — |

### 1.9 Public partner API

| ID | Requirement | Role(s) | Priority | Acceptance criterion | Tenant note |
|---|---|---|---|---|---|
| FR-43 | Map app partners read live bay availability by site through an API key. | PTR | S | Given a valid key, then the API returns sites and free bays; an invalid key is refused. | Read-only; no booking, driver or courier data. |
| FR-44 | Admins issue, rotate and revoke partner API keys. | ADM | S | Given a revoked key, then calls with it fail immediately. | — |

### 1.10 Notifications

| ID | Requirement | Role(s) | Priority | Acceptance criterion | Tenant note |
|---|---|---|---|---|---|
| FR-45 | Users get in-app and email notifications for bookings, cancellations, faults and account changes. | All | S | Given a booking change, then the affected user is notified within 1 minute *(proposed)*. | Only to the record's owner. |

### Out of scope for now (Won't)

| ID | Item | Why not now |
|---|---|---|
| W-01 | Taking card payments in the app | Fleets are invoiced; payments add PCI scope. |
| W-02 | Public (non-fleet) drivers | Fullbar is fleet-only at launch. |
| W-03 | Real charger hardware control (OCPP) | Readings are simulated in this project. |
| W-04 | Native iOS and Android apps | A mobile-friendly web app first. |
| W-05 | Dynamic pricing | Fixed tariffs at launch. |

---

## 2. Non-functional requirements

| ID | Category | Requirement | Target *(proposed unless stated)* | How we measure it | Priority |
|---|---|---|---|---|---|
| NFR-01 | Tenant isolation | No user, API call or chatbot answer may expose one courier's data to another. | Zero tolerance (agreed risk appetite) | Automated cross-courier tests on every data endpoint and chatbot tool; release blocked on failure | M |
| NFR-02 | Performance | Booking confirmed quickly on a mobile connection. | 95% of bookings confirmed in < 2 s | API timing in logs and load test | M |
| NFR-03 | Performance | Map and availability load fast. | First screen in < 3 s on 4G | Browser performance test | S |
| NFR-04 | Performance | Chatbot replies promptly. | 90% of replies in < 5 s | Chatbot timing logs | S |
| NFR-05 | Availability | Service is up during delivery hours. | 99.5% between 06:00 and 21:00 every day | Uptime monitoring | M |
| NFR-06 | Resilience | If the website is down, confirmed bookings are still honoured on site. | Bays stay reserved; drivers can start a charge via fallback | Outage drill | S |
| NFR-07 | Reliability (success metric) | Booked bays are free and working on arrival. | ≥ 98% per month (agreed) | Booked slots with a successful charge ÷ all booked slots | M |
| NFR-08 | Scalability | Platform grows without redesign. | 20 couriers, 50 sites, 1,000 bays, 5,000 vans, 50,000 bookings/month | Load test at 2× these figures | S |
| NFR-09 | Scalability | Adding a new courier is configuration, not code. | New tenant live in < 1 hour by an admin | Onboarding test | M |
| NFR-10 | Usability | Driver screens work one-handed on a phone in a van cab. | Book a bay in ≤ 3 taps from the map; large touch targets | Usability test with 5 users | M |
| NFR-11 | Accessibility | Meets accessibility guidelines. | WCAG 2.2 AA | Automated checker plus manual review | S |
| NFR-12 | Browser and device support | Works on current mobile and desktop browsers. | Last two versions of Chrome, Safari, Edge, Firefox; iOS and Android | Test matrix | M |
| NFR-13 | Security standard | App, APIs and chatbot follow agreed standards. | OWASP ASVS Level 2, OWASP API Top 10, OWASP LLM Top 10 | Security requirements traced in Jira; scans | M |
| NFR-14 | Authentication | Strong sign-in. | MFA mandatory for admins; optional for fleet managers *(open question)* | Config review and test | M |
| NFR-15 | Encryption | Data protected in transit and at rest. | TLS 1.2+ everywhere; database and file store encrypted at rest | Config scan | M |
| NFR-16 | Privacy | Complies with UK GDPR. | Data inventory kept current; lawful basis recorded; subject access requests answerable | DPIA and Data inventory review | M |
| NFR-17 | Data retention | Data kept only as long as needed. | Bookings and sessions 2 years (billing); chatbot logs 30 days; fault photos 12 months; audit logs 1 year | Automated deletion jobs; quarterly check | S |
| NFR-18 | Auditability | Security-relevant actions are traceable. | Audit log for logins, bookings, admin actions, chatbot tool calls, incl. courier; tamper-evident | Log review; test entries exist | M |
| NFR-19 | Observability | Problems are seen before customers report them. | Metrics, logs and alerts for API errors, latency, charger offline, chatbot failures | Alert test | S |
| NFR-20 | Backup and recovery | Data can be restored after loss. | RPO 15 minutes, RTO 4 hours | Quarterly restore test | S |
| NFR-21 | Environments | Separate places to build, test and run. | Dev, staging and production, with fake data outside production | Environment review | M |
| NFR-22 | Deployability | Changes ship safely and repeatably. | Automated build, tests and security scans before every release; rollback in < 15 min | Pipeline evidence | S |
| NFR-23 | Infrastructure as code | Infrastructure is reproducible. | Cloud resources defined in code (e.g. Terraform) *(open decision)* | Repo review | C |
| NFR-24 | Hosting region | Data stays in the UK. | AWS eu-west-2 (London) for data and Bedrock where available *(open decision)* | Config review | S |
| NFR-25 | Maintainability | Code is understandable and tested. | Lint passes; automated tests on all tenant-scoped endpoints; key decisions in decisions.md | CI checks | M |
| NFR-26 | Cost | Running costs stay predictable. | Monthly cloud and Bedrock spend tracked against a budget; alert at 80% | Billing alerts | S |
| NFR-27 | AI behaviour | Chatbot stays within its role and is safe to rely on. | Only two tools; refuses off-topic or cross-tenant requests; prompt-injection test suite passes | Chatbot test suite | M |
| NFR-28 | Integration | Partner API is stable and fair to use. | Versioned (`/v1`); rate-limited per key *(e.g. 60 requests/min, proposed)* | API tests | S |
| NFR-29 | Localisation | UK users. | English (UK), GBP, 24-hour times, Europe/London time zone | UI review | M |
| NFR-30 | Test data | No real data outside production. | All seed and test data fictional | Data review | M |

---

## 3. Assumptions to confirm with Kyle

1. Fleets pay (45p/kWh and £15 per van per month, from the business model); drivers never pay in the app.
2. Slot lengths are 30, 45 or 60 minutes, with a cancellation cut-off of 30 minutes and a no-show grace period of 10 minutes.
3. A driver belongs to one courier only, and a van to one courier only.
4. Site hosts never see which courier or driver used their bays.
5. The app is a mobile-friendly website, not a native app, at launch.
6. Charger readings are simulated for this project.

## 4. Open questions

1. Should MFA be mandatory for fleet managers as well as admins?
2. Can a driver book more than one slot at a time, or a slot days ahead? If so, what limit?
3. Should fleet managers set booking rules for their drivers (e.g. max slots per day)?
4. Hosting: AWS only, and is London region required for everything including Bedrock?
5. Infrastructure as code: Terraform (the repo's existing .gitignore suggests it)?
6. Who confirms a fault: Fullbar admins only, or site hosts too?

## 5. Next steps

1. **secure-design** challenges this draft and adds a security requirement for each functional requirement.
2. **enterprise-architect** turns the journeys and NFRs into user journeys, DFDs and the HLD.
3. Kyle approves, then the epics, stories and tasks are designed and created in Jira.
