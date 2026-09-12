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

## Strategic roadmap — Active Directory Domain Services

Adopt Active Directory Domain Services (AD DS) support incrementally. Each phase must be usable on
its own, preserve the distinction between observed assignments and inferred effective control, and
meet its exit criteria before write-back capabilities are enabled. AD collection must support a
domain-connected worker without making domain connectivity a requirement for offline classification,
report generation, or the existing cloud collectors.

### Phase 1 — Read-only privileged identity MVP

- [ ] **Define the versioned Active Directory data contract and source boundaries.**
  - Add `ActiveDirectory` as an RBAC system and define stable identifiers for forests, domains, and
    objects using forest/domain identity plus `objectGUID` and SID rather than mutable distinguished
    names alone.
  - Extend the common EAM object and role-assignment contracts with optional AD properties, including
    forest, domain, distinguished name, SID, source adapter, collection time, and correlation
    provenance, without overloading Entra tenant or Administrative Unit fields.
  - Represent direct and nested group membership paths explicitly and label them as observed
    membership, not complete effective privilege.
  - Version the normalized snapshot and generated artifact schemas and document compatibility rules.
  - **Done when:** sanitized fixtures validate against machine-readable schemas and existing RBAC
    exports and reports remain backward-compatible.

- [ ] **Implement a read-only, pluggable AD collector.**
  - Define a provider-neutral collector interface and implement a cross-platform LDAP/LDAPS adapter;
    optionally provide a Windows `ActiveDirectory` PowerShell/AD Web Services adapter with identical
    normalized output.
  - Support paging, referrals, Global Catalog discovery, configurable domain controllers and search
    bases, LDAPS certificate validation, Kerberos or explicit credentials, retry, timeout, partial
    result, and cancellation behavior.
  - Inventory forests, domains, trusts, users, groups, computers, managed service accounts, group
    managed service accounts, foreign security principals, and deleted or unresolved references
    needed to explain privileged membership.
  - Record source server, invocation status, completeness, and per-domain collection errors without
    logging credentials, tokens, or sensitive raw attribute values.
  - **Done when:** a least-privileged account can repeatedly collect a single-forest test environment
    through either supported adapter and produce deterministic, equivalent normalized snapshots.

- [ ] **Discover privileged groups and recursive membership.**
  - Cover well-known forest-, domain-, and built-in privileged groups by SID/RID, including Enterprise
    Admins, Domain Admins, Schema Admins, Administrators, Account Operators, Backup Operators, Server
    Operators, Print Operators, Group Policy Creator Owners, Key Admins, Enterprise Key Admins, DNS
    administration groups, and Protected Users where present.
  - Support tenant-defined privileged groups and critical users, service accounts, computers, OUs,
    and containers through AD classification parameters and exclusions.
  - Expand direct and transitive membership with cycle detection, preserve every relevant membership
    path, resolve cross-domain and foreign-security-principal membership, and identify unresolved or
    stale SIDs.
  - Collect security-relevant account context including enabled state, account category, `adminCount`,
    AdminSDHolder protection, primary group, SID history presence, delegation flags, password-policy
    flags, and supported authentication protections without collecting credential material.
  - **Done when:** the collector identifies and explains every direct and nested privileged-group
    member in representative single-forest fixtures, including cycles and unresolved identities.

- [ ] **Correlate hybrid AD and Entra identities.**
  - Correlate users, groups, devices, and applicable service identities using authoritative immutable
    identifiers such as on-premises SID, immutable ID, device ID, and synchronization metadata.
  - Treat UPN, mail, display name, and `sAMAccountName` matches only as configurable low-confidence
    hints; never silently merge objects from heuristic matches.
  - Record match method, confidence, source identifiers, contradictions, and one-to-many or unresolved
    states so reports can explain every link.
  - **Done when:** hybrid test fixtures link synchronized identities deterministically, ambiguous
    matches remain separate, and cross-environment paths retain both AD and Entra identifiers.

- [ ] **Add MVP classification, export, and reporting.**
  - Add `Classification_ActiveDirectory.json` and `.Param.json` templates with fixed classification
    for well-known privileged groups and tenant-overridable classification for custom assets.
  - Add `Get-EntraOpsPrivilegedEAMActiveDirectory`, classification export/maintenance commands, and
    `ActiveDirectory` dispatch to in-memory and JSON EAM orchestration.
  - Write `PrivilegedEAM/ActiveDirectory/ActiveDirectory.json` plus deterministic per-object artifacts
    using the shared EAM schema, classification provenance, and completeness metadata.
  - Add forest/domain, object type, direct/nested membership, protection status, and hybrid identity
    filters to the EAM Dashboard, Tier Breach Analyzer, Privilege History, and generic reporting data.
  - Extend Sentinel custom-table and WatchList schemas/parsers for AD identity and membership fields.
  - **Done when:** an operator can collect, classify, export, compare, and report privileged AD group
    membership end to end without enabling any AD mutation permission.

