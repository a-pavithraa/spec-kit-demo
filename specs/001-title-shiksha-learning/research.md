## Phase 0 — Research for Shiksha Learning Platform

Decision: Technical context provided by product owner.

Rationale:
- Backend: Spring Boot 3.x with Spring Modulith chosen to enforce domain-first modularity and align with the project's constitution requiring module boundaries, test-first development, and explicit contracts.
- Frontend: React 18 + TypeScript + Tailwind CSS chosen for developer productivity, type-safety, and easy component styling; aligns with modular frontend guidelines in constitution.
- Database: PostgreSQL on AWS RDS for reliability and performance; choose single primary with read replicas later if needed.
- File Storage: AWS S3 for submissions and CloudFront as CDN for distribution of static assets and faster downloads.
- Infrastructure: Terraform to manage AWS resources reproducibly.
- Authentication: JWT tokens with role-based authorization; sessionless API for simplicity and easy scaling.

Alternatives considered:
- Backend: modular microservices vs modular monolith — modular monolith (Spring Modulith) chosen to reduce operational complexity while keeping modular boundaries.
- Auth: OAuth/SSO vs JWT — JWT chosen now for simplicity; SSO can be added later as an integration.

Performance & operational notes:
- Enforce upload size 5 MB at application and S3 presigned URL policy.
- Use connection pooling, prepared statement caching, and appropriate indexes for Postgres.
- Plan RDS instance class based on expected load; enable Performance Insights and slow query logging.

Security & compliance:
- Store only minimal PII; use TLS in transit and server-side encryption for S3 buckets.
- JWT signing keys stored in AWS Secrets Manager or Parameter Store; rotate keys periodically.

Constitution Check (initial): PASS
- Modular structure planned for features: modules for `user`, `assignment`, `submission`, `grade`, `notification`.
- Plan follows Test-first and explicit-contract principles.

Open questions / recommendations (not blockers):
- Backup and retention beyond 1 year for archival — recommend an archival policy to cold storage.
- Monitoring/observability: choose a logging/metrics stack (e.g., CloudWatch + Prometheus/Grafana) during implementation.

Outputs
- This research phase resolves the major technical decisions and feeds Phase 1.
