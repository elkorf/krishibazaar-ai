# KrishiBazaar AI

**A farmer-first digital marketplace for direct agricultural trading.**

> **Project status:** Planning / pre-development. Development is planned to begin around **11–12 November 2026**. The features described below are proposed, not yet implemented.

KrishiBazaar AI is a planned web platform that will help Indian farmers connect directly with wholesalers, suppliers and bulk buyers. Farmers will be able to post crop photos and stock details, receive competing price offers, negotiate in chat and finalise deals. The aim is to improve access to buyers, make pricing more transparent and reduce dependence on unnecessary intermediaries and their commissions.

The initial release will focus on **bananas**. The underlying data model will be designed to support additional crops, such as maize, cotton and onions, later.

## The problem

Farmers often rely on local agents and several phone calls to find buyers. It can be difficult to compare offers, share reliable information about available stock or reach buyers outside an existing network. Intermediaries can provide valuable services, but multiple commissions may reduce the amount a farmer receives.

KrishiBazaar AI aims to offer an alternative route: a transparent, direct channel between farmers and buyers. It will not guarantee better prices or eliminate the need for logistics, inspection or other services; those outcomes will depend on real market conditions.

## How the marketplace will work

1. **Farmer creates a listing:** Uploads crop photos and enters variety, quantity, unit, location, expected price and harvest/availability date.
2. **Buyer discovers stock:** Browses a feed of listings and filters by crop, location, quantity and price.
3. **Buyer sends an offer:** For example, ₹1,900, ₹2,000 or ₹2,100 per quintal, or a custom amount.
4. **Both parties negotiate:** Discuss quality, quantity, pickup and terms through real-time chat.
5. **Deal is confirmed:** The platform records the agreed price, quantity and status for both parties.

The first version will be designed as a mobile-friendly **Progressive Web App (PWA)** so farmers can use the website and install it on supported phones without a separate native app.

## Planned features

### Marketplace MVP

- Farmer and buyer registration, login and role-based access
- Farmer profiles and crop listings with multiple images
- Quantity, variety, location, availability and asking-price details
- Buyer feed, search and filters
- Price offers, counteroffers and offer history
- Private real-time chat and deal status tracking
- Basic notifications and listing management
- Mobile-responsive PWA interface

### AI features (planned, not yet implemented)

**1. Image-based banana quality assistance**

Use computer vision to estimate *visible* indicators such as ripeness, colour consistency, surface blemishes and apparent damage. The result will include confidence or uncertainty information where possible. Image analysis cannot establish hidden defects, moisture, weight, sweetness or certified grade, so buyers will still need appropriate physical checks.

**2. Market price recommendations**

Explore models using historical market prices, location, season, variety and available quantity to suggest indicative price ranges. Recommendations will depend on access to reliable, sufficiently detailed data and will not be presented as guaranteed sale prices.

**3. Farmer–buyer matching**

Recommend relevant buyers and listings using crop type, quantity, location, buyer requirements and other suitable signals. Start with understandable rule-based ranking before adding machine learning where it provides measurable value.

**4. Regional language assistance**

Explore Marathi and Hindi support, including voice-assisted listing creation and translation, with user confirmation before publishing or sending important information.

## Proposed technology stack

| Area | Technology | Role |
| --- | --- | --- |
| Web frontend / PWA | Next.js, TypeScript, Tailwind CSS | Responsive farmer and buyer experience |
| Backend | Python, FastAPI | APIs, authentication, business rules and real-time communication |
| Database | PostgreSQL, SQLAlchemy, Alembic | Structured data, database access and migrations |
| Real-time features | FastAPI WebSockets | Chat and live offer updates |
| Computer vision | PyTorch, OpenCV | Image preprocessing and model inference |
| Price modelling | Scikit-learn | Baseline models and evaluation |
| Image storage | S3-compatible object storage | Listing images |
| Testing | Pytest, Playwright | Backend, integration and browser tests |
| Development / CI | Docker, GitHub Actions | Local setup and automated checks |

**Architecture approach:** Keep the initial backend as one modular FastAPI application. Separate modules for users, listings, offers, deals, chat and AI tasks through clear interfaces rather than starting with multiple microservices. PostgreSQL will store trading records; images will live in object storage. If AI inference becomes slow, it can later run in background workers without changing the core marketplace API.

