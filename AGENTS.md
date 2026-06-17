# AGENTS.md — DebtWise AI Developer Instructions

## 1. Product Vision

You are an expert AI full-stack developer building **DebtWise**, a personal debt decision-support application.

DebtWise must evolve deliberately through this product arc:

**Calculator → Mirror → Advisor → Coach**

The application should start as a reliable EMI calculator and loan dashboard, then become a personalized debt mirror, then a decision-support advisor, and eventually a financial coaching tool.

The core product promise is:

> Help users understand their loans, see their debt position clearly, compare choices, stress-test risk, and make better repayment decisions using transparent calculations.

DebtWise must not behave like a generic EMI calculator. It must educate users through the decisions they are already making.

Non-negotiable product principles:

1. **Transparency** — every score, warning, and recommendation must be decomposable into visible arithmetic.
2. **Honesty about downside** — the app must show risk and negative paths, not only optimistic savings.
3. **Education inside decisions** — explanations must appear near the relevant action, not in a separate ignored learning section.
4. **No false authority** — the app provides educational estimates and decision-support, not certified financial advice.

---

## 2. Approved Technology Stack

Use only the following stack unless the user explicitly approves a change.

### Frontend

- React with Vite
- Functional components and React Hooks
- React Router for routing
- Bootstrap as the primary UI framework
- Chart.js for charts and visualizations
- React-compatible Chart.js wrapper where appropriate, such as `react-chartjs-2`

### Backend

- Flask REST API
- Python
- SQLAlchemy ORM
- Flask-Migrate / Alembic for migrations
- Pytest for backend tests

### Database

- MySQL

### Authentication

- JWT-based authentication using secure, HttpOnly cookies
- Do not store JWTs in LocalStorage or SessionStorage
- Use bcrypt or Werkzeug password hashing

### Deployment

- Railway as the primary deployment target
- Render as fallback
- Use environment variables for all secrets and environment-specific configuration

### Do Not Introduce Without Permission

Do not introduce these unless the user explicitly asks:

- Redux
- Next.js
- TypeScript
- MongoDB
- PostgreSQL
- Tailwind CSS
- Material UI
- Django
- FastAPI
- Firebase
- Supabase
- OAuth providers
- External financial APIs
- Account Aggregator integration
- AI/LLM integrations

If a requested feature appears to require one of these, explain the trade-off and ask for confirmation before adding it.

---

## 3. Epic-Based Development Protocol

The user will usually provide **high-level epic prompts**, not detailed technical instructions.

When the user gives an epic, do the following:

1. Understand the product goal and user value.
2. Break the epic into small implementation tasks internally.
3. Choose best-practice implementation details within the approved stack.
4. Do not ask the user for low-level technical choices unless there is genuine ambiguity, a product trade-off, or a stack conflict.
5. Implement production-quality code with clean architecture.
6. Keep the implementation scoped to the requested epic.
7. Add backend tests for financial calculation logic.
8. Add or update migrations when database models change.
9. Ensure authentication-protected APIs are scoped to the current user.
10. Verify the app builds and tests pass where practical.
11. Summarize what changed, how to test it, and any assumptions made.

The user wants the AI developer to make good engineering decisions. Do not wait for the user to specify every function, folder, validation rule, or UI state.

---

## 4. Scope Control

DebtWise has a large roadmap. Do not implement future features prematurely.

Rules:

- Implement only the current epic or release requested by the user.
- Create extensible foundations when future features are obvious, but do not build them before they are requested.
- Do not combine multiple releases into one implementation unless the user asks.
- Do not silently add advanced features such as live FX, refinance detection, bureau score integration, Account Aggregator, or AI narration.
- If the user asks for a broad feature, implement the simplest correct version first and leave advanced variants as future work.

Example:

- If asked for **Debt Optimization**, first implement the lump-sum allocator.
- Do not automatically implement avalanche/snowball recurring strategy simulation unless explicitly requested.

---

## 5. Release Roadmap Context

Use this roadmap to keep the codebase extensible, but do not implement future releases unless requested.

The implementation roadmap is intentionally split into **six releases**. Keep this six-release structure aligned with release prompts, migrations, seed data, and scope boundaries.

### Release 1 — Foundation, Calculator, and Dashboard

Foundation features:

- App shell
- Authentication
- Loan CRUD
- EMI calculation
- Amortization schedule
- Basic dashboard
- Prepayment simulator
- Loan nicknames
- Dashboard charts
- Initial loan schema prepared for future releases

