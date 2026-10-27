# Technical challenges

This document describes engineering problems visible in the project design and the approaches used to address them. It avoids customer data and does not claim unmeasured performance or business outcomes.

## 1. One operating day, several staff workflows

**Problem.** Organizers, reception, shift leads, kitchen, animators, HR, and service staff share information but need different actions during a shift. A department screen is not the same thing as the identity or permissions assigned to a device.

**Approach.** The application models staff role and department view separately. Role-scoped access gates sensitive workflows, while department views present the relevant tasks over shared operational records. Reservation changes retain a history entry with the acting role, and deleted reservations have a separate history record.

**Outcome.** Staff can switch between task-focused screens without silently changing the device’s role context, and important reservation changes remain traceable. No user-level audit trail is claimed: the history identifies the role, not a named individual.

## 2. External systems have different data shapes and availability

**Problem.** Booking and menu information comes from a POS integration, staff schedules come from a spreadsheet, and reporting needs stable monthly snapshots. Treating every external read as the live source of truth would couple routine shift work to those services.

**Approach.** Integration code is separated by service. The application keeps selected POS catalog data in a local mirror, maps local operational concepts to external product identifiers, and stores reporting snapshots for monthly views. Schedule data is refreshed from its source rather than treated as application-owned staff data.

**Outcome.** The application has explicit boundaries for external data and can present captured operational information without embedding provider-specific identifiers throughout every workflow. The case study does not claim a particular uptime or synchronization latency.

## 3. Reservation records evolve with operational needs

**Problem.** A booking can include multiple add-ons, cakes, menu lines, staff assignments, status changes, and later corrections. A single flat record becomes difficult to evolve and audit as these workflows grow.

**Approach.** The relational model keeps the reservation as the central record and separates repeatable or historical data into related collections, such as reservation history, menu lines, inventory requests, and sales snapshots. Schema changes are versioned as migrations.

**Outcome.** The model supports richer operational detail while retaining the reservation as the shared reference for downstream tasks. Exact table definitions and client-specific fields are not published.

## 4. Production schema changes need an explicit safety boundary

**Problem.** A database migration that runs automatically during application startup can turn a deployment into an unreviewed production data change.

**Approach.** Production startup skips schema initialization. Migrations are invoked explicitly, support a dry-run, and require an additional production opt-in before applying changes. Staging uses an isolated database rather than the production database.

**Outcome.** Deploying the application and changing the production schema are separate, reviewable operations. This reduces accidental schema changes during routine restarts; it does not replace backups or a tested recovery plan.

## Further evidence

The original implementation includes focused tests around reservations, access, integrations, reports, and migrations. This public case study intentionally excludes those source files and test code; no test count or coverage percentage is asserted here.