- [ ] **Provide secure worker deployment and MVP validation.**
  - Support self-hosted Windows runners and a generic domain-connected worker entry point; document
    Azure Automation Hybrid Worker, Arc-enabled server, scheduled task, and LDAP-capable Linux worker
    patterns without coupling the collector to one automation platform.
  - Publish exact least-privilege directory reads, network requirements, LDAPS/Kerberos setup, gMSA or
    service-account guidance, secret handling, artifact sensitivity, retention, and sanitization.
  - Add unit and golden-file tests plus an opt-in disposable-forest integration workflow covering
    nested groups, foreign security principals, AdminSDHolder, trusts, and synchronized identities.
  - Keep Linux and macOS offline classification/report tests operational even when live AD collection
    is available only on a domain-connected runner.
  - **Done when:** the MVP has repeatable offline CI and an opt-in test-forest run that publishes only
    sanitized results and fails on collection, classification, or cross-platform regressions.

### Phase 2 — Directory ACL and effective-control analysis

- [ ] **Collect and normalize AD security descriptors.**
  - Read owner, DACL, inheritance state, protected ACL state, object-specific ACEs, inherited-object
    types, standard access masks, extended rights, validated writes, property sets, and schema GUIDs.
  - Resolve trustees through nested groups, SID history, foreign security principals, and cross-domain
    references while retaining unresolved trustees and raw evidence references.
  - Build an offline schema/right GUID catalog with version and forest-schema provenance.
  - **Done when:** representative security descriptors round-trip into deterministic normalized ACEs
    with correct trustee, target class, inheritance, and right names.

- [ ] **Implement explainable effective-right evaluation.**
  - Evaluate allow and deny ACE ordering, inheritance, object/class restrictions, owner-derived rights,
    `WRITE_DAC`, `WRITE_OWNER`, `GENERIC_ALL`, `GENERIC_WRITE`, create/delete child, self/validated
    writes, property rights, and control-access rights.
  - Propagate control through nested membership and ownership with bounded cycle-safe graph traversal.
  - Distinguish observed ACEs, calculated effective rights, potential paths, blocked paths, incomplete
    paths, and assumptions; attach confidence and source evidence to every decision.
  - Provide reusable normalized actions such as group membership modification, password reset,
    key-credential write, SPN change, delegation change, ACL takeover, and object creation.
  - **Done when:** results match Windows effective-access expectations across positive, deny,
    inheritance, ownership, nested-group, and cross-domain fixtures, with a human-readable explanation.

- [ ] **Classify ACL-derived control dynamically.**
  - Classify a principal based on both the granted action and the target asset's EAM tier so control of
    a Control Plane object or container propagates Control Plane sensitivity.
  - Cover control of privileged groups, AdminSDHolder, domain roots, critical OUs/containers, domain
    controllers, synchronization servers/accounts, tier-management groups, and custom critical assets.
  - Support action, right GUID, object class, DN pattern, scope, inheritance, target classification,
    exclusions, and tenant overwrite rules.
  - Detect contradictory, newly unclassified, and unexpectedly downgraded AD rights in classification
    drift gates.
  - **Done when:** every material ACL-derived tier has target-aware provenance and unsafe classification
    changes can block automation before artifacts are committed.

- [ ] **Add effective-control paths to reports and integrations.**
  - Add ACL source, target, action, inheritance, confidence, and evidence views to dashboards and the
    Access Path Map while allowing membership-only and effective-control views to be separated.
  - Emit BloodHound OpenGraph-compatible AD nodes and control edges without duplicating nodes when a
    native BloodHound dataset is also present.
  - Add an optional BloodHound CE/Enterprise import adapter as a source of normalized AD edges, mark
    its provenance, and reconcile rather than silently override native collection.
  - Extend Sentinel schemas and queries for ACL changes, new privileged paths, and tier breaches.
  - **Done when:** operators can trace an ACL-derived tier or attack path from principal through each
    edge to the critical target and back to source evidence.

### Phase 3 — Authentication, delegation, policy, and credential-control paths

- [ ] **Analyze Kerberos and delegation exposure.**
  - Cover unconstrained delegation, constrained delegation, protocol transition, resource-based
    constrained delegation, sensitive/non-delegable accounts, SPNs, delegation target changes, and
    rights that can create or alter those configurations.
  - Model abuse prerequisites and distinguish configuration exposure from a currently traversable
    privilege path.
  - **Done when:** test-forest delegation scenarios produce accurate, explainable paths and tiers with
    false-positive controls for intentionally constrained configurations.