Initial loan schema should support these future-safe fields where practical:

- `status` with supported values: `active`, `considering`, `closed`
- `rate_type` with supported values: `fixed`, `floating`
- `native_currency`, defaulting to `INR`
- `tax_benefit_enabled`, defaulting to false
- `deductible_fraction`, defaulting to 0
- `prepayment_penalty_pct`, defaulting to 0
- `penal_charge_pct`, defaulting to 2.00 where relevant
- `palette_index`, nullable

Release 1 UI does not need to expose every advanced field, but the schema should avoid late-stage refactors where practical.

Real portfolio calculations must include only loans where:

```text
status = "active"
```

unless a feature explicitly says it is working with hypothetical or considering loans.

### Release 2 — Financial Health Profile and Personalization

Personalization features:

- User profile
- Monthly income
- Monthly non-loan obligations
- Emergency fund
- DTI / FOIR calculation
- DebtWise Health Score
- Transparent factor breakdown
- Score snapshots

### Release 3 — Advisor Features and Decision Support

Decision-support features:

- Affordability Advisor
- Loan Comparison Engine
- Effective APR calculation
- 5-year exit cost
- Basic lump-sum Debt Optimization Engine
- Future-compatible `optimization_runs` table

When `optimization_runs` is introduced, it should support future optimizer types from the beginning:

```text
optimization_type = lump_sum_optimizer | recurring_strategy
lump_sum_amount nullable
monthly_surplus nullable
inputs_json
results_json
created_at
```

### Release 4 — Risk, Resilience, Storytelling, and Display-Layer Currency

Risk and coaching features:

- Missed-payment stress simulator
- Rate-shock simulator
- Income-shock simulator
- Prepayment storytelling
- Personal storytelling anchors
- Display-layer currency conversion using static rates
- Dashboard payoff timeline and visual polish

### Release 5 — Advanced Recurring Strategy Optimizer

Advanced optimization features:

- Avalanche recurring strategy simulator
- Snowball recurring strategy simulator
- Hybrid recurring strategy simulator
- Month-by-month waterfall simulation
- EMI rollover when loans close
- First-win month and payoff sequence
- Side-by-side strategy comparison

### Release 6 — Final Polish and Product Hardening

Final polish features:

- Stable loan colors through `palette_index`
- Floating-rate exposure dashboard card
- Prepayment penalty handling in optimizer outputs
- Equal-payment loan comparison
- Active / considering / closed loan status hardening
- Product copy consistency pass
- Optional saved advisor run history if low-risk

### Future Coach Stage

Do not implement unless explicitly requested:

- Actual payment tracking intelligence
- Refinance detection
- Account Aggregator ingestion
- Bureau score comparison
- LLM-generated financial narratives
- Household/shared accounts
- Live FX APIs
- External banking integrations

---
## 6. Backend Architecture Rules

Use a clean Flask structure. Avoid one large `app.py` file.

Recommended backend structure:

```text
backend/
├── app/
│   ├── __init__.py
│   ├── config.py
│   ├── extensions.py
│   ├── models/
│   ├── routes/
│   ├── services/
│   ├── schemas/
│   ├── utils/
│   └── errors/
├── migrations/
├── tests/
├── requirements.txt
└── run.py
```

Rules:

- Use Flask application factory pattern where practical.
- Use Blueprints for route grouping.
- Keep route handlers thin.
- Put business logic and financial calculations in `services/`.
- Put SQLAlchemy models in `models/`.
- Put request validation schemas/helpers in `schemas/` or `validators/`.
- Put shared formatting, date, currency, and math helpers in `utils/`.
- Do not place financial formulas directly inside Flask route files.
- Do not duplicate formulas across multiple files.
- Return consistent JSON responses.
- Use appropriate HTTP status codes.

Recommended route groups:

```text
/api/auth
/api/profile
/api/loans
/api/dashboard
/api/calculations
/api/health-score
/api/affordability
/api/compare-loans
/api/stress-test
/api/optimize
```

---

## 7. Frontend Architecture Rules

Use a clean React structure.

Recommended frontend structure:

```text
frontend/
├── src/
│   ├── api/
│   ├── assets/
│   ├── charts/
│   ├── components/
│   ├── context/
│   ├── hooks/
│   ├── layouts/
│   ├── pages/
│   ├── routes/
│   ├── utils/
│   ├── App.jsx
│   └── main.jsx
├── index.html
└── package.json
```

Rules:

- Use pages for route-level screens.
- Use components for reusable UI.
- Use charts for Chart.js components.
- Use hooks for data-fetching and reusable stateful logic.
- Use API service files for HTTP calls.
- Do not put API calls directly inside deeply nested presentational components.
- Use React Context for authentication and user profile state.
- Use local component state for forms and simple UI state.
- Keep components small and readable.
- Prefer clear, boring, maintainable code over clever abstractions.
- Handle loading, empty, success, and error states for all data-driven screens.
- Use Bootstrap as the primary UI system.
- Custom CSS is allowed only for layout polish, dashboard cards, Chart.js containers, gauges, and visual elements that Bootstrap cannot express cleanly.

---
## 7A. UI/UX Design System and Professional Look-and-Feel

DebtWise must look and feel like a professional, trustworthy financial decision-support application.

The UI should be clean, modern, responsive, and user-friendly. Do not create a rough academic-project interface. Every screen should feel suitable for a serious personal finance product.

### Design Principles

Follow these product design principles:

* Prioritize clarity over decoration.
* Use a calm, trustworthy visual style suitable for financial data.
* Keep layouts clean, spacious, and easy to scan.
* Make important financial numbers highly visible.
* Use visual hierarchy: page title, summary cards, primary actions, secondary details.
* Avoid cluttered forms, crowded dashboards, and overly colorful screens.
* Use consistent spacing, typography, cards, buttons, badges, and chart styles.
* Use Bootstrap as the primary UI system, but custom CSS is allowed for layout polish, dashboard cards, Chart.js containers, gauges, timelines, and professional visual refinement.

### Visual Style

Use a professional dashboard style:

* White or very light background.
* Clean cards with subtle borders or shadows.
* Rounded corners.
* Consistent spacing.
* Clear section headings.
* Muted neutral colors for structure.
* Green only for positive savings or healthy states.
* Amber/yellow only for caution.
* Red only for risk, warning, overdue, or high-stress states.
* Avoid loud gradients, random colors, excessive animations, and decorative clutter.

### Financial Dashboard UX

Dashboard screens should use:

* Summary cards for key metrics.
* Clear chart titles and short explanatory subtitles.
* Responsive Chart.js charts.
* Empty states when no data exists.
* Loading states during API calls.
* Error states with helpful messages.
* Tooltips or helper text for financial terms such as EMI, DTI / FOIR, effective rate, APR, and interest saved.

### Forms and User Input

Forms should be clean and easy to complete.

Requirements:

* Use clear labels.
* Use helpful placeholders.
* Show validation errors close to the relevant field.
* Use currency-friendly input formatting where practical.
* Avoid overwhelming the user with too many fields at once.
* Group related fields together.
* Use sensible defaults when safe.
* Use primary and secondary buttons consistently.

### Charts and Data Visualization

Charts must be professional and readable.

Requirements:

* Use Chart.js consistently.
* Charts must be responsive.
* Always provide chart titles.
* Avoid unnecessary 3D effects or decorative chart styling.
* Use stable loan colors where available.
* Do not use misleading scales.
* Use green, amber, and red carefully and only when they communicate meaning.
* Show legends clearly.
* For financial values, use compact and readable currency formatting.

### Accessibility and Responsiveness

The application must be usable on desktop and reasonably usable on tablet/mobile.

Requirements:

* Use semantic HTML where practical.
* Maintain readable font sizes.
* Maintain sufficient color contrast.
* Do not communicate important meaning using color alone.
* Buttons and inputs must be easy to click or tap.
* Layouts must not break on smaller screens.
* Charts should resize properly.

### Product Copy

Use concise, calm, educational copy.

Avoid harsh or overconfident wording such as:

* "You are rejected"
* "You must do this"
* "This is the best option"
* "Guaranteed savings"

Prefer:

* "Based on the data entered..."
* "This may reduce your total interest..."
* "This estimate depends on lender terms..."
* "Actual outcomes may vary..."

### Completion Requirement

A feature is not complete if it only works technically but looks rough, inconsistent, or confusing.

Before completing any frontend task, verify:

* The page is visually clean.
* The layout is responsive.
* Buttons, cards, tables, and charts are aligned.
* Loading, empty, success, and error states are handled.
* Financial numbers are formatted clearly.
* The user can understand what to do next.


---

## 8. Chart.js Rules

Use Chart.js for DebtWise visualizations.

Recommended chart mapping:

| Screen | Chart Type |
|---|---|
| Dashboard loan distribution | Doughnut chart |
| Interest-cost distribution | Doughnut chart |
| Monthly EMI comparison | Bar chart |
| Amortization schedule | Line chart |
| Prepayment before/after | Line chart |
| Health score factor breakdown | Bar chart |
| DTI / FOIR gauge | Doughnut-style gauge or custom Chart.js gauge pattern |
| Payoff timeline | Bar, timeline-like custom visualization, or simple structured Bootstrap layout |

Rules:

- Charts must be responsive.
- Charts must not misrepresent proportions or risk levels.
- Use clear labels and tooltips.
- Do not show a chart where a simple number or table is clearer.
- Use consistent colors for the same loan across dashboard charts when possible.
- Use green only for positive savings or safe states.
- Use red only for warnings, risks, or dangerous states.
- Do not rely on color alone; include labels and text explanations.

---

## 9. Database & Migration Rules

Use SQLAlchemy models and Flask-Migrate/Alembic migrations.

Rules:

- Every persistent model change must include a migration.
- Use foreign keys and relationships correctly.
- Every user-owned table must include `user_id` where appropriate.
- Every user-owned query must be scoped to the authenticated user.
- Do not expose another user's data.
- Use timestamps such as `created_at` and `updated_at` where useful.
- Use status fields rather than deleting historically meaningful records when appropriate.
- All real portfolio calculations must filter to `status = "active"` unless the feature explicitly includes hypothetical or considering loans.

### Core Loan Status Rules

The `loans.status` field should be supported from Release 1 to prevent late-stage refactors.

Allowed values:

```text
active
considering
closed
```

Rules:

- `active` loans affect dashboard totals, DTI / FOIR, health score, optimizers, stress tests, and portfolio charts.
- `considering` loans are hypothetical and must not affect real portfolio calculations unless a feature explicitly asks to include them.
- `closed` loans must be excluded from active portfolio calculations.
- Every service that aggregates a portfolio must explicitly filter by status rather than assuming all loans are active.

### Mixed-Currency Aggregation Rules

DebtWise stores each loan in its native currency, but mixed-currency aggregation must be handled carefully.

For Releases 1-3:

- Treat `INR` as the default supported portfolio currency.
- The `native_currency` field may exist for future extensibility.
- Do not allow mixed-currency portfolio aggregation in dashboard totals, health score, DTI / FOIR, optimizers, or advisor calculations.
- If the UI allows loan currency selection before display-layer conversion exists, restrict it to `INR` or clearly reject mixed-currency aggregation.

From Release 4 onward, when display-layer currency support exists:

- Never add amounts from different native currencies directly.
- Convert display values using the approved static display-rate configuration before showing aggregated display totals.
- Do not write converted values back to the database.
- EMI, amortization, prepayment, stress, comparison, and optimizer calculations must continue using each loan's native currency unless a feature explicitly defines a safe converted-display calculation.

### Future-Compatible Optimization Run Schema

When `optimization_runs` is introduced, it should be future-compatible with both lump-sum and recurring strategy optimization.

Recommended columns:

```text
id
user_id
optimization_type
lump_sum_amount nullable
monthly_surplus nullable
inputs_json
results_json
created_at
```

Allowed `optimization_type` values:

```text
lump_sum_optimizer
recurring_strategy
```


Minimum initial tables:

```text
users
user_profiles
loans
prepayment_simulations
score_snapshots
```

Future tables when needed:

```text
loan_comparisons
optimization_runs
stress_test_runs
exchange_rates
payment_history
```

## Database Migrations, Synthetic Data, and Demo Readiness

After every release implementation, DebtWise must be runnable and manually testable using synthetic demo data.

The project must maintain one cumulative MySQL seed file:

```text
database/master_seed.sql
```

This file is used to insert or refresh synthetic demo data after database migrations are applied.

---

## Migration Responsibility

All schema changes must be handled through Flask-Migrate / Alembic migrations.

Do not use `database/master_seed.sql` as the primary mechanism for creating or altering application tables.

The correct setup flow after each release is:

```bash
flask db upgrade
mysql -u <user> -p <database_name> < database/master_seed.sql
```

A helper script may be created to run these commands together, but schema changes must still come from migrations.

---

## Master Synthetic Data File

Maintain one cumulative seed file:

```text
database/master_seed.sql
```

This file must contain all synthetic data required to test the application after each completed release.

The file must be updated during every release whenever new features require new demo data.

The seed file must preserve earlier release demo data and add or update data for the current release.