## Initial architecture (proposed)

```text
Browser / installable PWA (Next.js)
                |
         HTTPS + WebSockets
                |
        FastAPI application
     /       |       |       \
  Users   Listings  Offers   Chat / Deals
                |
       PostgreSQL database
                |
     AI analysis / price models
                |
       Object image storage
```

This is a conceptual diagram, not a deployed architecture.

## Repository structure (target)

```text
krishibazaar-ai/
├── frontend/                 # Next.js PWA
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── services/
│   │   ├── db/
│   │   └── ai/
│   └── tests/
├── docs/                     # Architecture and development notes
├── docker-compose.yml       # Planned local services
└── README.md
```

The folders and configuration shown above are a **planned structure** and may not yet exist in the repository.

## Development roadmap

| Phase | Scope | Status |
| --- | --- | --- |
| 0. Planning | Requirements, architecture, repository setup and data model | Planned |
| 1. Marketplace foundation | Authentication, profiles, listings, image uploads and search | Not started |
| 2. Trading workflow | Offers, counteroffers, chat, deal confirmation and notifications | Not started |
| 3. AI quality assistance | Banana image dataset, baseline model, evaluation and inference API | Not started |
| 4. Pricing and matching | Market data pipeline, price estimates and buyer recommendations | Not started |
| 5. Pilot and expansion | Feedback from farmers/buyers; evaluate more crops | Not started |

**Planned development start:** Around **11–12 November 2026**.

The first goal is a usable banana-trading MVP. New crops and more advanced AI features will be added only after the basic marketplace is working and tested.

## Engineering challenges to explore

- **Concurrent offers and deals:** Prevent overselling, duplicate acceptance and inconsistent listing status when several buyers act at once. Use database transactions, constraints and concurrency tests.
- **Reliable real-time chat:** Handle reconnects, message ordering, delivery state and user permissions.
- **Secure image uploads:** Validate file type and size, protect storage access and handle failed uploads.
- **AI evaluation:** Use labelled examples and held-out test data; report meaningful metrics and avoid treating visual estimates as certified quality.
- **Price data quality:** Handle missing, stale or geographically mismatched market records and evaluate prediction error against appropriate baselines.
- **Low-bandwidth usability:** Optimise image sizes, mobile performance and error handling for users with inconsistent connectivity.
- **Privacy and trust:** Protect personal/contact information, apply role-based access and add verification or dispute workflows as needed.

## How I plan to use an AI coding assistant

An AI coding assistant will be part of my regular development workflow, not just a tool for generating isolated snippets. I plan to use it to:

- Explore architectural choices and review trade-offs before implementation.
- Scaffold and refine Next.js components, FastAPI endpoints and database models.
- Help implement the bidding state machine, WebSocket flows and transaction handling.
- Generate unit, integration, concurrency and end-to-end test cases.
- Assist with image preprocessing, computer vision experiments and model integration.
- Investigate bugs, explain failures, suggest fixes and review security risks.
- Profile slow APIs and database queries and compare possible optimisations.
- Improve documentation, CI checks, Docker setup and code maintainability.

I will review generated changes, run tests and validate important behaviour myself. For AI features, I will compare results against suitable baselines and real test data rather than relying on generated claims. The project should provide ongoing opportunities for AI-assisted coding across frontend, backend, real-time systems and machine learning.

## Getting started

The application has **not been implemented yet**, so there are currently no verified installation or run commands. Once the initial frontend and backend are committed, this section will include prerequisites, environment variables, local setup commands, migrations, test commands and deployment instructions.

No production credentials, private keys or real user data should be committed to this repository.

## Ownership, contributions and licence

This is an independently planned personal project. Development and code ownership will be subject to the terms of any agreements I enter into.

**Licence:** Not selected yet. Until a licence is added, no open-source licence is granted by this repository. Public visibility alone does not make the code open source.

External contributions are not being accepted at this early planning stage. Contribution guidelines may be added later.

## Longer-term vision

Start with banana trading, learn from real farmers and buyers, and expand only where the platform offers practical value. The broader goal is to create a trusted digital agricultural marketplace that improves access to buyers, makes offers easier to compare and helps farmers make more informed selling decisions.

---

*KrishiBazaar AI — Direct connections, clearer deals, better market access.*