- [ ] **Analyze domain replication and credential-control rights.**
  - Detect combinations enabling directory replication/DCSync, including changes that can grant the
    required rights.
  - Model password reset/change, key credential, authentication policy/silo, gMSA password-read,
    LAPS-related, account-control, SID history, and other credential-relevant control without reading
    or exporting secret values.
  - Inventory KRBTGT and other critical accounts only to the extent needed for classification and
    hygiene findings.
  - **Done when:** credential-control findings identify the exact right combination and target while
    proving that no credential material is collected or written to artifacts.

- [ ] **Analyze Group Policy and logon-right paths.**
  - Collect GPO objects, links, inheritance, enforcement/block-inheritance, security filtering, WMI
    filter references, directory ACLs, and SYSVOL file permissions needed to identify who can alter a
    policy applied to critical computers or users.
  - Model control over GPO creation, linking, editing, ownership, and relevant restricted-group,
    scheduled-task, script, security-option, and user-right-assignment settings.
  - Correlate critical computer scope, including domain controllers, tier-management systems, PAWs,
    synchronization infrastructure, and configured high-value servers.
  - **Done when:** an operator can trace who can change a GPO or SYSVOL content and which classified
    principals or computers receive the affected policy.

- [ ] **Analyze trusts and multi-forest privilege paths.**
  - Inventory forest, external, shortcut, realm, and selective-authentication trust properties,
    direction, transitivity, SID filtering/quarantine, and trust-account control.
  - Resolve cross-domain and cross-forest group membership and ACL trustees where connectivity and
    credentials permit, while preserving partial-boundary status where they do not.
  - Model trust configuration control and only emit traversable cross-forest paths when all required
    conditions are evidenced.
  - **Done when:** multi-forest fixtures accurately distinguish inventory-only trusts, permitted paths,
    SID-filtered paths, selective-authentication constraints, and incomplete evidence.

- [ ] **Add identity hygiene and exposure findings.**
  - Detect stale or disabled privileged members, inappropriate permanent memberships, privileged
    service accounts, weak authentication protections, dormant privileged paths, risky delegation,
    tier mixing, privileged logon exposure, unresolved SIDs, and protected-ACL inconsistencies.
  - Make thresholds and exceptions configurable, record evidence and confidence, and separate
    normative recommendations from objectively exploitable control paths.
  - **Done when:** findings are deterministic, suppressible with documented justification, and linked
    to the affected identity, assignment/path, classification rule, and remediation guidance.

### Phase 4 — Extended AD security systems and comprehensive attack paths

- [ ] **Add optional Active Directory Certificate Services analysis.**
  - Discover enterprise certification authorities, enrollment services, certificate templates,
    issuance requirements, enrollment/auto-enrollment permissions, template and CA ACLs, NTAuth and
    relevant PKI containers, and web enrollment endpoints where configured.
  - Detect material certificate-based escalation conditions and control paths using versioned,
    explainable rules; clearly label reachability checks and data that require an optional worker.
  - Never request or export private keys or issued authentication certificates during assessment.
  - **Done when:** disposable AD CS fixtures cover vulnerable and hardened configurations, permissions,
    prerequisites, mitigations, and safe data handling.

- [ ] **Cover DNS, services, hosts, and directory-adjacent control planes.**
  - Analyze AD-integrated DNS administrative groups, zones, records, ACLs, and dangerous update/control
    paths that affect classified authentication or management endpoints.
  - Optionally ingest authoritative endpoint-management or local-administrator data for domain
    controllers, PAWs, synchronization servers, PKI hosts, and other configured critical systems.
  - Keep host/network collection modular and explicitly report when an attack path depends on data
    outside AD DS or was not assessed.
  - **Done when:** directory-adjacent paths have explicit source coverage and never imply host-level
    certainty from directory data alone.

- [ ] **Deliver a unified AD/Entra/Azure attack-path graph.**
  - Correlate on-premises identities and control edges with synchronized Entra identities, Entra
    roles, application permissions, managed identities, Azure RBAC, Intune, Defender, Identity
    Governance, PAWs, and critical resources.
  - Add bounded path search, shortest/material path prioritization, path confidence, stale-edge
    handling, suppression, differential snapshots, and path appearance/disappearance history.
  - Preserve source-specific semantics and avoid merging identities or edges solely by display names.
  - **Done when:** cross-environment test scenarios produce reproducible end-to-end paths with every
    identity correlation, privilege edge, assumption, and classification decision explainable.

- [ ] **Scale collection and analysis for enterprise forests.**
  - Add partitioned snapshots, incremental/delta collection where authoritative change tracking is
    available, bounded concurrency, referral controls, checkpoint/resume, cache invalidation, and
    deterministic aggregation.
  - Establish supported forest/domain/object/ACE/path scale limits and graceful degradation for
    reports, graph traversal, and exports.
  - Detect USN rollback or unsuitable delta state and fall back safely to a full collection rather
    than accepting an incomplete snapshot.
  - **Done when:** documented production-scale tests meet collection, memory, report, and path-search
    budgets without losing completeness indicators or deterministic results.

