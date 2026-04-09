# DUA Streamliner — Frontend Design Specification

**Project:** DUA Streamliner  
**Problem Statement:** Automate the generation of the Documento Único Aduanero (DUA) in Costa Rica by extracting structured data from heterogeneous document sources (PDF, Excel, Images, Word) using AI and OCR pipelines, eliminating manual data entry and reducing error rates in customs declarations.  
**Authors:** Ben Farzamipour Alfaro - 2023121041 (Agent Role: Senior Software Architect)
**Version:** 1.0.0  
**Date:** 2026-03-04

---

# 1. Frontend Design

## 1.1 Technology Stack
Frontend technology, security, third-party libraries, frameworks, hosting; all with their respective versions.

- **Application type:** Single-Page Application (SPA)
- **Web framework:** React `18.3.1`
- **Web server:** Nginx `1.26.2`
- **Coding language:** TypeScript `5.4.5`
- **Unit testing framework:** Vitest `1.6.0` + React Testing Library `15.0.0`
- **Data validation framework:** Zod `3.23.8`
- **Code formatter:** Prettier `3.3.3`
- **Code style framework:** ESLint `9.5.0` + eslint-config-airbnb-typescript `18.0.0`
- **Integration testing tools:** Playwright `1.44.1`
- **Cloud service:** Amazon Web Services (AWS)
- **Hosted services within the cloud service:**
  - AWS S3 `— ` Static asset storage
  - AWS CloudFront `— ` CDN + edge caching
  - AWS Cognito `— ` Identity & access management
  - AWS WAF `— ` Web application firewall
- **Code repositories service:** GitHub
- **Code automation task tool:** Nx `19.3.0`
- **CI/CD pipelines technology:** GitHub Actions
- **Environments:** `development` · `staging` · `production`
- **Environment deployment tools:** AWS CDK `2.147.0`
- **Observability framework:** OpenTelemetry JS `0.51.1` + AWS CloudWatch + Sentry `8.9.2`

---

## 1.2 UX/UI Analysis
Includes the desired usability attributes, preliminary wireframe designs, UX process steps, and validation evidence from real user testing.

### 1.2.1 Core bussiness process.
Describe step by step what happens on each screen in terms of actions (nothing about buttons, nor interface elements, only user actions) and the result of each action.

### 1.2.1.1 Login
Action: The user provides their identity and confirms their access through a second authentication factor.
Result: The system validates the permissions associated with the role (agent, auditor or administrator) and establishes a secure and encrypted session.

### 1.2.1.2 Configure generator
Action: The user provides the source documentation (invoices, transport documents or Excel files) to the system.
Result: The system classifies the files and prepares the extraction engines (OCR/AI) to process the information according to the type of document.

### 1.2.1.3 Progress monitoring
Action: The user monitors the progress of the document processing and the status of the ingestion queue.
Result: The system reports in real time on the success of the extraction or if anomalies have been detected that require manual intervention.

### 1.2.1.4 Result obtention
Action: The user reviews the DUA fields generated automatically and corrects any discrepancies detected by the system.
Result: The system consolidates the validated information and prepares the document for submission to the customs authorities.

### 1.2.1.5 Logout
Action: The user logs out and closes the system.
Result: The system invalidates the access credentials and eliminates any temporary information associated with the session.

### 1.2.2 Wireframes
Title, description and image.

Login
- Login screen with fields for username and password, and login button.
![wireframe1](wireframes/login_wireframe.png)

Configure generator
- Generator configuration screen with fields to explore files, and cancel engine preparation.
![wireframe2](wireframes/configurar_generador_wireframe.png)

Progress monitoring
- Progress monitoring screen with fields to view the progress of document processing and the status of the ingestion queue.
![wireframe3](wireframes/monitoreo_avance_wireframe.png)

Result obtention
- Result obtention screen with fields to view the DUA fields generated automatically and correct any discrepancies detected by the system.
![wireframe4](wireframes/obtencion_resultado_wireframe.png)

Logout
- Logout screen with fields to close the session.
![wireframe5](wireframes/logout_wireframe.png)


### UX test results
For this UX test, Figma was used to simulate the user's actions and record the heatmaps (with Maze integration).

### Table with the test results

| Participant | State | Total Time | Screens Visited | Navigation Observation |
| --- | --- | --- | --- | --- |
| P1 | Completed | 46.2s | 6 | Linear and fast navigation. |
| P2 | Completed | 62.4s | 8 | Recurrent exploration between Monitoring and Results. |
| P3 | Completed | 93.5s | 11+ | Erratic path; returned several times to the start before completing. |
| Average | - | 67.4s | 8.3 | High exploration flow. |

