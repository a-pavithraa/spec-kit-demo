# Quickstart — Shiksha Learning Platform (developer smoke test)

Prerequisites
- Java 17+, Maven or Gradle
- Node 18+, npm/yarn
- PostgreSQL instance (local or RDS)
- AWS credentials with access to S3

Steps
1. Start the database locally or point to RDS.
2. Run backend tests (contract tests should fail initially): `./mvnw test` or `./gradlew test`.
3. Start backend: `./mvnw spring-boot:run`.
4. Start frontend: `cd frontend && npm install && npm start`.
5. Use Postman or OpenAPI UI to exercise endpoints: create teacher, create assignment, student submit PDF, teacher grade.

Smoke checks
- Creating an assignment sends an email notification (verify via dev SMTP or SES sandbox).
- Uploading a PDF larger than 5 MB returns a 413/validation error.
- Publishing grades triggers an email and visible entry in gradebook.
