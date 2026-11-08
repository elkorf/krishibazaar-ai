# KrishiBazaar AI — Frontend

> **Status:** Planned. Development is expected to begin around 11–12 November 2026. Features described below are targets, not completed functionality.

## Purpose

The frontend will be a mobile-first agricultural marketplace where farmers can post produce and buyers can discover listings, make offers, and negotiate directly. We will launch with **bananas**, while keeping the interface ready for additional crops later.

## Technology

- **Next.js** with **TypeScript** for the web application
- **Tailwind CSS** for responsive styling
- **Progressive Web App (PWA)** capabilities so supported devices can install the website
- **FastAPI backend** for business data and real-time updates

We are intentionally starting with one responsive web codebase instead of separate Android and iOS apps.

## Planned user journeys

### Farmer
1. Register or sign in.
2. Create a produce listing with photos, crop/variety, quantity, unit, location, expected price, and harvest date.
3. Review incoming offers and negotiate in chat.
4. Accept a suitable offer and view deal history.
5. Review preliminary AI image-quality feedback when available.

### Buyer
1. Register or sign in.
2. Browse and filter listings by crop, location, quantity, and price.
3. Open listing details and submit an offer in ₹ per quintal (or another clearly shown unit).
4. Exchange messages and counteroffers.
5. View agreed deals and their status.

## Planned pages

| Route (proposed) | Purpose |
| --- | --- |
| `/` | Landing page and marketplace introduction |
| `/marketplace` | Listing feed and filters |
| `/listings/[id]` | Listing details, photos, and offers |
| `/listings/new` | Farmer listing form |
| `/dashboard` | Role-aware farmer or buyer dashboard |
| `/messages` | Negotiation conversations |
| `/deals` | Agreed deals and history |
| `/login`, `/register` | Authentication |

Actual routes may change during implementation.

## Frontend principles

- **Mobile-first:** usable on affordable smartphones and slower connections.
- **Accessible:** clear labels, keyboard support, readable text, and meaningful error messages.
- **Regional-language ready:** design UI copy so Marathi and Hindi can be added without major rewrites.
- **Trustworthy pricing:** always show currency, quantity unit, and whether a figure is an asking price, offer, or AI estimate.
- **Honest AI feedback:** show confidence and limitations; never present image-based assessments as certified grades.
- **Minimal coupling:** frontend communicates with the backend through a small, typed API client; business rules stay on the server.

## Suggested directory structure

```text
frontend/
├── app/                  # Next.js routes and layouts
├── components/           # Reusable UI elements
├── features/             # Marketplace, listings, offers, chat
├── lib/                  # Typed API client and utilities
├── public/               # Static assets and PWA icons
├── tests/                # UI and end-to-end tests
└── README.md
```

## API integration (planned)

- REST endpoints for authentication, listings, images, offers, and deals.
- WebSockets for live chat and offer updates.
- Frontend displays AI assessments returned by the backend; it does not run heavy models in the browser.
- Authentication and authorisation will be enforced by the backend, not only by hidden UI controls.

## Local development

The application has **not been scaffolded yet**. After creating the Next.js project, the expected workflow will be similar to:

```bash
cd frontend
npm install
npm run dev
```

These commands will work only after the Next.js app and `package.json` are added. Local environment variables and backend URLs will be documented at that time.

## Testing plan

- Component tests for forms, listing cards, and offer states.
- End-to-end tests for listing creation, offer submission, and deal confirmation.
- Responsive checks on mobile screen sizes.
- Accessibility checks and tests for loading/error states.
- PWA installability and offline-fallback checks on supported browsers.

## Milestones

1. Scaffold Next.js, create navigation and design system.
2. Build listing feed, listing details, and farmer submission form.
3. Connect login, listings, and offers to FastAPI.
4. Add chat, notifications, and deal status UI.
5. Display AI quality estimates and price recommendations.
6. Configure and validate PWA support.

## AI coding assistant workflow

I plan to use an AI coding assistant to prototype components, review accessibility, create typed API integrations, generate tests, investigate bugs, and improve performance. All generated code will be reviewed and tested before it is accepted.

## Project status and licence

This is an independently planned project. No licence has been selected yet; check the repository root for any future `LICENSE` file. Until a licence is added, do not assume reuse or redistribution rights.
