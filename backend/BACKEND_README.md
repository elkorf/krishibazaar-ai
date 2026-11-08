# KrishiBazaar AI — Backend

> **Status:** Planned. Development is expected to begin around 11–12 November 2026. The endpoints and modules below are proposed, not implemented.

## Purpose

The Python backend will manage accounts, crop listings, buyer offers, negotiations, deals, and AI-assisted insights for the KrishiBazaar marketplace. The first supported crop will be **bananas**, with a data model designed to accommodate more crops later.

## Technology

- **Python + FastAPI:** REST APIs and WebSockets
- **PostgreSQL:** transactional application data
- **SQLAlchemy + Alembic:** database access and schema migrations
- **PyTorch + OpenCV:** planned computer vision processing
- **Scikit-learn:** planned baseline price recommendation models
- **Pytest:** automated backend tests
- **Docker:** reproducible local environment and deployment
- **S3-compatible object storage:** listing photos (planned)

We will begin with a **modular monolith**, not a collection of microservices. Heavy AI tasks can be moved into background workers when necessary.

## Proposed modules

```text
backend/
├── app/
│   ├── main.py             # FastAPI entry point
│   ├── api/                # Routes and WebSocket endpoints
│   ├── core/               # Configuration and security
│   ├── db/                 # Sessions, migrations, repositories
│   ├── models/             # SQLAlchemy models
│   ├── schemas/            # Request/response validation
│   ├── services/           # Trading business logic
│   └── ai/                 # Image analysis and pricing
├── tests/
├── alembic/
└── README.md
```

This structure is a starting point and may change during development.

## Core entities (planned)

| Entity | Key information |
| --- | --- |
| User | Identity, role, contact details, verification status |
| Crop | Crop name and supported units/attributes |
| Listing | Farmer, crop, variety, quantity, location, expected price, status |
| ListingImage | Listing reference, storage key, upload metadata |
| Offer | Buyer, listing, offered price, quantity, status, timestamps |
| Conversation / Message | Participants, listing or offer context, message content |
| Deal | Accepted offer, agreed quantity/price, status and audit history |
| AIAnalysis | Listing/image reference, model version, results, confidence, timestamp |

Amounts will use decimal types rather than floating-point arithmetic. Quantity and price units must always be explicit. Sensitive information will not be publicly exposed by default.

## Proposed API surface

These are **illustrative routes**, not a published contract:

```text
GET    /health
POST   /auth/register
POST   /auth/login
GET    /crops
GET    /listings
POST   /listings
GET    /listings/{id}
POST   /listings/{id}/images
POST   /listings/{id}/offers
GET    /listings/{id}/offers
POST   /offers/{id}/accept
GET    /deals
GET    /conversations
WS     /ws/conversations/{id}
POST   /listings/{id}/quality-analysis
GET    /listings/{id}/quality-analysis
```

Endpoint naming, permissions, request schemas, and authentication details will be finalised during implementation.

## Key business rules

1. Only an authorised farmer can edit their own listing or accept an offer on it.
2. A buyer cannot accept their own offer on behalf of a farmer.
3. Offer amounts and quantities must be validated server-side.
4. Accepting an offer must use a database transaction and concurrency controls so the same available stock cannot be committed twice.
5. Listing, offer, and deal statuses must follow defined state transitions.
6. Chat access must be restricted to authorised conversation participants.
7. Important state changes should be auditable.

## AI features and limitations

### Image-based banana assessment

A planned computer vision pipeline will process uploaded photos to estimate visible ripeness, colour consistency, blemishes, and damage. It will include input validation, preprocessing, inference, model versioning, and confidence reporting.

**Important:** Photos cannot reliably establish internal quality, weight, chemical residue, or certified grade. Low-quality or unsupported images should return an uncertain result rather than a confident claim.

### Price recommendations

A planned model will use available historical mandi prices and contextual features such as crop variety, location, and season. Before showing recommendations, we will evaluate data coverage, accuracy, drift, and whether a simple baseline is more reliable than a complex model. Recommendations are estimates, not guaranteed selling prices.

### Buyer matching

Initial matching may use transparent filters and ranking rules. Machine learning can be considered later if enough relevant data is available.

## Performance and processing plan

- Store photos in object storage; keep metadata in PostgreSQL.
- Validate image size, format, and content before processing.
- Use background jobs for expensive image analysis rather than blocking normal API requests.
- Add pagination and database indexes for listing feeds.
- Introduce caching or queues only when measurements show a need.
- Apply request limits and appropriate timeouts to protect the service.

## Security and privacy plan

- Secure password hashing and session/token handling.
- Role-based authorisation on every protected API.
- Input validation and safe file-upload handling.
- Secrets stored in environment variables, never committed to Git.
- TLS in deployed environments, restricted CORS, and rate limiting.
- Data minimisation for farmers' and buyers' personal information.
- Logging that avoids exposing passwords, tokens, or private chat content.

## Local development

The FastAPI application has **not been scaffolded yet**. After the project files and dependencies are added, a typical workflow will be:

```bash
cd backend
python -m venv .venv
# Activate the virtual environment for your operating system
pip install -r requirements.txt
uvicorn app.main:app --reload
```

These commands are **not yet runnable** because `requirements.txt` and the application code still need to be created. A `docker-compose.yml` for PostgreSQL will be added during setup.

## Testing plan

- Unit tests for validation, pricing calculations, and deal state transitions.
- Integration tests for PostgreSQL transactions and API permissions.
- Concurrent-offer tests to verify that stock cannot be double-booked.
- WebSocket authentication and reconnection tests.
- Upload tests for invalid, oversized, and unsupported images.
- AI tests for reproducibility, confidence handling, and baseline accuracy.
- Load tests for listing reads, bids, and image-processing queues.

## Development milestones

1. Create FastAPI app, health check, configuration, and PostgreSQL setup.
2. Implement accounts, roles, crops, and listings.
3. Add image uploads, offers, and safe deal acceptance.
4. Add conversations and real-time updates.
5. Build the first banana image-analysis prototype and evaluate it.
6. Add price recommendations using validated market data.
7. Improve monitoring, security, performance, and deployment.

## AI coding assistant workflow

I plan to use an AI coding assistant extensively for API design, SQLAlchemy models, concurrency-safe transactions, image-processing experiments, automated tests, debugging, code reviews, and performance improvements. Generated solutions will be checked against requirements and verified with tests before merging.

## Project status and licence

This is an independently planned project. No licence has been selected yet; consult the root repository for future licence information. Without an explicit licence, reuse and redistribution rights are not granted by default.