### Phase 5 — Governed remediation and continuous operations

- [ ] **Introduce AD change plans before mutation support.**
  - Generate immutable, reviewable plans for proposed membership, ACL, delegation, GPO, trust, and
    protection changes with before/after state, replication scope, prerequisites, dependency order,
    impact, rollback commands, expiry, and source snapshot fingerprint.
  - Require fresh precondition checks and invalidate plans when source objects, ACL versions,
    membership, or classification have changed.
  - Make all planning available to read-only operators and keep execution in a separately permissioned
    command surface and worker identity.
  - **Done when:** every supported mutation can be reviewed and independently verified without
    granting the planning process write access.

- [ ] **Add guarded remediation in narrowly approved lanes.**
  - Introduce `SupportsShouldProcess`, dry-run by default, explicit forest/domain/target allowlists,
    emergency exclusions, safety thresholds, maintenance windows, approval artifacts, and independent
    backups for each mutation command.
  - Begin with reversible privileged-group membership changes; add ACL, delegation, GPO, trust, or
    protection changes only after dedicated disposable-forest validation and rollback exercises.
  - Account for multi-master replication, AdminSDHolder reapplication, PDC/GC availability, protected
    objects, accidental lockout, and partial failure; never infer success from a single DC write.
  - **Done when:** each mutation lane has positive, negative, idempotency, concurrency, replication,
    partial-failure, stale-plan, safety-threshold, and rollback coverage and is disabled by default.

- [ ] **Add continuous monitoring and drift response.**
  - Support scheduled full and incremental snapshots, membership/ACL/policy/trust/delegation change
    history, new attack-path alerts, classification drift, stale data detection, and source-health
    monitoring.
  - Integrate structured results with pull requests, Sentinel/Log Analytics, and operator-selected
    notification systems while deduplicating known findings and preserving investigation history.
  - Define service-level indicators for freshness, completeness, unresolved objects, collection
    latency, and remediation convergence per forest/domain.
  - **Done when:** operators can distinguish healthy, stale, partial, drifted, and failed AD coverage
    and can trace every alert to a versioned snapshot and source evidence.

- [ ] **Complete production hardening and support documentation.**
  - Publish a threat model, permissions matrix, deployment topologies, schema/API compatibility policy,
    backup and disaster-recovery runbooks, forest onboarding/offboarding, incident response, secure
    artifact sharing, retention/deletion, and break-glass procedures.
  - Add conformance suites for supported Windows Server functional levels, forest topologies, LDAP and
    AD PowerShell adapters, PowerShell versions, and worker platforms.
  - Mark each AD capability as experimental, preview, or stable and publish unsupported boundaries and
    known blind spots.
  - **Done when:** production adoption does not depend on undocumented maintainer knowledge and support
    status can be evaluated per capability and environment.

### Active Directory phase gates

1. **MVP gate:** read-only privileged-group inventory, hybrid correlation, classification, exports,
   reports, least-privilege deployment, and disposable-forest tests are complete.
2. **Effective-control gate:** ACL evaluation is evidence-backed and matches representative Windows
   behavior before it is presented as effective access.
3. **Attack-path gate:** delegation, replication, credential-control, GPO, and trust prerequisites are
   modeled and partial source coverage is visible before path findings are enabled by default.
4. **Extended-systems gate:** AD CS and directory-adjacent collectors remain optional and identify
   their separate permissions, network reachability, and confidence boundaries.
5. **Mutation gate:** no AD write command ships until reviewable plans, separate identities, safety
   thresholds, replication-aware verification, rollback, and disposable-forest tests exist.

## Suggested delivery sequence

1. Tenant-backed read-only integration tests and generated-data protection.
2. Active Directory Phase 1: read-only privileged identity MVP.
3. Mutation end-to-end tests and separate least-privilege identities.
4. Versioned schemas, API adapters, and structured observability.
5. Explicit context refactor and stable public API definition.
6. Classification provenance and drift gates.
7. Active Directory Phase 2: directory ACL and effective-control analysis.
8. Active Directory Phase 3: authentication, delegation, policy, credential-control, and trust paths.
9. Active Directory Phase 4: optional AD CS, adjacent systems, unified hybrid paths, and scale.
10. Preflight, dry-run plans, configuration presets, and operational runbooks.
11. Active Directory Phase 5: governed remediation and continuous operations.
12. CI portability, supply-chain provenance, accessibility, and scale testing.

## Backlog maintenance

- Convert an item into one or more tracked issues before implementation.
- Record the responsible subsystem, risk level, compatibility impact, and test strategy on each issue.
- Do not mark an item complete until its **Done when** condition is satisfied.
- Preserve backward compatibility by default; document and version unavoidable breaking changes.
