# KrishiBazaar AI — Project Plan

> **Status:** Planning / pre-development  
> **Planned start:** 11–12 November 2026  
> **Initial focus:** Banana trading in India  
> **Future expansion:** Maize, cotton, onions and other crops, based on demand and feasibility

## 1. Project overview

KrishiBazaar AI is a planned digital agricultural marketplace where farmers can connect directly with wholesalers and bulk buyers. Farmers will post crop photos, available quantity, variety, location, harvest details and expected price. Buyers will browse listings, send offers in ₹ per quintal (or another appropriate unit), chat with farmers and agree on deals.

The aim is to improve access to buyers and price transparency, and reduce reliance on unnecessary intermediaries and their commissions. The platform cannot guarantee higher prices or eliminate every intermediary: transport, grading, payment and local market arrangements still matter.

The first release will support **bananas only**, while the data model will allow additional crops later.

## 2. Goals and success criteria

### MVP goals

- Farmers and buyers can register, sign in and manage profiles.
- Farmers can create, edit, publish and close banana listings with photos and quantities.
- Buyers can search and filter active listings and send offers.
- Farmers can accept, reject or counter offers and chat with buyers.
- A confirmed deal records the agreed price, quantity, parties and status.
- The website works well on low-end mobile phones and can be installed as a Progressive Web App (PWA) on supported devices.

### Initial acceptance criteria

- A farmer can publish a valid listing and a buyer can find it.
- Offers are stored accurately and only authorised users can act on them.
- Concurrent acceptance cannot sell more quantity than remains available.
- Chat messages are visible only to participants in the relevant conversation.
- Invalid images, oversized uploads and failed requests return understandable errors.
- Core flows have automated tests and are demonstrable end to end.

## 3. Users and key workflows

**Farmer:** Register → create profile → upload crop photos → enter stock details → publish listing → review offers → chat/counteroffer → confirm deal → update remaining quantity.

**Buyer:** Register → browse/filter listings → review photos and details → submit offer → negotiate → confirm deal → coordinate collection and payment outside the platform in the MVP.

**Administrator (limited MVP scope):** Review reported listings/accounts, moderate misuse and investigate transaction disputes. Admin functions will be added only as needed.

## 4. Planned features by priority

| Priority | Feature | Notes |
|---|---|---|
| P0 | Authentication and roles | Farmer, buyer; secure sessions and authorisation |
| P0 | Crop listings | Photos, banana variety, quantity, unit, location, harvest date, asking price |
| P0 | Search and filters | Location, variety, quantity, price, listing status |
| P0 | Offers and counteroffers | Price per unit, offered quantity, status and expiry |
| P0 | Deal confirmation | Transaction-safe stock reservation / deduction |
| P1 | Real-time chat | Private conversations linked to listings/offers |
| P1 | PWA | Installable, responsive interface; basic caching where safe |
| P1 | Notifications | In-app first; push notifications only where supported |
| P2 | AI visual quality assistance | Preliminary estimates from images, with uncertainty |
| P2 | Market price suggestions | Historical-data-based estimates, not guaranteed prices |
| P3 | Buyer matching | Relevant buyer recommendations |
| P3 | Marathi/Hindi assistance | Explore text and voice input after validating demand |
| P3 | More crops | Crop-specific attributes and models |

## 5. Technology stack

| Layer | Choice |
|---|---|
| Web frontend / PWA | Next.js, TypeScript, Tailwind CSS |
| Backend | Python, FastAPI |
| Relational database | PostgreSQL |
| Database access / migrations | SQLAlchemy, Alembic |
| Live communication | FastAPI WebSockets |
| Images | S3-compatible object storage; local development alternative |
| AI experimentation | Python, PyTorch, OpenCV, scikit-learn |
| Testing | pytest, Playwright |
| Local environment / CI | Docker Compose, GitHub Actions |

Start with a **modular Python backend**, not multiple independent microservices. Add background jobs and a worker process when AI inference or image processing requires it. Do not run expensive inference synchronously in user-facing request handlers.