Do not create separate unrelated seed files for every feature unless they are explicitly imported by `database/master_seed.sql`.

---

## Idempotency Requirement

`database/master_seed.sql` must be safe to run repeatedly.

Running the seed file multiple times must not create duplicate users, duplicate loans, duplicate profiles, duplicate score snapshots, or duplicate advisor runs.

Use one of these safe approaches:

* Fixed demo usernames and `INSERT ... ON DUPLICATE KEY UPDATE`
* Controlled delete-and-reinsert of demo data in dependency order
* Transactions to keep the seed operation consistent

Do not rely on random IDs, random usernames, or randomly generated data that changes every run.

---

## Demo User Requirement

The seed file must create stable demo users with known credentials.

Passwords must never be stored in plaintext in the database.

Use password hashes generated using the same password-hashing method as the application.

Document demo credentials in comments inside the seed file.

Example:

```sql
-- Demo login:
-- username: demo_user
-- password: Demo@12345
-- The password_hash below must be generated using the application's password hashing utility.
```

---

## Demo Data Coverage by Release

Each release must add enough synthetic data to test the new feature from the UI.

Expected coverage:

### Release 1: Foundation, Calculator, and Dashboard

Seed data should support:

* Demo user login
* Active loans
* Loan dashboard
* EMI calculations
* Amortization schedules
* Prepayment simulator

### Release 2: Financial Health Profile and Personalization

Seed data should support:

* User financial profile
* Monthly income
* Monthly non-loan obligations
* Emergency fund
* DTI / FOIR display
* DebtWise Health Score
* Score snapshots

### Release 3: Advisor Features and Decision Support

Seed data should support:

* Affordability Advisor
* Loan Comparison Engine
* Lump-sum optimizer
* Tax-benefit loan examples
* Emergency-fund guardrail examples
* Optimization run examples where appropriate

### Release 4: Risk, Resilience, Storytelling, and Display-Layer Currency

Seed data should support:

* Floating-rate loans
* Fixed-rate loans
* Penal charge examples
* Personal storytelling anchor
* Stress simulator
* Prepayment storytelling
* Display-layer currency conversion

### Release 5: Advanced Recurring Strategy Optimizer

Seed data should support:

* Multiple active loans
* High-rate debt
* Small-balance debt
* Tax-adjusted effective-rate examples
* Avalanche strategy
* Snowball strategy
* Hybrid strategy

### Release 6: Final Polish and Product Hardening

Seed data should support:

* Stable loan palette indexes
* Floating-rate exposure dashboard card
* Prepayment penalty examples
* Equal-payment loan comparison
* Active, considering, and closed loan statuses

---

## Current-Release Compatibility

At the end of each release, `database/master_seed.sql` must be compatible with the schema after running that release’s migrations.

Do not add seed data for future-release columns before the migration for those columns exists.

For example:

* Do not seed `personal_anchor_name` before the migration that adds it.
* Do not seed `palette_index` before the migration that adds it.
* Do not seed fields or statuses before the application supports them. Since `considering` status is supported from Release 1, it may be seeded from Release 1 onward if useful.

---

## Recommended Seed File Structure

Keep `database/master_seed.sql` organized with clear release sections.

Recommended structure:

```sql
-- =========================================================
-- DebtWise Master Synthetic Data Seed
-- Safe to run repeatedly after flask db upgrade
-- =========================================================

START TRANSACTION;

-- =========================================================
-- Demo Users
-- =========================================================

-- username: demo_user
-- password: Demo@12345
-- password_hash must be generated using app hashing utility

-- =========================================================
-- Release 1: Foundation, Calculator, and Dashboard
-- =========================================================

-- users
-- active loans
-- dashboard-ready data

-- =========================================================
-- Release 2: Financial Health Profile and Personalization
-- =========================================================

-- user_profiles
-- score_snapshots

-- =========================================================
-- Release 3: Advisor Features and Decision Support
-- =========================================================

-- tax-benefit loan flags
-- optimizer-ready portfolio
-- optimization_runs if needed

-- =========================================================
-- Release 4: Risk, Resilience, Storytelling, and Currency
-- =========================================================

-- floating loans
-- penal charge values
-- personal anchors
-- currency display scenarios

-- =========================================================
-- Release 5: Advanced Recurring Strategy Optimizer
-- =========================================================

-- portfolio suitable for Avalanche, Snowball, and Hybrid comparison

-- =========================================================
-- Release 6: Final Polish and Product Hardening
-- =========================================================

-- palette_index
-- considering loans
-- prepayment penalty examples
-- equal-payment comparison examples

COMMIT;
```

