# Data Model — Shiksha Learning Platform

## Entities

- Teacher
  - id: UUID
  - name: string
  - email: string
  - roles: ["TEACHER"]

- Student
  - id: UUID
  - name: string
  - email: string
  - enrolled_classes: [UUID]

- Assignment
  - id: UUID
  - title: string
  - description: text
  - attachments: [{key, filename, url, size_bytes}]
  - created_by: teacher_id (UUID)
  - created_at: timestamp
  - due_date: timestamp
  - visibility: {class_ids, section_ids}

- Exam
  - id: UUID
  - title: string
  - schedule_start: timestamp
  - duration_minutes: int
  - instructions: text
  - visibility: {class_ids, section_ids}

- Submission
  - id: UUID
  - assignment_id: UUID
  - student_id: UUID
  - file_key: S3 key
  - file_name: string
  - file_size: int
  - submitted_at: timestamp
  - is_late: boolean
  - version: int

- Grade
  - id: UUID
  - submission_id: UUID
  - assignment_id: UUID
  - student_id: UUID
  - score: decimal
  - feedback: text
  - published_at: timestamp
  - adjusted_by: teacher_id

- Notification
  - id: UUID
  - recipient_id: UUID
  - type: enum [ASSIGNMENT_PUBLISHED, SUBMISSION_RECEIVED, GRADE_PUBLISHED, EXAM_SCHEDULED]
  - channel: enum [EMAIL, IN_APP]
  - payload: json
  - sent_at: timestamp
  - read_at: timestamp | null

## Relationships
- Teacher (1) -> Assignment (N)
- Student (N) -> Submission (N)
- Assignment (1) -> Submission (N)
- Submission (1) -> Grade (0..1)

## Indexing suggestions
- Index submissions by (assignment_id, student_id)
- Index grades by (student_id, published_at)
- Index assignments by (visibility, due_date)