## 6. High-level architecture

```text
Next.js website / installable PWA
       |
       | HTTPS REST + authenticated WebSocket
       v
Python FastAPI application
  |-- auth and users
  |-- crop listings and image metadata
  |-- offers, negotiation and deals
  |-- chat and notifications
  |-- AI analysis interface (planned)
       |                |
       v                v
  PostgreSQL       Object storage
       |
       +-- Optional background job / AI worker later
```

Next.js is responsible for user experience. FastAPI owns business rules and database writes. PostgreSQL stores structured records; object storage holds images. AI results are stored as **advisory metadata**, not as unquestionable quality certificates.

## 7. Initial data model

- `users`: identity, role, contact details, verification status.
- `farmer_profiles` and `buyer_profiles`: role-specific information.
- `crops`: crop catalogue; initially only banana is enabled.
- `listings`: farmer, crop, variety, location, quantity, unit, expected price, harvest date, status.
- `listing_images`: storage reference, upload metadata, ordering.
- `offers`: listing, buyer, proposed price and quantity, status, expiry, timestamps.
- `offer_events`: audit trail of changes and counteroffers.
- `deals`: agreed terms, reserved quantity, buyer, farmer, deal status.
- `conversations` and `messages`: private negotiation history.
- `ai_assessments` (later): model version, result, confidence/limitations, timestamp.

Use database constraints, transactions and indexes. Crop-specific fields can be introduced using validated attributes rather than hardcoding bananas into every table.

## 8. Important engineering decisions

### Concurrent offers and stock safety

Multiple buyers may bid on the same listing. Submitting an offer must **not** automatically reserve stock. When confirming a deal, perform the quantity check and stock update in a single database transaction, using suitable row locking or an atomic conditional update. Make confirmation idempotent and test simultaneous requests, retries and partial failures.

### Images and user safety

Validate file type and size, verify actual file contents, generate safe object names and restrict access appropriately. Avoid trusting user-provided filenames or metadata. Add rate limits and basic moderation/reporting as the product grows.

### Price and quantity consistency

Store prices with decimal-safe types and an explicit currency/unit. Record the agreed quantity and price on the deal itself so later listing edits do not change historical transactions.

### Connectivity and PWA

Design mobile-first pages, optimise images and support poor network conditions with clear retry states. Offline caching must not make stale prices, availability or offers appear current. PWA installation and notification behaviour differs across platforms.

## 9. AI and machine learning plan

### A. Banana image assessment

**Goal:** Estimate visible ripeness, colour consistency and possible surface defects from submitted images.

1. Define an image collection protocol and obtain permission to use photos.
2. Build or source a labelled dataset with diverse lighting, devices and banana varieties.
3. Establish simple baseline models before more complex architectures.
4. Split data carefully by source/lot to avoid misleading evaluation.
5. Measure accuracy and error patterns across conditions.
6. Return confidence or uncertainty and flag unusable images.
7. Keep human inspection as the final basis for quality-sensitive deals.

Photos cannot reliably establish internal quality, weight, moisture or every defect. Do not describe image scores as certified grades.

### B. Market price guidance

Start with clearly labelled historical price charts if suitable data is available. Then evaluate simple statistical baselines and machine learning models using time-aware validation. Inputs may include market, location, variety, season and available quantity. Compare predicted ranges with actual observed prices, report uncertainty and avoid presenting estimates as guaranteed selling rates.

### C. Buyer matching and languages

Initially use transparent filters (location, crop, quantity) before introducing machine learning ranking. Explore Marathi/Hindi interfaces and voice input after user testing. Do not assume that speech recognition works equally well for all accents and farming terminology.

## 10. AI coding assistant workflow

The AI coding assistant will be used regularly for:

- Planning feature boundaries, API contracts and database schemas.
- Generating and refining Next.js components and FastAPI endpoints.
- Reviewing authentication, authorisation and transaction logic.
- Producing unit, integration, concurrency and end-to-end tests.
- Debugging failed builds, API errors, database issues and WebSocket behaviour.
- Building image preprocessing, inference and evaluation scripts.
- Comparing alternative implementations and profiling bottlenecks.
- Improving documentation, developer setup and CI workflows.