### Evidence of the test execution

Clip of the test execution (download from ./heatmaps UX Test/evidence_clip.mp4)
<video src="./heatmaps%20UX%20Test/evidence_clip.mp4" controls width="100%">
  Your Markdown viewer does not support embedded video.
</video>

Start of the test for all users
![evidence_login_start](./heatmaps%20UX%20Test/evidencia_login.png)

End of the test for all users
![evidence_login_end](./heatmaps%20UX%20Test/evidencia_fin.png)

### Heatmaps

Heatmap of the login page
![heatmap_login](./heatmaps%20UX%20Test/login_heatmap.jpg)

Heatmap of the ingestion page
![heatmap_ingestion](./heatmaps%20UX%20Test/ingestion_heatmap.jpg)

Heatmap of the dashboard page
![heatmap_dashboard](./heatmaps%20UX%20Test/dashboard_heatmap.jpg)

Heatmap of the review page
![heatmap_review](./heatmaps%20UX%20Test/review_heatmap.jpg)

Heatmap of the settings page
![heatmap_settings](./heatmaps%20UX%20Test/settings_heatmap.jpg)


## 1.3 Component Design Strategy
Define the design techniques and principles for frontend components, how component reuse is achieved, how styles, branding, internationalization, and responsiveness are centralized.

### Methodology: Atomic Design

Components are organized using the Atomic Design taxonomy:

| Level | Description | Examples |
|---|---|---|
| **Atoms** | Single-purpose, stateless primitives | `Button`, `Badge`, `Input`, `Tooltip`, `Icon` |
| **Molecules** | Composed atoms with local state | `FormField`, `ConfidenceBadge`, `FileCard`, `SearchBar` |
| **Organisms** | Feature-complete sections | `DocumentUploader`, `DUAFormSection`, `ExtractionReviewPanel` |
| **Templates** | Layout shells, no business logic | `TwoColumnLayout`, `DashboardShell`, `AuthLayout` |
| **Pages** | Route-bound, data-connected | `IngestionPage`, `ReviewPage`, `DUAEditorPage`, `DashboardPage` |

### Reusability Principles

- All atoms and molecules are **headless-first**: behavior is decoupled from presentation via `shadcn/ui` primitives.
- Compound components use the **Compound Component Pattern** (context + sub-components) for complex UI like the `DUAForm`.
- Shared hooks in `/src/hooks/` encapsulate all reusable side-effect logic.

### Design Tokens (Centralized Styling)

Defined in `/src/styles/tokens.css` as CSS custom properties, consumed by Tailwind via `tailwind.config.ts`:

```css
/* /src/styles/tokens.css */
:root {
  /* Confidence Semaphore */
  --color-confidence-high:   #22c55e;
  --color-confidence-medium: #eab308;
  --color-confidence-low:    #ef4444;

  /* Brand */
  --color-brand-primary:   #1d4ed8;
  --color-brand-secondary: #0f172a;

  /* Surfaces */
  --color-surface-base:    #ffffff;
  --color-surface-raised:  #f8fafc;
  --color-surface-overlay: #1e293b;

  /* Typography */
  --font-family-sans: 'Inter', system-ui, sans-serif;
  --font-size-base:   1rem;

  /* Spacing scale */
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-4: 1rem;
  --space-8: 2rem;
}
```

### Internationalization (i18n)

- Provider: `i18next` with `react-i18next` bindings.
- Locale files: `/src/locales/{es,en}/` — namespace-per-feature (e.g., `dua.json`, `common.json`, `errors.json`).
- Lazy-loaded namespaces via `i18next-http-backend` to avoid bundling all translations upfront.
- Date/number formatting delegated to `Intl` API; currency formatting uses `Intl.NumberFormat` with `CRC`/`USD` locale awareness.
- Default locale: `es-CR`. Fallback: `en`.

### Responsiveness Strategy

| Breakpoint | Target | Layout Strategy |
|---|---|---|
| `sm` (640px) | Mobile — tablet | Single-column, stacked panels |
| `md` (768px) | Tablet landscape | Two-column with collapsible sidebar |
| `lg` (1024px) | Desktop | Full split-view (document + form) |
| `xl` (1280px+) | Wide desktop | Three-column with audit panel |

CSS container queries (`@container`) used for organism-level responsiveness independent of viewport width.

---

## 1.4 Security
Technologies, techniques and classes with their respective location in the project structure responsible for authentication and authorization of permissions and sessions.

### Authentication