---

## Completion Requirement

A release is not complete until:

1. Backend tests pass.
2. Frontend build passes.
3. Database migrations run successfully.
4. `database/master_seed.sql` runs successfully.
5. The application can be manually tested using the seeded demo data.
6. The completion summary includes the demo login credentials.
7. The completion summary explains what release-specific scenarios are available in the seeded data.

The standard verification flow is:

```bash
flask db upgrade
mysql -u <user> -p <database_name> < database/master_seed.sql
pytest
npm run build
```

At completion, report:

* Whether `database/master_seed.sql` was created or updated
* Demo users created or updated
* Demo login credentials
* Release-specific seeded scenarios
* Commands run and results
* Any assumptions or limitations

---

## 10. Financial Precision Rules

DebtWise is a financial calculation application. Precision matters.

Rules:

- Never store money using FLOAT or DOUBLE in MySQL.
- Use DECIMAL for currency values.
- Use appropriate precision, for example `DECIMAL(14, 2)` for money and `DECIMAL(6, 3)` where interest-rate precision requires it.
- Avoid careless binary floating-point arithmetic for persisted financial values.
- Use Python `Decimal` where practical for money-sensitive backend calculations.
- Round only at display boundaries unless a formula has a defined financial rounding requirement.
- Do not round intermediate values unnecessarily.
- Store loans in their native currency.
- Currency conversion, if present, must be display-layer only.
- Do not recompute the financial reality of a loan in a display currency.

Example:

A ₹50,00,000 home loan remains an INR-denominated loan even when the UI displays an approximate USD equivalent.

---

## 11. Core Financial Formula Rules

Financial formulas must be centralized, documented, and tested.

Unless a feature explicitly works with hypothetical or historical loans, all portfolio-level financial calculations must use only loans where:

```text
status = "active"
```

Do not let `considering` or `closed` loans affect real dashboard totals, DTI / FOIR, health score, optimizers, stress tests, or portfolio charts.


### EMI Formula

Use the standard EMI formula:

```text
EMI = P × r × (1 + r)^n / ((1 + r)^n - 1)
```

Where:

- `P` = principal or outstanding principal
- `r` = monthly interest rate = annual interest rate / 12 / 100
- `n` = tenure in months

Handle edge cases:

- Zero interest rate
- Invalid negative principal
- Invalid negative rate
- Invalid tenure
- Very short tenure
- Very large principal

### Amortization

Amortization schedule must show:

- Month number
- Opening balance
- EMI
- Interest component
- Principal component
- Closing balance

Rules:

- Final month should close the loan cleanly.
- Avoid negative closing balance due to rounding.
- Total principal paid should equal original principal or current outstanding.

### Prepayment

Prepayment simulation must clearly support:

- Reduce tenure mode
- Reduce EMI mode, when implemented
- Interest saved
- Tenure reduced
- Original payoff date
- New payoff date

Default recommendation should generally prefer reducing tenure because it usually saves more interest, but explain the reasoning.

### DTI / FOIR

```text
DTI or FOIR = total monthly EMIs / gross monthly income
```

Suggested zones:

- Green: below 35%
- Amber: 35% to 50%
- Red: above 50%

Always explain that DTI/FOIR is based on user-entered data.

### DebtWise Health Score

The DebtWise Health Score must be transparent.

Never create a hidden-weight black-box score.

Suggested factor structure:

| Factor | Weight |
|---|---:|
| DTI / FOIR | 35 |
| Weighted average interest rate | 25 |
| High-cost debt share | 20 |
| Debt-to-annual-income | 12 |
| Loan count / mix | 8 |

Rules:

- Show factor-level points.
- Show maximum possible points.
- Show one action-oriented explanation per weak factor.
- Never call this a CIBIL score.
- Never call this a credit score.

### Affordability Advisor

Affordability should compare:

- Current EMI burden
- New proposed EMI
- New total EMI burden
- Income
- New DTI/FOIR
- Disposable income after EMI and known obligations

Rules:

- Never show only a bare "No".
- Show paths to improve affordability, such as higher down payment, longer tenure with cost warning, smaller principal, or waiting until another loan closes.

### Loan Comparison

When implemented, compare:

- EMI
- Total interest
- Total repayment
- Processing fees
- Effective APR
- 5-year exit cost
- Rate type
- Prepayment terms

### Debt Optimization

