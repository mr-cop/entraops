# EntraOps improvement backlog

This backlog captures the engineering and operational improvements identified during the repository assessment. Items are grouped by priority and include completion criteria so they can be converted into focused issues without losing intent.

## P0 — Safety and production validation

- [ ] **Create a tenant-backed integration test suite.**
  - Exercise Microsoft Graph pagination, retry, throttling, and token refresh behavior.
  - Compare sampled Entra ID, Azure RBAC, PIM active, and PIM eligible assignments with their authoritative APIs.
  - Cover nested groups, deleted principals, unresolved objects, and cross-tenant identities.
  - Validate Defender, Security Exposure Management, and Tenant Configuration Management (UTCM) response schemas.
  - Run destructive tests only in a disposable tenant and clearly separate them from read-only tests.
  - **Done when:** a documented opt-in workflow runs repeatably against a test tenant, publishes sanitized results, and fails on collection or classification parity regressions.

- [ ] **Add end-to-end tests for every mutation path.**
  - Cover Conditional Access groups, Administrative Units, Restricted Management Administrative Units, privileged Entitlement Management catalogs, Sentinel WatchLists, and Log Analytics ingestion.
  - Test idempotency, partial failure, retry, dry-run/preview behavior, and rollback guidance.
  - Verify removal safety thresholds and protection against empty or incomplete source data.
  - **Done when:** every write operation has positive, negative, idempotency, and safety-threshold coverage in a disposable tenant.

- [ ] **Document and enforce separate least-privilege identities.**
  - Publish permission matrices for collection, reporting, Tenant Governance, integrations, and mutation workflows.
  - Recommend separate workload identities for read-only collection and write-back automation.
  - Add validation that reports unnecessary or missing permissions before execution.
  - **Done when:** operators can provision each automation identity without granting the union of all EntraOps permissions.

- [ ] **Strengthen generated-data protection.**
  - Classify which exports contain privileged topology, tenant configuration, identity details, or attack-path data.
  - Add pre-publication checks for accidental tenant data in public repositories and GitHub releases.
  - Document private-repository, artifact-retention, encryption, access-review, and secure-deletion expectations.
  - Provide a sanitized sample-data path for demos and report testing.
  - **Done when:** collection and publication workflows prevent unsafe defaults and documentation provides an explicit data-handling checklist.

## P1 — Architecture and maintainability

- [ ] **Replace implicit global state with an explicit EntraOps context.**
  - Introduce a context object containing tenant identity, authentication mode, paths, configuration, caches, and feature capabilities.
  - Pass the context through internal commands while retaining compatibility wrappers for existing public commands.
  - Support two independent tenant contexts in one PowerShell process without collisions.
  - Remove command-order dependencies where practical.
  - **Done when:** core collectors can be tested with an isolated context and concurrent tenant sessions do not share mutable state.

- [ ] **Define stable and advanced command surfaces.**
  - Categorize exported functions as operator-facing, advanced, compatibility, or internal.
  - Reduce exports where doing so is backward-compatible; otherwise publish a deprecation schedule.
  - Add discoverable command groups and task-oriented examples.
  - Establish semantic-versioning rules for functions, parameters, configuration, and generated JSON schemas.
  - **Done when:** the supported public API is documented, versioned, and guarded by contract tests.

- [ ] **Introduce versioned schemas for configuration and generated artifacts.**
  - Add a schema version to `EntraOpsConfig.json`, Privileged EAM exports, snapshot manifests, and report datasets.
  - Validate inputs before authentication or API activity begins.
  - Provide migrations for supported historical versions and actionable errors for unsupported versions.
  - Preserve unknown settings only where forward compatibility is safe.
  - **Done when:** each persisted format has a machine-readable schema, migration tests, and compatibility policy.

- [ ] **Centralize API contracts and feature detection.**
  - Consolidate endpoints, API versions, required permissions, paging conventions, and response adapters.
  - Detect unavailable preview APIs or tenant capabilities and return explicit `unsupported`, `partial`, or `failed` states.
  - Add contract fixtures for Microsoft Graph, ARM, Resource Graph, Defender, Exposure Management, and UTCM.
  - **Done when:** API-version changes can be addressed in one adapter layer and schema drift produces a clear diagnostic.

- [ ] **Standardize observability.**
  - Emit structured operation summaries for API calls, cache hits, retries, throttles, unresolved objects, filtered assignments, and mutations.
  - Add correlation IDs without logging tokens or sensitive response bodies.
  - Support machine-readable output suitable for CI annotations and Log Analytics.
  - **Done when:** operators can distinguish successful, partial, skipped, and failed runs without parsing console prose.

## P1 — Classification quality

- [ ] **Add classification confidence and provenance.**
  - Record the template, overwrite, parameter, object, scope, or inferred relationship responsible for each tier decision.
  - Distinguish authoritative, tenant-overridden, inferred, fallback, contradictory, and unresolved classifications.
  - Surface confidence and provenance consistently in JSON exports and reports.
  - **Done when:** every classified assignment can explain why it received its tier.

- [ ] **Add classification drift and contradiction gates.**
  - Detect changes caused by template updates separately from tenant-state changes.
  - Require review for newly unclassified privileged assignments, contradictory tier pairs, and Control Plane scope reductions.
  - Produce a concise pull-request summary of classification-impact changes.
  - **Done when:** automation can block unsafe classification regressions before generated state is committed or applied.

- [ ] **Expand classification regression fixtures.**
  - Cover custom roles, ABAC conditions, management-group inheritance, delegated Identity Governance access, nested groups, application permissions, and first-party resource applications.
  - Include expected provenance and confidence in fixture assertions.
  - **Done when:** each supported RBAC system has representative positive, negative, edge-case, and unresolved fixtures.