| Concern | Technology | Location |
|---|---|---|
| Identity Provider | Auth0 (PKCE + OIDC) | `/src/auth/AuthProvider.tsx` |
| Token Storage | In-memory only (no localStorage) | `/src/auth/tokenStore.ts` |
| Silent Refresh | Auth0 SDK `useAuth0().getAccessTokenSilently()` | `/src/auth/useAuthToken.ts` |
| Protected Routes | `<RequireAuth>` HOC | `/src/router/ProtectedRoute.tsx` |

### Authorization

| Concern | Technology | Location |
|---|---|---|
| RBAC Roles | Auth0 custom claims (`permissions[]`) | Decoded in `/src/auth/usePermissions.ts` |
| Permission Guards | `<Can action="dua:submit" subject="DUA">` | `/src/auth/Can.tsx` (CASL integration) |
| UI-level gating | `usePermissions()` hook | Consumed in organisms and pages |

### Session Management

| Concern | Mechanism | Location |
|---|---|---|
| Session expiry | Auth0 idle timeout + refresh token rotation | `/src/auth/AuthProvider.tsx` |
| Logout | Full redirect logout, clears IDP cookie | `/src/auth/useLogout.ts` |
| CSRF protection | SameSite=Strict cookies on API layer | Backend-enforced; Axios sends no cookies |
| API request signing | Bearer token injected via Axios interceptor | `/src/services/http/authInterceptor.ts` |

---

## 1.5 Layered Design
Design and explanation of the various layers of the application on the frontend.

```
┌─────────────────────────────────────────────────────┐
│                  Presentation Layer                  │
│  /src/pages, /src/components/{organisms,templates}  │
│  React components, route definitions, UI state      │
├─────────────────────────────────────────────────────┤
│                 Application Layer                    │
│  /src/hooks, /src/store, /src/features/*            │
│  Use-case orchestration, TanStack Query mutations,  │
│  Zustand slice reducers, form submission workflows  │
├─────────────────────────────────────────────────────┤
│                   Service Layer                      │
│  /src/services                                      │
│  Axios instances, API adapters, Auth0 token mgmt,  │
│  WebSocket client, file upload multipart handlers   │
├─────────────────────────────────────────────────────┤
│                   Domain Layer                       │
│  /src/domain                                        │
│  TypeScript interfaces/types, Zod schemas,          │
│  business rule validators, value objects (DUA, Doc) │
└─────────────────────────────────────────────────────┘
```

| Layer | Dependency Rule | Key Constraint |
|---|---|---|
| Presentation | → Application | No direct service/domain imports |
| Application | → Service, Domain | No React rendering logic |
| Service | → Domain | No UI state, no Zustand |
| Domain | (none) | Pure TypeScript; zero framework imports |

---

## 1.6 Design Patterns
Design of classes with their respective location in the project structure, where it is necessary to apply object-oriented design patterns, such as: security, UI refresh, notification reception, state storage, API calls, asynchronous operations, session invalidation, event-driven programming, object creation.

| Pattern | Intent | Location in `/src` |
|---|---|---|
| **Observer** | React to extraction events pushed via WebSocket | `/src/services/ws/ExtractionEventBus.ts` |
| **Factory** | Instantiate document parsers based on MIME type | `/src/domain/factories/DocumentParserFactory.ts` |
| **Repository** | Abstract API data access behind a consistent interface | `/src/services/repositories/DUARepository.ts`, `DocumentRepository.ts` |
| **Strategy** | Swap confidence-scoring algorithms without consumer changes | `/src/domain/strategies/ConfidenceStrategy.ts` |
| **Proxy** | Intercept HTTP requests for auth token injection and logging | `/src/services/http/authInterceptor.ts`, `loggingInterceptor.ts` |
| **Compound Component** | Expose flexible composition API for complex form sections | `/src/components/organisms/DUAForm/` (Context + sub-components) |
| **Command** | Encapsulate user actions (submit, revert, bulk-approve) | `/src/features/dua/commands/` |
| **Facade** | Single entry point for AI extraction service calls | `/src/services/ExtractionFacade.ts` |
| **Adapter** | Normalize heterogeneous API response shapes to domain types | `/src/services/adapters/ExtractionAdapter.ts`, `DUAAdapter.ts` |
| **Singleton** | Single Axios instance and i18n instance across the app | `/src/services/http/httpClient.ts`, `/src/i18n/index.ts` |

---

## 1.7 Scaffold
A folder in /src that contains the project scaffold, which is generated from the entire specification of points 1.1 to 1.6.