When implemented, optimization must be explainable.

For first version, prefer a simple lump-sum allocator.

Rules:

- Consider interest rate.
- Consider effective rate where tax treatment is captured.
- Consider prepayment penalties.
- Consider emergency fund and liquidity guardrails.
- Do not recommend deploying all available cash.
- Always show the math behind the recommendation.

---

## 12. Authentication & Security Rules

Authentication must be secure even for a demo project.

Rules:

- Store password hashes only.
- Never store plaintext passwords.
- Use secure HttpOnly cookies for JWT storage.
- Do not store access tokens in LocalStorage or SessionStorage.
- Use CSRF protection or appropriate same-site cookie strategy for cookie-based JWT flows.
- Use CORS carefully and allow credentials only for approved frontend origins.
- Validate all API inputs.
- Use parameterized database access through SQLAlchemy.
- Do not leak internal stack traces in API responses.
- Use environment variables for secrets.
- Do not commit `.env` files.
- Protect all user-specific routes.
- Scope every loan/profile/dashboard query by authenticated `user_id`.

Recommended auth endpoints:

```text
POST /api/auth/register
POST /api/auth/login
POST /api/auth/logout
GET  /api/auth/me
```

---

## 13. API Design Rules

Return predictable JSON.

Recommended success response pattern:

```json
{
  "success": true,
  "data": {},
  "message": "Optional human-readable message"
}
```