**Review process:** Define the task and acceptance criteria → request a proposed implementation → inspect the changes → run tests → investigate failures → refine and commit. AI-generated code will be reviewed and validated before use. Avoid uploading private farmer/buyer data to external AI tools without permission.

## 11. Milestones (tentative, not commitments)

| Phase | Scope | Deliverable |
|---|---|---|
| 0 — Setup | Repository, README, environment, Next.js and FastAPI skeleton, database connection | Both services run locally; basic CI checks |
| 1 — Marketplace | Auth, profiles, listings, photo upload, search | Farmer publishes and buyer discovers a listing |
| 2 — Negotiation | Offers, counteroffers, chat, deal confirmation | Tested end-to-end deal flow with concurrency protection |
| 3 — AI pilot | Banana image dataset, baseline model, API integration | Experimental quality assessment with evaluation results |
| 4 — Insights | Historical prices and recommendation prototype | Price guidance with documented accuracy and limitations |
| 5 — Pilot | Mobile usability, bug fixes, farmer/buyer feedback | Small real-user trial and prioritised improvement backlog |

**Planned start:** Mid-next week (11–12 November 2026). Exact milestone dates will be set after initial development and feedback. The project is **not yet implemented** as of this plan.

## 12. Testing strategy

- **Backend unit tests:** Validation, permissions, offer transitions, pricing and quantity calculations.
- **Database integration tests:** Transactions, constraints, migrations and concurrent deal confirmation.
- **Frontend tests:** Listing forms, offer flows, errors and responsive layouts.
- **End-to-end tests:** Register → publish → discover → offer → negotiate → confirm.
- **AI evaluation:** Holdout datasets, false positives/negatives, uncertainty and robustness under different photo conditions.
- **Security checks:** Access control, upload validation, rate limiting and protection of personal details.
- **Performance checks:** Image upload sizes, listing search, chat load and AI processing latency.

## 13. Risks and mitigations

| Risk | Planned response |
|---|---|
| Limited trustworthy AI training data | Begin with research and baseline experiments; delay quality claims |
| Incomplete or noisy market price data | Label data sources and uncertainty; use historical charts first |
| Buyers or farmers not adopting the platform | Test with a small local pilot and simplify onboarding |
| Fraud, misleading photos or disputed deals | Verification, reporting, audit history and clear terms |
| Conflicting bids or overselling | Database transactions, idempotency and concurrency tests |
| Unreliable mobile internet | Lightweight pages, retries and clear stale-data handling |
| Legal/marketplace obligations | Review applicable rules before facilitating payments or regulated trading |

## 14. Scope boundaries

The initial MVP will **not** provide escrow, payment processing, delivery guarantees, certified produce grading or automatic crop disease diagnosis. Payments and transport coordination can remain outside the platform until requirements, compliance and operational responsibilities are understood.

## 15. First development tasks

- [ ] Create repository, project documentation and issue tracker.
- [ ] Bootstrap Next.js + TypeScript frontend and FastAPI backend.
- [ ] Add Docker Compose for local PostgreSQL.
- [ ] Define initial database tables and Alembic migrations.
- [ ] Create basic farmer and buyer flows and API contracts.
- [ ] Implement authentication and authorisation.
- [ ] Build listing creation, image upload and browse pages.
- [ ] Add offer and deal state machines with tests.
- [ ] Implement chat and mobile-first PWA configuration.
- [ ] Start AI dataset and evaluation research.

## 16. Ownership and licensing

This is planned as an independently developed project. **No open-source licence has been selected yet.** Until a licence is added, do not assume that public repository visibility grants permission to reuse the code. Any future licence choice should be checked against the requirements of the development contract.

---

**Project principle:** Build a working, trustworthy banana marketplace first; add AI only where it can be tested and genuinely helps farmers and buyers. Expand to other crops after validating the first use case.