```
/src
├── assets/
│   ├── fonts/
│   └── images/
│
├── auth/
│   ├── AuthProvider.tsx          # Auth0 provider wrapper
│   ├── Can.tsx                   # CASL permission guard component
│   ├── tokenStore.ts             # In-memory token store
│   ├── useAuthToken.ts           # Hook: getAccessTokenSilently
│   ├── useLogout.ts              # Hook: full redirect logout
│   └── usePermissions.ts         # Hook: decode Auth0 custom claims
│
├── components/
│   ├── atoms/
│   │   ├── Badge/
│   │   ├── Button/
│   │   ├── Icon/
│   │   ├── Input/
│   │   ├── Spinner/
│   │   └── Tooltip/
│   ├── molecules/
│   │   ├── ConfidenceBadge/      # Semaphore visual indicator
│   │   ├── FileCard/
│   │   ├── FormField/
│   │   └── SearchBar/
│   ├── organisms/
│   │   ├── DocumentUploader/     # Drag-and-drop + progress
│   │   ├── DUAForm/              # Compound component
│   │   │   ├── DUAFormContext.ts
│   │   │   ├── DUAFormRoot.tsx
│   │   │   ├── DeclarantSection.tsx
│   │   │   ├── GoodsSection.tsx
│   │   │   ├── ValuationSection.tsx
│   │   │   └── CustomsRegimeSection.tsx
│   │   ├── ExtractionReviewPanel/
│   │   ├── AuditLogTable/
│   │   └── NavigationSidebar/
│   └── templates/
│       ├── AuthLayout.tsx
│       ├── DashboardShell.tsx
│       └── TwoColumnLayout.tsx
│
├── domain/
│   ├── entities/
│   │   ├── DUA.ts                # Core DUA value object
│   │   └── Document.ts           # Ingested document entity
│   ├── factories/
│   │   └── DocumentParserFactory.ts
│   ├── schemas/                  # Zod schemas
│   │   ├── dua.schema.ts
│   │   └── document.schema.ts
│   ├── strategies/
│   │   └── ConfidenceStrategy.ts
│   └── types/
│       ├── api.types.ts
│       ├── dua.types.ts
│       └── extraction.types.ts
│
├── features/
│   ├── dashboard/
│   │   ├── hooks/
│   │   └── components/
│   ├── dua/
│   │   ├── commands/             # Command pattern actions
│   │   │   ├── SubmitDUACommand.ts
│   │   │   ├── RevertFieldCommand.ts
│   │   │   └── BulkApproveCommand.ts
│   │   ├── hooks/
│   │   │   ├── useDUAEditor.ts
│   │   │   └── useFieldValidation.ts
│   │   └── store/
│   │       └── duaSlice.ts
│   └── ingestion/
│       ├── hooks/
│       │   └── useDocumentUpload.ts
│       └── store/
│           └── ingestionSlice.ts
│
├── hooks/                        # Shared application hooks
│   ├── useDebounce.ts
│   ├── useMediaQuery.ts
│   └── useToast.ts
│
├── i18n/
│   ├── index.ts                  # i18next singleton init
│   └── locales/
│       ├── en/
│       │   ├── common.json
│       │   ├── dua.json
│       │   └── errors.json
│       └── es/
│           ├── common.json
│           ├── dua.json
│           └── errors.json
│
├── pages/
│   ├── DashboardPage.tsx
│   ├── DUAEditorPage.tsx
│   ├── IngestionPage.tsx
│   ├── LoginPage.tsx
│   ├── ReviewPage.tsx
│   └── AdminPage.tsx
│
├── router/
│   ├── index.tsx                 # Route definitions, lazy loading
│   └── ProtectedRoute.tsx        # RequireAuth HOC
│
├── services/
│   ├── adapters/
│   │   ├── DUAAdapter.ts
│   │   └── ExtractionAdapter.ts
│   ├── http/
│   │   ├── httpClient.ts         # Axios singleton
│   │   ├── authInterceptor.ts    # Proxy: token injection
│   │   └── loggingInterceptor.ts # Proxy: observability
│   ├── repositories/
│   │   ├── DUARepository.ts
│   │   └── DocumentRepository.ts
│   ├── ws/
│   │   └── ExtractionEventBus.ts # Observer: WebSocket events
│   └── ExtractionFacade.ts       # Facade: AI extraction calls
│
├── store/
│   └── index.ts                  # Zustand root store composition
│
├── styles/
│   ├── tokens.css                # Design tokens (CSS custom props)
│   ├── globals.css               # Base reset + Tailwind directives
│   └── animations.css            # Keyframes and transitions
│
├── utils/
│   ├── formatters.ts             # Date, currency, number formatting
│   ├── confidence.ts             # Semaphore score → color mapping
│   └── errorHandler.ts           # Global error normalization
│
├── App.tsx
├── main.tsx
└── vite-env.d.ts
```