## P1 — Automation and supply-chain resilience

- [ ] **Add reusable pipeline entry points beyond GitHub Actions.**
  - Publish documented scripts or reusable commands for collection, validation, reporting, and mutation that do not depend on GitHub environment variables.
  - Provide examples for Azure DevOps and a generic OIDC-capable runner.
  - Keep the GitHub workflows as thin adapters over the same entry points.
  - **Done when:** another CI system can run the supported lifecycle without reimplementing workflow logic.

- [ ] **Harden update and dependency provenance.**
  - Verify downloaded modules, templates, and update candidates against immutable versions and integrity metadata.
  - Generate a software bill of materials for PowerShell modules, NPM packages, report libraries, and GitHub Actions.
  - Add automated dependency and license review with an exception process.
  - **Done when:** a release records the exact provenance and integrity of every shipped or downloaded dependency.

- [ ] **Add API and dependency compatibility monitoring.**
  - Schedule smoke tests against supported PowerShell, Az, Microsoft Graph, Node.js, and Playwright versions.
  - Detect Microsoft API deprecations and breaking schema changes before routine tenant runs fail.
  - Publish a supported-version matrix.
  - **Done when:** dependency or API drift opens an actionable issue with the affected commands and fixtures.

## P2 — Operator experience

- [ ] **Add a preflight/readiness command.**
  - Validate PowerShell version, required modules, Git availability, configuration schema, paths, authentication contexts, permissions, API capabilities, and destination access.
  - Return remediation instructions and structured status output.
  - **Done when:** operators can identify setup problems before a long collection or reporting run begins.

- [ ] **Provide a first-class dry-run plan for mutations.**
  - Show additions, removals, unchanged objects, unresolved dependencies, thresholds, and required permissions.
  - Make plan artifacts reviewable in pull requests and reusable only when their source data still matches.
  - **Done when:** no mutation workflow needs to be enabled before its exact intended changes can be reviewed.

- [ ] **Improve configuration usability.**
  - Add presets for read-only inventory, reporting, Sentinel integration, Tenant Governance, and controlled remediation.
  - Display the permissions and data sensitivity introduced by each option.
  - Validate incompatible combinations in both the browser wizard and PowerShell.
  - Keep beginner and expert configuration outputs contract-equivalent.
  - **Done when:** a new operator can create a minimal valid configuration without understanding every advanced setting.

- [ ] **Publish an operational runbook.**
  - Cover initial read-only rollout, classification review, integration enablement, controlled remediation, incident response, rollback, backup, artifact retention, and disaster recovery.
  - Include troubleshooting for throttling, partial snapshots, stale data, API permission failures, and schema changes.
  - **Done when:** production operation does not depend on undocumented maintainer knowledge.

- [ ] **Clarify support and release expectations.**
  - Publish release cadence, compatibility windows, deprecation periods, and security-fix handling for the community project.
  - Distinguish experimental or preview-backed features from stable features.
  - **Done when:** adopters can evaluate upgrade and operational risk from repository documentation alone.

## P2 — Reporting and accessibility

- [ ] **Add report data freshness and completeness indicators.**
  - Show collection time, source systems, partial failures, unresolved objects, snapshot age, schema version, and configuration fingerprint.
  - Keep health indicators visible in exported or printed views.
  - **Done when:** report readers cannot mistake stale or partial data for a complete current tenant view.

- [ ] **Complete accessibility and large-dataset validation.**
  - Test keyboard navigation, focus order, screen-reader labels, color contrast, reduced motion, zoom, and high-contrast mode.
  - Benchmark reports with production-scale datasets and define supported size limits.
  - Add graceful degradation or aggregation for data beyond those limits.
  - **Done when:** automated accessibility checks and documented performance baselines run in CI.

- [ ] **Secure report sharing workflows.**
  - Add an export manifest containing classification, sensitivity, tenant, generation time, and expiry metadata.
  - Warn before publishing reports from a public repository or with unrestricted artifact access.
  - Document redaction and sanitized sharing options.
  - **Done when:** each distributed report package carries enough metadata for recipients to handle it appropriately.

## P2 — Test and development environment

- [ ] **Provide a reproducible local test bootstrap.**
  - Install or verify PowerShell 7.4+, required PowerShell modules, Node.js, and Playwright Chromium.
  - Support containers and environments where the default Playwright CDN is unavailable by documenting mirrors or prebuilt images.
  - Pin toolchain versions consistently with CI.
  - **Done when:** one documented command prepares a clean supported environment and runs all offline tests.

- [ ] **Publish coverage and test-layer reporting.**
  - Separate unit, contract, generated-artifact, browser, integration, and mutation results.
  - Track PowerShell code coverage and critical-path coverage without optimizing solely for a percentage target.
  - **Done when:** pull requests show which functional layers were exercised and where meaningful coverage gaps remain.

## Suggested delivery sequence

1. Tenant-backed read-only integration tests and generated-data protection.
2. Mutation end-to-end tests and separate least-privilege identities.
3. Versioned schemas, API adapters, and structured observability.
4. Explicit context refactor and stable public API definition.
5. Classification provenance and drift gates.
6. Preflight, dry-run plans, configuration presets, and operational runbooks.
7. CI portability, supply-chain provenance, accessibility, and scale testing.

## Backlog maintenance

- Convert an item into one or more tracked issues before implementation.
- Record the responsible subsystem, risk level, compatibility impact, and test strategy on each issue.
- Do not mark an item complete until its **Done when** condition is satisfied.
- Preserve backward compatibility by default; document and version unavoidable breaking changes.
