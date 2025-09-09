# Tasks — Shiksha Learning Platform (TDD-first)

Feature: Shiksha Learning Platform
Branch: `001-title-shiksha-learning`
Spec: `/Users/pavithra/ai/speckit/shiksha-learning-platform/specs/001-title-shiksha-learning/spec.md`

Notes: Tasks follow TDD order. Contract tests and integration tests are created before implementations. Tasks marked [P] can run in parallel when they touch different files.

T001 — Setup: Repo bootstrap and developer environment
- Description: Ensure repository has required build and dev tools configured for backend and frontend.
- Actions:
  - Add/verify `backend/` Spring Boot project (Java 17+, Maven/Gradle) skeleton if missing
  - Add/verify `frontend/` React + TypeScript skeleton with Tailwind
  - Add CI job placeholders for tests, modulith boundary checks, and build
  - Files/Paths: `/backend/`, `/frontend/`, `.github/workflows/` (CI)
  - Depends on: none

T002 — Setup: Database & infra config (local dev)
- Description: Add local Postgres setup and Terraform placeholders for RDS and S3
- Actions:
  - Add `docker-compose.yml` for local Postgres and optional localstack
  - Add `infra/terraform` skeleton with S3 and RDS modules referenced (no real creds)
  - Files/Paths: `docker-compose.yml`, `infra/terraform/`
  - Depends on: T001

T003 [P] — Contract tests: Auth API
- Description: Create failing contract tests for auth endpoints from `contracts/auth-openapi.yaml`.
- Actions:
  - Generate test skeleton asserting request/response schema for `/api/auth/register` and `/api/auth/login`
  - Files/Paths: `tests/contract/auth_contract_test.java` (backend)
  - Depends on: T001

T004 [P] — Contract tests: Assignments API
- Description: Create failing contract tests using `contracts/assignments-openapi.yaml`.
- Actions:
  - Add tests asserting POST `/api/assignments` request body validation and GET `/api/assignments/{id}` response schema
  - Files/Paths: `tests/contract/assignments_contract_test.java`
  - Depends on: T001

T005 [P] — Contract tests: Submissions API (file upload)
- Description: Add contract tests for uploads from `contracts/submissions-openapi.yaml`.
- Actions:
  - Create tests expecting multipart/form-data with PDF file and 201 response
  - Files/Paths: `tests/contract/submissions_contract_test.java`
  - Depends on: T001

T006 [P] — Contract tests: Grades API
- Description: Create failing contract tests for `/api/assignments/{id}/grades` from `contracts/grades-openapi.yaml`.
- Actions:
  - Add tests asserting payload shape and 200 response
  - Files/Paths: `tests/contract/grades_contract_test.java`
  - Depends on: T001

T007 [P] — Contract tests: Notifications API
- Description: Create failing contract tests for notifications listing endpoint.
- Actions:
  - Add test skeleton for GET `/api/notifications`
  - Files/Paths: `tests/contract/notifications_contract_test.java`
  - Depends on: T001

T008 [P] — Data models: Create JPA entities and repositories (one task per entity)
- Description: Create model classes and repository interfaces for all entities in `data-model.md`.
- Actions (parallelizable per entity):
  - Teacher: `backend/src/main/java/.../model/Teacher.java`, `.../repository/TeacherRepository.java`
  - Student: `backend/.../model/Student.java`, `.../repository/StudentRepository.java`
  - Assignment: `backend/.../model/Assignment.java`, `.../repository/AssignmentRepository.java`
  - Exam: `backend/.../model/Exam.java`, `.../repository/ExamRepository.java`
  - Submission: `backend/.../model/Submission.java`, `.../repository/SubmissionRepository.java`
  - Grade: `backend/.../model/Grade.java`, `.../repository/GradeRepository.java`
  - Notification: `backend/.../model/Notification.java`, `.../repository/NotificationRepository.java`
  - Files/Paths: `backend/src/main/java/.../model/*.java`, `backend/src/main/java/.../repository/*Repository.java`
  - Depends on: T001

T009 — DB integration test setup
- Description: Add testcontainers or local DB config and a base integration test class
- Actions:
  - Add Testcontainers Postgres config for integration tests
  - Files/Paths: `backend/src/test/java/.../BaseIntegrationTest.java`
  - Depends on: T001, T008

T010 — Implement Auth module (TDD)
- Description: Implement auth endpoints to satisfy contract tests using email/password and JWT issuance.
- Actions:
  - Add controllers, service, DTOs, security config (JWT filter), and tests
  - Files/Paths: `backend/src/main/java/.../auth/` and corresponding tests
  - Depends on: T003, T008, T009