# Backend Design

## 2.1 Technology Stack
Backend technology, security, third-party libraries, frameworks, and hosting; all with their respective versions.

- **API style:** REST over HTTPS, contract defined with OpenAPI `3.1`
- **Coding language:** Python `3.12`
- **Web framework:** FastAPI `0.111`
- **Data validation:** Pydantic `v2`
- **ORM:** SQLAlchemy `2.0` (async)
- **Unit testing:** Pytest `8.2` + pytest-asyncio `0.23`
- **Cloud service:** Microsoft Azure
- **Hosted services within the cloud service:**
  - Azure App Service `— ` API hosting (Linux, Python runtime)
  - Azure API Management `— ` Gateway: routing, rate limiting, OpenAPI publishing
  - Azure Service Bus `— ` Async message queue for file processing events
  - Azure Notification Hubs `— ` Push notifications to frontend clients
  - Azure Blob Storage `— ` Raw file storage (uploads, DUA templates, output)
  - Azure SQL Database `— ` Relational data persistence
  - Azure Cache for Redis `— ` Template and permission caching
  - Azure Key Vault `— ` Secrets, API keys, connection strings
- **Code repositories service:** GitHub (monorepo, backend folder: `duabusiness/`)
- **CI/CD pipelines technology:** GitHub Actions
- **Environments:** `development` · `staging` · `production`
- **Environment deployment tools:** Azure Bicep `0.28`
- **Observability:** OpenTelemetry Python `1.24` + Azure Application Insights + Azure Monitor

---

## 2.2 Security
Technologies and mechanisms responsible for transport security, authentication, authorization, and data protection.

### Transport & Network

| Concern | Mechanism |
|---|---|
| Transport | TLS 1.3 enforced by Azure API Management; HTTP redirected to HTTPS |
| DB encryption at rest | Azure SQL Transparent Data Encryption (TDE) — AES-256 |
| Secrets storage | Azure Key Vault; never hardcoded in source |

### Authentication & Authorization

Auth is **delegated to Auth0** (same IdP as the frontend), validated on the backend as Bearer JWT:

| Concern | Mechanism | Location |
|---|---|---|
| Token validation | Auth0 JWKS public key verification | `/duabusiness/api/v1/dependencies.py` |
| RBAC roles | JWT custom claim `permissions[]` | `/duabusiness/domain/value_objects/UserPermission.py` |
| Permission guard | FastAPI dependency injection | `/duabusiness/api/v1/dependencies.py` |

Roles and permissions mirror the frontend specification: **Manager** (`MANAGE_USERS`, `VIEW_REPORTS`, `EDIT_TEMPLATES`) and **Customs Agent** (`LOAD_FILES`, `GENERATE_DUA`, `DOWNLOAD_DUA`).

### API Protection

| Concern | Mechanism |
|---|---|
| Rate limiting | Azure API Management policy: 100 req/min per client |
| Max payload (general) | 10 MB — enforced at Azure API Management |
| Max payload (file upload endpoints) | 50 MB per file — exception policy on `/documents/upload` |
| OWASP API Top 10 | Input validation via Pydantic, Azure API Management WAF policies |
| CORS | Restricted to known frontend origins via FastAPI `CORSMiddleware` |

### Data Retention

| Tier | Duration | Storage |
|---|---|---|
| Active (production) | 12 months | Azure SQL + Azure Blob Storage (hot tier) |
| Archive | 5 years | Azure Blob Storage (cold tier, automated lifecycle policy) |

---

## 2.3 Observability
Instrumentation strategy, event catalog, and tooling for monitoring the backend.

### Tooling

| Concern | Technology |
|---|---|
| Tracing | OpenTelemetry Python SDK → Azure Application Insights |
| Logs | Structured JSON (Python `structlog`) → Azure Monitor / Log Analytics |
| Metrics | Azure Application Insights live metrics (latency p95, error rate, throughput) |
| Dashboards | Azure Monitor Workbooks |
| Health checks | `GET /health` (liveness) and `GET /health/ready` (readiness) |

### Correlation

Every request receives a `X-Correlation-ID` header (generated at Azure API Management if absent); all log entries and traces carry this ID throughout the async pipeline.

### Event Catalog

