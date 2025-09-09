# [PROJECT_NAME] Constitution
<!-- Project-level architecture & agent guidance. Tailor this document when modules or frontend responsibilities change. -->

## Core Principles

1. Domain-first modularity
   - Align code modules with bounded contexts and business domains. Keep each module understandable and cohesive.

2. Test-first and verifiable boundaries
   - Write tests before implementation. Enforce module boundaries with automated checks and tests.

3. Explicit contracts only
   - Cross-module access must go through declared interfaces, named interfaces, or domain events.

4. Observability and simplicity
   - Structured logs, metrics, and clear error semantics. Prefer simple, readable solutions over premature optimization.

5. Incremental refactorability
   - Design so modules can be extracted or split with minimal friction as the product grows.

## Spring Modulith — Best Practices

- Module organization
  - Map each module to a single business or technical responsibility (e.g., `order`, `user`, `notification`).
  - Place modules under top-level packages beneath the main application package. Use module descriptors/annotations to declare module boundaries (examples: `@ApplicationModule`, `@NamedInterface` or equivalents provided by Spring Modulith).
  - Keep module internals private; publish only a small explicit surface of interfaces and Spring beans.

- Dependency management
  - Enforce unidirectional dependencies. Avoid cycles between modules.
  - Depend on abstractions (interfaces) not concrete implementations. Prefer constructor injection.
  - Use build-time or CI checks (Spring Modulith tooling or custom checks) to detect boundary violations and fail the build on violations.

- Communication patterns
  - Prefer event-driven interactions (domain events) for loose coupling. Use Spring application events or a lightweight messaging layer when appropriate.
  - Allow synchronous requests only via declared module interfaces/APIs (`@NamedInterface`, REST, gRPC client, etc.).

- Data isolation
  - Prefer separate schemas or clearly partitioned tables per module. Treat module data as private — access via repository or DAO interfaces only.

- Testing and verification
  - Unit-test module internals and write module-level integration tests that exercise published interfaces only.
  - Use Spring Modulith's test support to assert module boundaries and visible contracts.

- Documentation & architecture
  - Generate and maintain module-level documentation (C4, module diagrams, or spring-modulith-generated docs) as part of PRs. Keep diagrams updated when boundaries change.

- CI / Governance
  - CI must run boundary checks, run module-scoped tests, and block merges on contract/API or boundary violations.

## React (Frontend) — Best Practices for a Modular Project

- Folder & feature structure
  - Group code by feature/module (e.g., `features/Orders`, `features/Users`, `shared/`, `lib/`). Align frontend feature modules with backend bounded contexts when it simplifies contracts.
  - Co-locate component, styles, tests, and mocks for each feature.

- Type Safety & API contracts
  - Use TypeScript. Define shared DTO types for API responses/requests and keep them in a shared package or mono-repo workspace consumed by both frontend and backend when feasible.

- State & data flow
  - Prefer local component state or scoped state per feature. When global state is required, keep thin, module-scoped slices (Redux Toolkit, Zustand, etc.).

- Communication with backend
  - Call backend module APIs through an explicit client layer per module. Centralize API clients, error mapping, and auth logic to avoid scattered network calls.

- Performance & bundling
  - Use route- and feature-level code splitting. Enforce bundle budgets in CI and monitor via build reports.

- Styling & accessibility
  - Use CSS Modules / styled solutions with strict scoping. Run automated accessibility checks (axe) and include Storybook for visual docs and regressions.

- Testing & docs
  - Ship unit tests for components and integration tests for feature flows. Update Storybook and the module README with usage and contract details.

## Agent Instruction File Guidelines (for coding agents / automated contributors)

Agent instruction files (the files that drive automated coding agents or scaffolding scripts) must enforce the same modular rules as the codebase and be explicit about scope, tests, and docs.

- Scope & boundaries
  - Every change must declare the target module and only modify files within that module unless a documented cross-module migration is approved.
  - Prohibit direct imports into another module's internal packages. Require calls through the declared `@NamedInterface` or module API client.

- Interface-first edits
  - Agents must create or update interface contracts and failing test stubs before implementing behavior (tests → implementation → refactor).

- Prefer events over internal calls
  - Suggest domain events for cross-module interactions. If synchronous interaction is necessary, call the module's public API only.

- Data access rules
  - Agents must read/write only via module repository/DAO interfaces. Do not modify another module's schema without approval and documented migration steps.

- Tests & documentation
  - Agent-generated changes must include:
    - Unit tests (happy path + at least one failure case where relevant)
    - Module-level integration test that exercises the public API
    - Updated module README or architecture docs (C4 diagram snapshot or link)

## Quick Checklist (applies to backend + frontend + agents)

- Module boundaries declared and enforced — Required
- Unidirectional dependencies — Required
- DTOs / contracts versioned and typed — Required
- Events used for cross-module async behavior — Recommended
- Module-scoped tests + CI verification — Required
- Documentation (C4 / module diagrams) updated with PRs — Required
- Accessibility & bundle budgets enforced for UI — Recommended/Required

## Practical examples (short)

- Creating a feature
  1. Choose or create a module (e.g., `features/orders`).
  2. Add interface contract (public API) and failing tests first.
  3. Implement internals and repository; keep data private.
  4. Expose functionality via interfaces or events; update docs and diagrams; add CI checks.

- Approved cross-module call (pattern)
  - Caller -> call target module's public client or `@NamedInterface` -> target responds or publishes an event.

## Additional info about Spring Modulith

- Spring Modulith is a lightweight framework that helps enforce modular-monolith boundaries in Spring Boot apps. It provides:
  - Module discovery and reporting
  - Test support for module boundaries
  - Tools to generate module documentation and visualizations
  - Annotations and conventions to declare explicit module APIs (e.g., named interfaces, events)

- Practical tips
  - Add modulith metadata to each module package and run modulith reports in CI.
  - Use Modulith's test assertions to fail fast on accidental internal API usage.

## References

- Spring Modulith docs: https://docs.spring.io/spring-modulith/reference/index.html
- Introducing Spring Modulith: https://spring.io/blog/2022/10/21/introducing-spring-modulith
- JetBrains on Modulith fundamentals: https://www.jetbrains.com/help/idea/spring-modulith.html
- Practical guides & examples: https://bell-sw.com/blog/how-to-build-a-modular-application-with-spring-modulith/

## Governance

- Amendments: update this `memory/constitution.md` and include a migration plan in the PR when changing module boundaries or API contracts.
- Compliance: CI must run boundary checks, tests, and docs generation; PRs should reference updated architecture docs when relevant.

**Version**: 1.0 | **Ratified**: [RATIFICATION_DATE] | **Last Amended**: [LAST_AMENDED_DATE]