T011 — Implement Assignments module (TDD)
- Description: Implement create and get assignment endpoints, persistence, and publish notification event.
- Actions:
  - Controllers, service, repository wiring, DTOs, and unit tests
  - Publish domain event `AssignmentPublishedEvent` for notifications
  - Files/Paths: `backend/src/main/java/.../assignment/`
  - Depends on: T004, T008, T009

T012 — Implement Submissions module (TDD)
- Description: Implement submission upload (presigned S3 flow) with PDF validation (<=5 MB), storage, and notification to teacher.
- Actions:
  - Add presigned URL endpoint or direct multipart handling, validation logic, S3 client wiring, and tests
  - Files/Paths: `backend/src/main/java/.../submission/`, `infra/terraform/s3.tf` (placeholder)
  - Depends on: T005, T008, T009, T002

T013 — Implement Grades module (TDD)
- Description: Implement grade publishing, gradebook persistence, and email notifications on publish.
- Actions:
  - Controllers, service, repository updates, and tests
  - Files/Paths: `backend/src/main/java/.../grade/`
  - Depends on: T006, T008, T009

T014 — Implement Notifications module (TDD)
- Description: Implement notification delivery (email via SES client) and in-app notification store.
- Actions:
  - Notification service, delivery adapters (email), inbox APIs, and tests
  - Files/Paths: `backend/src/main/java/.../notification/`
  - Depends on: T007, T008

T015 — Frontend: Auth flows and dashboard skeleton
- Description: Scaffold frontend auth (login/register) and a dashboard showing assignments and notifications.
- Actions:
  - Create React pages/components, API client, and basic styles with Tailwind
  - Files/Paths: `frontend/src/`
  - Depends on: T010

T016 — Frontend: Assignment view and submission flow
- Description: Implement assignment list, details, and submission flow (PDF upload) following quickstart scenarios.
- Actions:
  - Components for assignment list/page, submission UI with client-side size/type validation
  - Files/Paths: `frontend/src/features/assignments/`
  - Depends on: T011, T012

T017 — Frontend: Gradebook view
- Description: Implement gradebook UI for students and teachers to view published grades and feedback.
- Actions:
  - Gradebook components, API integration
  - Files/Paths: `frontend/src/features/gradebook/`
  - Depends on: T013

T018 [P] — Integration tests: Primary user stories
- Description: Create integration tests that execute the primary user stories from `quickstart.md`.
- Actions:
  - Student submission flow: create teacher, create assignment, student submits PDF, teacher receives notification
  - Grade publishing flow: teacher publishes grades, student receives email and sees grade
  - Files/Paths: `tests/integration/primary_story_tests.java` or frontend E2E tests
  - Depends on: T009, T010, T011, T012, T013

T019 — Observability & logging
- Description: Add structured logging, request tracing ids, and metrics hooks in backend modules.
- Actions:
  - Integrate Micrometer metrics and structured JSON logging
  - Files/Paths: `backend/src/main/resources/`, `backend/src/main/java/.../observability/`
  - Depends on: T001

T020 — Infra: Terraform and S3/CLOUDFRONT wiring
- Description: Implement Terraform modules for S3, CloudFront, and RDS; create placeholders for secrets and IAM roles.
- Actions:
  - Add `infra/terraform/modules/s3`, `infra/terraform/modules/rds`, and a top-level staging stack
  - Files/Paths: `infra/terraform/`
  - Depends on: T002

T021 — Polish: Unit tests and documentation
- Description: Add unit tests for services and update README/architecture docs.
- Actions:
  - Unit tests per service, update module READMEs, add C4 diagrams or Modulith reports
  - Files/Paths: `backend/src/test/unit/`, `docs/`
  - Depends on: T010..T014

T022 — Polish: Performance & security checks
- Description: Run basic load and security scans; fix obvious issues.
- Actions:
  - Add simple load test script, run Snyk/OWASP checks
  - Files/Paths: `ops/scripts/`, CI jobs
  - Depends on: T021

Parallel Task Groups (examples)
- Group A [P]: T003, T004, T005, T006, T007 (contract tests) — these can be created in parallel.
- Group B [P]: T008 (entities) per-entity subtasks — create these in parallel across files.
- Group C [P]: T018 (integration tests) and frontend scaffolding T015 can proceed in parallel once backend auth is implemented.

How to run a task (agent command examples)
- Java backend contract test generation (contract tests): `./mvnw -Dtest=**/*ContractTest test`
- Start local Postgres: `docker-compose up -d`
- Run frontend dev: `cd frontend && npm install && npm start`

Estimated total tasks: 22 (T001..T022). Prioritization: T001 -> T003..T009 (contracts + models) -> T009 -> T010..T014 (implementation) -> T015..T017 (frontend) -> T018..T022 (integration + polish).