| Event | Trigger | Level |
|---|---|---|
| `user.session.start` | Successful JWT validation on first request | INFO |
| `user.session.end` | Session timeout or explicit logout | INFO |
| `document.upload.started` | File metadata received | INFO |
| `document.upload.completed` | All files stored in Blob Storage | INFO |
| `document.upload.failed` | Validation or storage error | ERROR |
| `dua.generation.started` | `DUA_GENERATION_REQUESTED` event dequeued | INFO |
| `dua.generation.completed` | AI extraction pipeline finished | INFO |
| `dua.generation.failed` | Unrecoverable extraction error | ERROR |
| `extraction.confidence.low` | Any field confidence score < 0.6 | WARNING |
| `api.error.4xx` | Client error on any endpoint | WARNING |
| `api.error.5xx` | Server error on any endpoint | ERROR |

---

## 2.4 Infrastructure (DevOps)
Tooling for automated deployment, environment management, and infrastructure provisioning.

### CI/CD Pipeline (GitHub Actions)

```
Push / PR → GitHub Actions
  ├─ Lint & Type-check (ruff, mypy)
  ├─ Unit tests (pytest)
  ├─ Integration tests (pytest + testcontainers)
  ├─ Docker image build + Trivy vulnerability scan
  └─ Deploy
       ├─ main branch    → development slot
       ├─ staging tag    → staging slot  (manual approval gate)
       └─ release tag    → production    (blue/green swap via App Service deployment slots)
```

### Infrastructure as Code

- **Tool:** Azure Bicep `0.28` — one module per resource (App Service, API Management, SQL, Service Bus, Key Vault, Blob Storage, Redis)
- **State:** tracked via Azure Resource Manager (no external state file needed)
- **Parameterization:** separate `.bicepparam` files per environment (`dev` / `staging` / `prod`)

### Deployment Strategy

Blue/Green via **Azure App Service deployment slots**: the new version is deployed to the `staging` slot, smoke-tested, then swapped to `production` with zero downtime. Rollback = reverse swap.

---

## 2.5 Availability
SLA targets, redundancy strategy, and failure mitigation for each critical component.

- **Target SLA:** 99.9% (~8.7 hours/year downtime) — covered by Azure App Service Standard S2 tier

### Redundancy

| Component | Redundancy Mechanism |
|---|---|
| Azure App Service | Availability zones enabled (Standard+); min 2 instances |
| Azure SQL | General Purpose tier — zone-redundant HA, automatic failover |
| Azure Service Bus | Standard tier — geo-redundant by default within region |
| Azure Blob Storage | LRS (locally redundant); upgrade to ZRS if compliance requires |
| Azure Cache for Redis | Standard tier — primary + replica |

### Single Points of Failure & Recovery

| SPOF | Recovery Strategy |
|---|---|
| App Service instance crash | Health probes auto-restart; auto-scale maintains min 2 instances |
| Azure SQL failover | Automatic failover group; SQLAlchemy retry on `OperationalError` |
| Azure Notification Hubs timeout | Messages re-queued via Azure Service Bus dead-letter queue |
| External AI/OCR API unavailable | Fields flagged `LOW_CONFIDENCE`; fallback to manual review mode |
| Azure API Management outage | App Service direct endpoint available as emergency bypass |

### Resilience Patterns

- **Circuit breaker:** `tenacity` library on all external AI/OCR calls (3 retries, exponential backoff, 30 s open window)
- **Timeout:** 60 s on AI extraction calls; 10 s on all other external HTTP calls
- **Bulkhead:** Azure Service Bus queue isolates file processing from the synchronous API path

---

## 2.6 Scalability
Elements that grow under increased request volume and the strategy to scale each.

- **Stateless API layer:** no server-side session; all state in JWT or DB. App Service can scale horizontally without session affinity.

| Bottleneck | Scaling Strategy |
|---|---|
| API throughput | App Service auto-scale: CPU > 70% → add instance (max 5) |
| File processing pipeline | Azure Service Bus queue → independent worker (App Service WebJob); scale workers by queue depth |
| DB read load | Azure SQL read replica for reporting queries; Redis cache for DUA templates (TTL 5 min) |
| File storage I/O | Azure Blob Storage scales transparently; SAS URLs offload upload traffic from the API |
| Auth token validation | JWKS public key cached in-process; no external call per request |

---

## 2.7 Backend Key Workflows
Step-by-step description of the main backend operations.

### Upload files to generate DUA

