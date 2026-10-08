Business Brief: Bank Statement Finance Tracker
1. Problem
Many people don't know where their money goes. Bank statements are long lists of transactions with cryptic merchant names, so budgeting by hand takes time and is easy to abandon. Existing apps are either closed, paid, or require sharing bank login details.

2. Solution
A web API that takes a bank statement file (CSV), cleans and categorizes each transaction using rules the user defines, and shows spending reports and budget alerts. The user keeps control of their data: files are uploaded manually, with no bank login.

3. Target Users
Primary: individuals who want to track monthly spending without sharing bank credentials.
Secondary: freelancers and small-household budget keepers who need category totals for tax or planning.
Portfolio audience: recruiters and engineering managers evaluating junior .NET backend candidates.
4. Goals
Goal	Measure
Save time on categorization	Transactions auto-categorized after rules are set (target: 80% or more on a sample statement)
Give clear spending insight	Monthly report generated in under 500 ms for 5,000 transactions
Prevent data errors	Duplicate transactions detected on re-import (target: 100% on the test set)
Demonstrate engineering quality	80%+ unit test coverage, CI passing on every push, deployed live demo
5. Scope
In scope (MVP)
User registration and login (JWT)
Upload a CSV statement with a defined column mapping
Parse, validate, and reject malformed rows with clear error messages
Duplicate detection (hash of date, amount, description, and account)
Category management and rule-based categorization (merchant contains, amount range)
Manual override of a transaction's category
Monthly summary and category breakdown endpoints
Budget limits per category with an over-budget flag
Out of scope (later)
Direct bank connection (Open Banking APIs)
Multi-currency conversion
Mobile app
Machine-learning categorization
Shared households and multi-user budgets
6. Functional Requirements
Import: accept CSV files up to 5 MB. Return a summary: imported, duplicate, and rejected row counts.
Categorize: apply rules in priority order. The first match wins. Manual overrides are never changed by re-running rules.
Reports: return totals per month and per category for a date range.
Budgets: store a monthly limit per category. Flag categories that exceed their limit.
Security: users only access their own data. Passwords are hashed. Uploaded files are not stored after parsing.
7. Non-Functional Requirements
Performance: import 10,000 rows in under 10 seconds. Reports under 500 ms.
Reliability: an import is all-or-nothing, in one database transaction.
Security: OWASP top 10 review, secrets from environment variables, no sensitive data in logs (mask account numbers).
Maintainability: Clean Architecture layers, CQRS with MediatR, FluentValidation.
Observability: structured logs (Serilog), health check endpoint, basic request metrics.
8. Architecture Overview
Api: controllers, auth, middleware
Application: commands and queries, validation
Domain: entities and business rules, no framework dependencies
Infrastructure: EF Core, CSV parsing, time provider
Tests: unit tests for domain and handlers, integration tests with Testcontainers
9. Data Model (main entities)
User: id, email, password hash, created at
Account: id, user id, name, currency
Transaction: id, account id, date, amount, description, merchant, category id, import hash, is manual override
Category: id, user id, name
CategoryRule: id, user id, category id, priority, match type, match value
Budget: id, user id, category id, year, month, limit amount
10. Milestones
Week	Deliverable
1	Solution structure, Docker Compose with PostgreSQL, CI pipeline, health check
2	Auth, accounts, CSV import with validation and duplicate detection
3	Categories, rules engine, manual overrides
4	Reports and budgets
5	Testing (unit, integration, Stryker run), security review
6	Deploy to a free host, README with diagrams and screenshots, demo data
11. Risks and Mitigations
Risk	Mitigation
Bank CSV formats differ	Support a column mapping per account; ship sample files for 2 or 3 formats
Scope creep	Freeze the MVP at the week-5 scope; put extras in a backlog
Sensitive financial data	Use only fake or public sample data; no real statements in the repo
Time overrun	Each milestone must be demoable; cut the frontend first if needed
12. Success Criteria for the Portfolio
Live demo link in the CV and README
Green CI badge and test coverage report
README with architecture diagram, setup steps (docker compose up), and sample requests
Ability to explain three trade-offs in an interview (for example, the rules engine design, duplicate detection strategy, and why PostgreSQL)
13. Stakeholder Summary (one-liner)