Recommended error response pattern:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable error message",
    "details": {}
  }
}
```

Rules:

- Use 200 for successful reads/updates.
- Use 201 for successful creates.
- Use 204 for successful deletes where no response body is needed.
- Use 400 for validation errors.
- Use 401 for unauthenticated requests.
- Use 403 for unauthorized access.
- Use 404 for missing resources.
- Use 500 only for unexpected server errors.

---

## 14. Testing Rules

Pytest is mandatory for backend financial logic.

Test the service layer, not only API routes.

Required tests for financial modules:

- EMI calculation
- Zero-interest EMI calculation
- Invalid inputs
- Amortization schedule totals
- Final balance handling
- Prepayment reduce-tenure calculation
- DTI / FOIR calculation
- DebtWise Health Score factor breakdown
- Affordability Advisor calculations
- Loan comparison calculations when implemented
- Stress simulator calculations when implemented
- Optimization logic when implemented

Rules:

- Use deterministic test data.
- Avoid tests that depend on external APIs.
- Do not skip financial tests to make the build pass.
- Add regression tests when fixing calculation bugs.

---

## 15. UI/UX Rules

DebtWise should feel trustworthy, calm, and educational.

Rules:

- Prioritize clarity over decorative complexity.
- Show summary cards before detailed tables.
- Use charts only when they improve comprehension.
- Show the calculation explanation near the result.
- Use expandable "Why?" or "How is this calculated?" sections for financial recommendations.
- Use plain language for financial concepts.
- Avoid fear-based messaging.
- For warnings, explain what the user can do next.
- Keep forms short and grouped logically.
- Always show validation feedback near the field.
- For dashboards, provide useful empty states for new users.

Recommended dashboard cards:

- Total outstanding
- Total monthly EMI
- Weighted average interest rate
- Total interest remaining
- DTI / FOIR
- DebtWise Health Score
- Next loan payoff

---

## 16. Financial Safety & Product Language

DebtWise must provide educational decision-support, not legally binding financial advice.

Avoid wording such as:

- "You must prepay this loan."
- "This is the best financial decision."
- "Your credit score is..."
- "You will definitely save..."

Prefer wording such as:

- "Based on the data entered..."
- "This option may reduce total interest..."
- "Estimated savings..."
- "Indicative result; actual lender terms may vary."
- "Many borrowers compare this option against liquidity needs and lender charges."

Mandatory disclaimer concepts:

- DebtWise Health Score is not CIBIL score.
- Stress simulator credit impact is indicative only.
- Lender charges and policies vary.
- Tax treatment depends on the user's situation and current law.

---

## 17. Currency Rules

DebtWise may support currency display, but currency conversion must be honest.

Rules:

- Store every loan in its native currency.
- EMI and amortization must be calculated in native currency.
- Display conversion should happen only at rendering level.
- Converted values must be marked approximately, for example `≈`.
- Show conversion rate and date when using exchange rates.
- Do not write converted values back into loan records.
- Do not round-trip converted values.
- Never directly add amounts from different native currencies.
- Never calculate portfolio totals using a naive SQL `SUM()` across mixed currencies.
- Dashboard totals, DTI / FOIR, health score, optimizer, comparison, and stress calculations must either use a single native currency or explicitly convert for display with clearly documented assumptions.

### Release-Specific Currency Scope

For Releases 1-3:

- Use `INR` as the default and only supported portfolio-tracking currency.
- Keep `native_currency` available for future extensibility if already present in the schema.
- Do not allow users to create mixed-currency portfolios if the backend aggregation logic cannot safely handle them.
- If a non-INR loan is entered before Release 4, reject it clearly or mark the feature as not yet supported.

From Release 4 onward:

- Use static display rates for display-layer conversion only.
- Loans remain denominated in their native currency.
- Core financial calculations must continue in native currency.
- Aggregated display totals may be shown only after safe conversion, and should be labeled as approximate.

---
## 18. Logging, Errors & Observability

For local/student development, keep this simple but clean.

Rules:

- Log server-side unexpected errors.
- Do not log passwords, JWTs, or sensitive user financial data unnecessarily.
- Use clear error messages for validation failures.
- Use generic messages for unexpected server failures.
- Keep debug mode off in production.

---

## 19. Project Commands

Use these commands unless the project structure later defines different scripts.

### Frontend

```bash
cd frontend
npm install
npm run dev
npm run build
```

### Backend

```bash
cd backend
python -m venv .venv
pip install -r requirements.txt
flask db upgrade
pytest
```

### Before Completing a Task

- Run backend tests when backend logic changes.
- Run frontend build when frontend code changes.
- Run migrations when database schema changes.
- Report any command that cannot be run and why.

---

## 20. Environment Variable Rules

Use environment variables for configuration.

Recommended variables:

```text
FLASK_ENV
SECRET_KEY
JWT_SECRET_KEY
DATABASE_URL
MYSQL_HOST
MYSQL_PORT
MYSQL_USER
MYSQL_PASSWORD
MYSQL_DATABASE
FRONTEND_URL
CORS_ALLOWED_ORIGINS
COOKIE_SECURE
COOKIE_SAMESITE
```

Rules:

- Never hardcode secrets.
- Never commit `.env` files.
- Provide `.env.example` with safe placeholder values.
- Use production-safe cookie settings in deployment.

---

## 21. Definition of Done

A task is complete only when all applicable items are satisfied:

- The requested feature works end-to-end.
- React UI is connected to Flask API where needed.
- MySQL persistence is implemented where needed.
- Financial calculations are placed in backend service functions.
- No financial formula is hidden inside route handlers.
- Required migrations are created.
- User-owned data is scoped by authenticated user.
- Backend validation exists for incoming data.
- Pytest coverage is added for financial logic.
- React screens handle loading, empty, success, and error states.
- Frontend screens are not complete if they are only functional; they must be visually clean, responsive, user-friendly, and consistent with the DebtWise UI/UX Design System.
- JWT is not stored in LocalStorage or SessionStorage.
- Money uses DECIMAL in MySQL.
- Portfolio-level calculations explicitly filter to `status = "active"` unless the feature intentionally includes hypothetical or historical loans.
- Mixed-currency portfolio totals are not aggregated unless a safe display-conversion path exists and is clearly labeled.
- `database/master_seed.sql` is created or updated when release features need demo data.
- The UI shows the math or explanation behind recommendations.
- The feature does not overclaim financial certainty.
- The project runs without obvious console or server errors.
- The final response summarizes implementation, tests, and assumptions.

---

## 22. Preferred Response Format After Implementation

After completing an epic or task, respond with:

```text
Summary
- What was implemented

Files changed
- Important files created/modified

How to test
- Commands to run
- Manual test flow

Notes / assumptions
- Any assumptions made
- Any future work intentionally not implemented
```

Keep the summary concise but specific.

---

## 23. AI Developer Behavior

You are expected to act as a senior developer.

Do:

- Make clean architectural choices.
- Keep code readable.
- Add tests for financial logic.
- Respect the approved stack.
- Ask only meaningful clarifying questions.
- Warn when a request conflicts with security, scope, or financial correctness.
- Prefer maintainability over cleverness.

Do not:

- Add unapproved libraries casually.
- Build future roadmap features without being asked.
- Store money in FLOAT/DOUBLE.
- Store JWTs in LocalStorage.
- Put formulas directly in route handlers.
- Create a hidden black-box score.
- Present DebtWise estimates as guaranteed advice.
- Ignore failing tests.