1. Client calls `POST /documents/upload-request` with file metadata (names, sizes, MIME types).
2. Backend validates file types (`application/pdf`, `application/vnd.openxmlformats-officedocument.*`, `image/jpeg`, `image/png`) and sizes (max 50 MB each).
3. Backend generates time-limited **SAS URLs** (Azure Blob Storage) for each file and returns them.
4. Client streams each file directly to Azure Blob Storage using the SAS URLs (API not in the binary path).
5. Client calls `POST /documents/confirm-upload` with the list of blob references.
6. Backend persists file records in DB (status: `PENDING`) and publishes a `DUA_GENERATION_REQUESTED` event to Azure Service Bus.
7. Background worker dequeues the event, runs the OCR/AI extraction pipeline file by file.
8. Worker updates DB records per file (`PROCESSING` → `COMPLETED` or `FAILED`) and persists confidence scores per DUA field.
9. On completion, worker publishes a `DUA_READY` notification via Azure Notification Hubs to the client session.
10. Client receives push notification and calls `GET /dua/{id}` to retrieve the pre-filled DUA with confidence indicators.

### Setup DUA template

1. Manager calls `POST /templates` with a new DUA Word (`.docx`) template file.
2. Backend validates that all required field markers (e.g., `{{declarant_name}}`, `{{goods_description}}`) are present in the template.
3. File is stored in Azure Blob Storage under the `templates/` container.
4. Backend persists template metadata in DB: version, name, upload date, `active: true`.
5. Previous active template is soft-deprecated (`active: false`); data is retained for audit.
6. All subsequent `DUA_GENERATION_REQUESTED` events reference the new active template ID.

---

## 2.8 Architecture Diagrams

> Following the C4 model. The **Component diagram** is omitted per project scope.

### Context Diagram

```mermaid
graph TD
    CA(["👤 Customs Agent\n(Browser)"])
    MG(["👤 Manager\n(Browser)"])
    SYS["🖥️ DUA Streamliner\nSystem"]
    AUTH(["🔐 Auth0\nIdentity Provider"])
    AI(["🤖 AI / OCR\nExtraction API"])
    HAC(["🏛️ Ministerio de Hacienda\n(DUA Template Standard)"])

    CA -->|"Uploads files,\nreviews DUA"| SYS
    MG -->|"Manages users,\nconfigures templates"| SYS
    SYS -->|"Validates JWT,\nOIDC"| AUTH
    SYS -->|"Sends documents\nfor extraction"| AI
    SYS -.->|"Template format\nreference"| HAC
```

### Container Diagram

```mermaid
graph TD
    subgraph Browser
        FE["React SPA\nTypeScript 5.4\nAWS CloudFront + S3"]
    end

    subgraph Azure
        APIM["Azure API Management\nGateway · Rate limit · OpenAPI"]
        API["FastAPI App\nPython 3.12\nAzure App Service"]
        WORKER["Background Worker\nApp Service WebJob\nPython 3.12"]
        DB[("Azure SQL Database\nSQLAlchemy ORM")]
        BLOB[("Azure Blob Storage\nFiles · Templates · Output")]
        BUS["Azure Service Bus\nAsync event queue"]
        NH["Azure Notification Hubs\nPush to clients"]
        REDIS[("Azure Cache for Redis\nTemplate & permission cache")]
        KV["Azure Key Vault\nSecrets & config"]
    end

    AUTH0(["Auth0\nIdP OIDC/JWT"])
    AIAPI(["AI / OCR\nExternal API"])

    FE -->|"HTTPS / Bearer JWT"| APIM
    APIM -->|"Forwards request"| API
    API -->|"Validates JWT"| AUTH0
    API --- DB
    API --- BLOB
    API --- REDIS
    API --- KV
    API -->|"Publishes event"| BUS
    BUS -->|"Dequeues event"| WORKER
    WORKER -->|"Reads files"| BLOB
    WORKER -->|"Calls extraction"| AIAPI
    WORKER --- DB
    WORKER -->|"Push notification"| NH
    NH -->|"WebSocket / SSE"| FE
```

### Code Diagram (Key Classes)

```mermaid
classDiagram
    class DocumentParserFactory {
        +create(mime_type: str) DocumentParserStrategy
    }
    class DocumentParserStrategy {
        <<interface>>
        +parse(blob_url: str) ExtractionResult
    }
    class PDFParserStrategy {
        +parse(blob_url: str) ExtractionResult
    }
    class ImageOCRStrategy {
        +parse(blob_url: str) ExtractionResult
    }
    class DocxParserStrategy {
        +parse(blob_url: str) ExtractionResult
    }
    class XlsxParserStrategy {
        +parse(blob_url: str) ExtractionResult
    }

    class ExtractionFacade {
        -factory: DocumentParserFactory
        -event_bus: EventBus
        +run_pipeline(document_ids: list) DUA
    }

    class DUARepository {
        <<interface>>
        +get(id: UUID) DUA
        +save(dua: DUA) None
    }
    class DocumentRepository {
        <<interface>>
        +get(id: UUID) Document
        +update_status(id: UUID, status: str) None
    }

    class EventBus {
        <<singleton>>
        +publish(event: DomainEvent) None
    }

    class Settings {
        <<singleton>>
        +db_url: str
        +key_vault_url: str
        +blob_conn_str: str
    }

    DocumentParserFactory --> DocumentParserStrategy
    DocumentParserStrategy <|.. PDFParserStrategy
    DocumentParserStrategy <|.. ImageOCRStrategy
    DocumentParserStrategy <|.. DocxParserStrategy
    DocumentParserStrategy <|.. XlsxParserStrategy
    ExtractionFacade --> DocumentParserFactory
    ExtractionFacade --> DUARepository
    ExtractionFacade --> DocumentRepository
    ExtractionFacade --> EventBus
```

---

## 2.9 Design Patterns

| Pattern | Intent | Location in `duabusiness/` |
|---|---|---|
| **Factory** | Instantiate the correct document parser by MIME type | `/domain/factories/DocumentParserFactory.py` |
| **Strategy** | Swap parsing algorithm (PDF, DOCX, XLSX, Image/OCR) without changing caller | `/domain/strategies/` |
| **Repository** | Abstract DB access behind a consistent interface; decouples domain from SQLAlchemy | `/infrastructure/repositories/` |
| **Facade** | Single entry point for the full OCR/AI extraction pipeline | `/application/ExtractionFacade.py` |
| **Observer** | Publish domain events (extraction complete, DUA ready) to decoupled subscribers | `/infrastructure/events/EventBus.py` |
| **Singleton** | One instance of Settings, Redis client, and BlobStorageClient across the process | `/infrastructure/config/Settings.py`, `/infrastructure/storage/BlobStorageClient.py` |
| **Command** | Encapsulate each use case as an executable object (upload, generate DUA) | `/application/commands/` |

---

## 2.10 Scaffold

```
duabusiness/
├── api/
│   ├── v1/
│   │   ├── routes/
│   │   │   ├── auth.py               # Health / token introspection proxy
│   │   │   ├── dua.py                # GET /dua/{id}, GET /dua/{id}/status
│   │   │   ├── documents.py          # POST /documents/upload-request, /confirm-upload
│   │   │   └── templates.py          # POST /templates, GET /templates
│   │   └── dependencies.py           # JWT validation, RBAC guards (FastAPI Depends)
│   └── main.py                       # FastAPI app factory, middleware, routers
│
├── application/
│   ├── ExtractionFacade.py           # Facade: orchestrates full extraction pipeline
│   ├── services/
│   │   ├── DUAService.py
│   │   ├── DocumentService.py
│   │   └── TemplateService.py
│   └── commands/
│       ├── GenerateDUACommand.py
│       └── UploadFilesCommand.py
│
├── domain/
│   ├── entities/
│   │   ├── DUA.py
│   │   ├── Document.py
│   │   └── Template.py
│   ├── factories/
│   │   └── DocumentParserFactory.py  # Factory: returns parser by MIME type
│   ├── strategies/
│   │   ├── DocumentParserStrategy.py # Interface
│   │   ├── PDFParserStrategy.py
│   │   ├── DocxParserStrategy.py
│   │   ├── XlsxParserStrategy.py
│   │   └── ImageOCRStrategy.py
│   └── value_objects/
│       ├── ConfidenceScore.py
│       ├── DUAField.py
│       └── UserPermission.py
│
├── infrastructure/
│   ├── config/
│   │   └── Settings.py               # Singleton: env vars from Azure Key Vault
│   ├── database/
│   │   ├── connection.py             # Async SQLAlchemy engine
│   │   └── models/                   # ORM table definitions
│   │       ├── DUAModel.py
│   │       ├── DocumentModel.py
│   │       └── TemplateModel.py
│   ├── repositories/
│   │   ├── DUARepository.py
│   │   └── DocumentRepository.py
│   ├── events/
│   │   └── EventBus.py               # Observer: Azure Service Bus publisher
│   ├── storage/
│   │   └── BlobStorageClient.py      # Singleton: Azure Blob Storage SDK wrapper
│   └── notifications/
│       └── NotificationHubClient.py  # Azure Notification Hubs wrapper
│
├── tests/
│   ├── unit/
│   └── integration/
│
├── bicep/                             # IaC — one module per Azure resource
│   ├── main.bicep
│   ├── appservice.bicep
│   ├── sql.bicep
│   ├── servicebus.bicep
│   └── storage.bicep
│
├── .github/workflows/
│   └── backend-ci.yml                # Lint → Test → Build → Deploy
│
├── requirements.txt
└── pyproject.toml                     # ruff, mypy, pytest config
```

# Data Design