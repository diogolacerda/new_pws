# ProtoWorks System (PWS) - Database Tasks

**Version:** 1.0
**Date:** December 17, 2025
**Scope:** PostgreSQL database schema, SQLAlchemy models, and Alembic migrations

---

## Overview

This document contains all database-related tasks for the PWS project. Tasks should be completed in the order presented, as later tasks depend on earlier ones. All tables implement soft delete (is_active flag) unless explicitly noted otherwise.

---

## Task Categories

- **DB-0XX**: Core infrastructure and base setup
- **DB-1XX**: User and authentication related tables
- **DB-2XX**: Company and organizational hierarchy tables
- **DB-3XX**: Protocol templates and items
- **DB-4XX**: Protocols and execution
- **DB-5XX**: Audit and logging

---

## Priority: MVP (Must Have)

### Epic 1: Database Infrastructure Setup

#### DB-001: Initialize Alembic and Database Configuration
- **Description**: Set up Alembic for database migrations, configure PostgreSQL connection, and create the base SQLAlchemy setup with async support.
- **Dependencies**: None
- **Acceptance Criteria**:
  - [ ] Alembic is initialized with proper directory structure
  - [ ] Database connection string is configured via environment variables
  - [ ] SQLAlchemy async engine is configured
  - [ ] Base model class is created with common fields (id, created_at, updated_at)
  - [ ] alembic.ini is properly configured
  - [ ] Initial migration can be generated and applied
  - [ ] Database connection pool is configured appropriately
- **Priority**: MVP
- **Complexity**: S (Small)

#### DB-002: Create Base Model Mixins
- **Description**: Create reusable SQLAlchemy mixins for common patterns: timestamps, soft delete, and audit fields.
- **Dependencies**: DB-001
- **Acceptance Criteria**:
  - [ ] TimestampMixin with created_at, updated_at fields
  - [ ] SoftDeleteMixin with is_active field and default True
  - [ ] AuditMixin with created_by, updated_by foreign keys
  - [ ] All mixins use proper SQLAlchemy 2.0 syntax
  - [ ] Mixins are documented with usage examples
- **Priority**: MVP
- **Complexity**: S (Small)

#### DB-003: Create Enum Types for Database
- **Description**: Define all PostgreSQL enum types used across the system as SQLAlchemy Enum types.
- **Dependencies**: DB-001
- **Acceptance Criteria**:
  - [ ] CompanyTypeEnum: MONTADORA, GERENCIADORA, COMISSIONADORA, FORNECEDOR, CLIENTE
  - [ ] UserRoleEnum: ADMIN, TECHNICIAN, MANAGER, ENGINEERING, SUPPLIER, CLIENT
  - [ ] ProtocolTypeEnum: C1, C2, C3, C4
  - [ ] ProtocolStatusEnum: All statuses from PRD section 5.2 and 5.4
  - [ ] ItemTypeEnum: CHECKBOX_OK_NOK_NA, TEXT_SINGLE, TEXT_MULTI, NUMBER, NUMBER_RANGE, PHOTO_CAPTURE, DATE, SIGNATURE
  - [ ] ItemApprovalStatusEnum: PENDING, APPROVED, REJECTED
  - [ ] PendencyTypeEnum: PA, PB, PC
  - [ ] PendencyStatusEnum: OPEN, RESOLVED
  - [ ] AuditActionEnum: CREATE, UPDATE, DELETE, LOGIN, LOGOUT, APPROVE, REJECT, SIGN, SYNC
  - [ ] All enums are properly documented
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 2: User and Authentication Tables

#### DB-100: Create Company Model
- **Description**: Create the Company table to store all company types involved in the commissioning process.
- **Dependencies**: DB-002, DB-003
- **Acceptance Criteria**:
  - [ ] Table name: companies
  - [ ] Fields: id, name (required), type (CompanyTypeEnum, required), cnpj (optional), address (optional), phone (optional), email (optional)
  - [ ] Implements SoftDeleteMixin (is_active)
  - [ ] Implements TimestampMixin (created_at, updated_at)
  - [ ] Proper indexes on name, type, is_active
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

#### DB-101: Create Discipline Model
- **Description**: Create the Discipline table for technical discipline categories (ELE, MEC, INS, etc.).
- **Dependencies**: DB-002
- **Acceptance Criteria**:
  - [ ] Table name: disciplines
  - [ ] Fields: id, name (required), code (required, max 10 chars), legend (required, max 5 chars)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Unique constraint on code
  - [ ] Index on is_active
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### DB-102: Create User Model
- **Description**: Create the User table with all authentication and profile fields, including signature image support.
- **Dependencies**: DB-100, DB-101, DB-003
- **Acceptance Criteria**:
  - [ ] Table name: users
  - [ ] Fields: id, email (unique, required), password_hash (required), name (required)
  - [ ] Fields: company_id (FK to companies, required), role (UserRoleEnum, required)
  - [ ] Fields: discipline_id (FK to disciplines, nullable)
  - [ ] Fields: signature_image_path (nullable, max 500 chars)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Unique constraint on email
  - [ ] Indexes on company_id, role, discipline_id, is_active
  - [ ] Foreign key constraints with proper ON DELETE behavior
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

#### DB-103: Create Refresh Token Model
- **Description**: Create table to store refresh tokens for JWT authentication with device tracking.
- **Dependencies**: DB-102
- **Acceptance Criteria**:
  - [ ] Table name: refresh_tokens
  - [ ] Fields: id, user_id (FK to users, required), token (unique, required), expires_at (required)
  - [ ] Fields: device_info (nullable), ip_address (nullable, max 45 chars)
  - [ ] Fields: created_at, revoked_at (nullable)
  - [ ] Index on token, user_id, expires_at
  - [ ] Cascade delete when user is deleted
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

---

### Epic 3: Organizational Hierarchy Tables

#### DB-200: Create Project Model
- **Description**: Create the Project table representing commissioning projects.
- **Dependencies**: DB-100
- **Acceptance Criteria**:
  - [ ] Table name: projects
  - [ ] Fields: id, name (required), code (required), description (optional)
  - [ ] Fields: client_company_id (FK to companies, required for client reference)
  - [ ] Fields: start_date (nullable), expected_end_date (nullable)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Implements AuditMixin (created_by, updated_by)
  - [ ] Index on code, client_company_id, is_active
  - [ ] Unique constraint on code
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

#### DB-201: Create Project Companies Association Table
- **Description**: Create many-to-many relationship table between Projects and participating Companies (Montadora, Gerenciadora).
- **Dependencies**: DB-200, DB-100
- **Acceptance Criteria**:
  - [ ] Table name: project_companies
  - [ ] Fields: id, project_id (FK), company_id (FK), company_role (to distinguish Montadora from Gerenciadora)
  - [ ] Implements TimestampMixin
  - [ ] Unique constraint on (project_id, company_id, company_role)
  - [ ] Cascade delete behavior defined
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### DB-202: Create Area Model
- **Description**: Create the Area table representing areas within a project.
- **Dependencies**: DB-200
- **Acceptance Criteria**:
  - [ ] Table name: areas
  - [ ] Fields: id, project_id (FK to projects, required), name (required), code (required), description (optional)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Implements AuditMixin
  - [ ] Unique constraint on (project_id, code)
  - [ ] Index on project_id, is_active
  - [ ] Cascade behavior when project is deleted
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### DB-203: Create System Model
- **Description**: Create the System table representing systems within an area.
- **Dependencies**: DB-202
- **Acceptance Criteria**:
  - [ ] Table name: systems
  - [ ] Fields: id, area_id (FK to areas, required), name (required), code (required), description (optional)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Implements AuditMixin
  - [ ] Unique constraint on (area_id, code)
  - [ ] Index on area_id, is_active
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### DB-204: Create Subsystem Model
- **Description**: Create the Subsystem table representing subsystems within a system. This is critical for C2/C3/C4 dependency checks.
- **Dependencies**: DB-203
- **Acceptance Criteria**:
  - [ ] Table name: subsystems
  - [ ] Fields: id, system_id (FK to systems, required), name (required), code (required), description (optional)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Implements AuditMixin
  - [ ] Unique constraint on (system_id, code)
  - [ ] Index on system_id, is_active
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### DB-205: Create Equipment Model
- **Description**: Create the Equipment table representing individual equipment items with unique tag identifiers.
- **Dependencies**: DB-204, DB-101
- **Acceptance Criteria**:
  - [ ] Table name: equipments
  - [ ] Fields: id, tag_id (unique, required, max 50 chars), application (required, max 500 chars)
  - [ ] Fields: subsystem_id (FK to subsystems, required), discipline_id (FK to disciplines, nullable)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Implements AuditMixin
  - [ ] Unique constraint on tag_id
  - [ ] Index on subsystem_id, discipline_id, tag_id, is_active
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 4: Protocol Template Tables

#### DB-300: Create Protocol Template Model
- **Description**: Create the Protocol Template table for reusable protocol definitions.
- **Dependencies**: DB-101, DB-003
- **Acceptance Criteria**:
  - [ ] Table name: protocol_templates
  - [ ] Fields: id, type (ProtocolTypeEnum, required), code (required), description (optional)
  - [ ] Fields: discipline_id (FK to disciplines, required for C1, nullable for C2/C3/C4)
  - [ ] Fields: version (integer, default 1)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Implements AuditMixin
  - [ ] Unique constraint on (code, version)
  - [ ] Index on type, discipline_id, is_active
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

#### DB-301: Create Protocol Template Item Model
- **Description**: Create the Protocol Template Item table defining the structure of items within a template.
- **Dependencies**: DB-300, DB-003
- **Acceptance Criteria**:
  - [ ] Table name: protocol_template_items
  - [ ] Fields: id, protocol_template_id (FK, required)
  - [ ] Fields: code (required, max 50 chars), description (required, max 500 chars)
  - [ ] Fields: help_text (nullable, max 1000 chars), order (required integer)
  - [ ] Fields: item_type (ItemTypeEnum, required)
  - [ ] Fields: config (JSON, nullable) for type-specific configuration
  - [ ] Fields: required (boolean, default True)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Implements AuditMixin
  - [ ] Unique constraint on (protocol_template_id, code)
  - [ ] Index on protocol_template_id, order, is_active
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 5: Protocol Execution Tables

#### DB-400: Create Protocol Model
- **Description**: Create the Protocol table for protocol instances created from templates.
- **Dependencies**: DB-300, DB-003
- **Acceptance Criteria**:
  - [ ] Table name: protocols
  - [ ] Fields: id, template_id (FK to protocol_templates, required)
  - [ ] Fields: status (ProtocolStatusEnum, required, default CREATED)
  - [ ] Fields: current_company_id (FK to companies, nullable) - tracks which company is responsible
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Implements AuditMixin
  - [ ] Index on template_id, status, is_active
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

#### DB-401: Create Protocol Equipment Company Model (N:N Relationship)
- **Description**: Create the junction table for the many-to-many relationship between Protocol, Equipment, and Company.
- **Dependencies**: DB-400, DB-205, DB-100
- **Acceptance Criteria**:
  - [ ] Table name: protocol_equipment_companies
  - [ ] Fields: id, protocol_id (FK, required), equipment_id (FK, required), company_id (FK, required)
  - [ ] Fields: status (varchar, default 'CREATED')
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Unique constraint on (protocol_id, equipment_id, company_id)
  - [ ] Indexes on each foreign key column
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

#### DB-402: Create Protocol Item Model
- **Description**: Create the Protocol Item table storing filled values for each template item instance.
- **Dependencies**: DB-400, DB-301, DB-003
- **Acceptance Criteria**:
  - [ ] Table name: protocol_items
  - [ ] Fields: id, protocol_id (FK, required), template_item_id (FK to protocol_template_items, required)
  - [ ] Fields: value (JSON, nullable) - stores typed values as per PRD 6.3
  - [ ] Fields: approval_status (ItemApprovalStatusEnum, default PENDING)
  - [ ] Fields: filled_at (datetime, nullable), filled_by (FK to users, nullable), filled_ip (varchar 45, nullable)
  - [ ] Fields: approved_at (datetime, nullable), approved_by (FK to users, nullable), approved_ip (varchar 45, nullable)
  - [ ] Implements TimestampMixin
  - [ ] Unique constraint on (protocol_id, template_item_id)
  - [ ] Indexes on protocol_id, template_item_id, approval_status
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: M (Medium)

#### DB-403: Create Protocol Status History Model
- **Description**: Create an append-only table tracking all status changes for protocols.
- **Dependencies**: DB-400, DB-102, DB-003
- **Acceptance Criteria**:
  - [ ] Table name: protocol_status_history
  - [ ] Fields: id, protocol_id (FK, required)
  - [ ] Fields: previous_status (ProtocolStatusEnum, nullable - null for initial status)
  - [ ] Fields: new_status (ProtocolStatusEnum, required)
  - [ ] Fields: user_id (FK to users, required)
  - [ ] Fields: timestamp (datetime, required, default now)
  - [ ] Fields: ip_address (varchar 45, required)
  - [ ] Fields: reason (text, nullable) - for rejections
  - [ ] NO soft delete - records are immutable
  - [ ] Index on protocol_id, user_id, timestamp
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 6: Pendency Tables

#### DB-500: Create Pendency Model
- **Description**: Create the Pendency table for tracking issues on protocol items (PA/PB/PC).
- **Dependencies**: DB-402, DB-102, DB-003
- **Acceptance Criteria**:
  - [ ] Table name: pendencies
  - [ ] Fields: id, protocol_item_id (FK to protocol_items, required)
  - [ ] Fields: type (PendencyTypeEnum, required)
  - [ ] Fields: description (text, required)
  - [ ] Fields: status (PendencyStatusEnum, default OPEN)
  - [ ] Fields: created_by (FK to users, required), created_at (datetime, required), created_ip (varchar 45, required)
  - [ ] Fields: resolved_by (FK to users, nullable), resolved_at (datetime, nullable), resolved_ip (varchar 45, nullable)
  - [ ] Fields: resolution_description (text, nullable)
  - [ ] NO soft delete - pendencies cannot be deleted (per PRD)
  - [ ] Implements TimestampMixin for updated_at
  - [ ] Index on protocol_item_id, type, status, created_by
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

#### DB-501: Create Pendency History Model
- **Description**: Create table to track all changes to pendencies (type changes, comments).
- **Dependencies**: DB-500, DB-102
- **Acceptance Criteria**:
  - [ ] Table name: pendency_history
  - [ ] Fields: id, pendency_id (FK, required)
  - [ ] Fields: action (varchar, required) - 'TYPE_CHANGE', 'COMMENT', 'RESOLVED'
  - [ ] Fields: previous_type (PendencyTypeEnum, nullable)
  - [ ] Fields: new_type (PendencyTypeEnum, nullable)
  - [ ] Fields: comment (text, nullable)
  - [ ] Fields: user_id (FK to users, required)
  - [ ] Fields: timestamp (datetime, required, default now)
  - [ ] Fields: ip_address (varchar 45, required)
  - [ ] NO soft delete - immutable records
  - [ ] Index on pendency_id, timestamp
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 7: Attachment Tables

#### DB-600: Create Attachment Model
- **Description**: Create the Attachment table for storing file references (photos, PDFs) linked to various entities.
- **Dependencies**: DB-102
- **Acceptance Criteria**:
  - [ ] Table name: attachments
  - [ ] Fields: id, entity_type (varchar, required) - 'protocol', 'protocol_item', 'pendency', 'user_signature'
  - [ ] Fields: entity_id (integer, required)
  - [ ] Fields: file_path (varchar 500, required) - path in cloud storage
  - [ ] Fields: file_name (varchar 255, required) - original file name
  - [ ] Fields: file_type (varchar 50, required) - 'image/jpeg', 'image/png', 'application/pdf'
  - [ ] Fields: file_size (integer, required) - size in bytes
  - [ ] Fields: uploaded_by (FK to users, required)
  - [ ] Implements SoftDeleteMixin
  - [ ] Implements TimestampMixin
  - [ ] Index on (entity_type, entity_id), uploaded_by, is_active
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 8: Signature Tables

#### DB-700: Create Protocol Signature Model
- **Description**: Create table to store digital signatures on protocols from various approvers.
- **Dependencies**: DB-400, DB-102
- **Acceptance Criteria**:
  - [ ] Table name: protocol_signatures
  - [ ] Fields: id, protocol_id (FK, required)
  - [ ] Fields: user_id (FK to users, required)
  - [ ] Fields: signature_image_path (varchar 500, required) - path to signature image
  - [ ] Fields: signed_at (datetime, required)
  - [ ] Fields: ip_address (varchar 45, required)
  - [ ] Fields: action (varchar 50, required) - 'APPROVED', 'VALIDATED', 'FINALIZED'
  - [ ] NO soft delete - signatures are immutable
  - [ ] Unique constraint on (protocol_id, user_id, action)
  - [ ] Index on protocol_id, user_id, signed_at
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 9: Audit Log Table

#### DB-800: Create Audit Log Model
- **Description**: Create the immutable audit log table for tracking all system operations.
- **Dependencies**: DB-102, DB-003
- **Acceptance Criteria**:
  - [ ] Table name: audit_logs
  - [ ] Fields: id (BigInteger primary key for high volume)
  - [ ] Fields: user_id (FK to users, required)
  - [ ] Fields: timestamp (datetime, required, default now, indexed)
  - [ ] Fields: ip_address (varchar 45, required)
  - [ ] Fields: user_agent (varchar 500, nullable)
  - [ ] Fields: action (AuditActionEnum, required)
  - [ ] Fields: entity_type (varchar 100, required)
  - [ ] Fields: entity_id (integer, required)
  - [ ] Fields: previous_data (JSON, nullable)
  - [ ] Fields: new_data (JSON, nullable)
  - [ ] Fields: metadata (JSON, nullable) - for additional context
  - [ ] NO soft delete - logs are immutable and cannot be modified
  - [ ] Composite index on (entity_type, entity_id)
  - [ ] Composite index on (user_id, timestamp)
  - [ ] Index on timestamp for retention queries
  - [ ] Table is append-only (consider partitioning for future)
  - [ ] Migration creates the table correctly
- **Priority**: MVP
- **Complexity**: M (Medium)

---

## Priority: Post-MVP (Should Have)

### Epic 10: Import Tracking Tables

#### DB-900: Create Import Job Model
- **Description**: Create table to track Excel/CSV import jobs and their results.
- **Dependencies**: DB-102
- **Acceptance Criteria**:
  - [ ] Table name: import_jobs
  - [ ] Fields: id, entity_type (varchar, required) - which entity being imported
  - [ ] Fields: file_name (varchar 255, required)
  - [ ] Fields: file_path (varchar 500, required) - stored copy of original file
  - [ ] Fields: status (varchar) - 'PENDING', 'PROCESSING', 'COMPLETED', 'FAILED'
  - [ ] Fields: total_rows (integer, nullable)
  - [ ] Fields: processed_rows (integer, default 0)
  - [ ] Fields: success_rows (integer, default 0)
  - [ ] Fields: error_rows (integer, default 0)
  - [ ] Fields: error_details (JSON, nullable) - detailed error messages per row
  - [ ] Fields: started_at (datetime, nullable), completed_at (datetime, nullable)
  - [ ] Fields: created_by (FK to users, required)
  - [ ] Implements TimestampMixin
  - [ ] Index on status, entity_type, created_by
  - [ ] Migration creates the table correctly
- **Priority**: Post-MVP
- **Complexity**: S (Small)

---

## Priority: Phase 2 (Could Have)

### Epic 11: Offline Sync Support Tables

#### DB-1000: Create Sync Queue Model
- **Description**: Create table to track offline sync operations pending server confirmation (for future offline support).
- **Dependencies**: DB-102
- **Acceptance Criteria**:
  - [ ] Table name: sync_queue
  - [ ] Fields: id, local_id (varchar, required) - UUID generated on client
  - [ ] Fields: user_id (FK, required)
  - [ ] Fields: operation (varchar) - 'CREATE', 'UPDATE', 'DELETE'
  - [ ] Fields: entity_type (varchar, required)
  - [ ] Fields: entity_id (integer, nullable)
  - [ ] Fields: data (JSON, required)
  - [ ] Fields: client_timestamp (datetime, required)
  - [ ] Fields: server_timestamp (datetime, nullable)
  - [ ] Fields: status (varchar) - 'PENDING', 'SYNCED', 'CONFLICT', 'FAILED'
  - [ ] Fields: conflict_data (JSON, nullable)
  - [ ] Fields: attempts (integer, default 0)
  - [ ] Fields: last_error (text, nullable)
  - [ ] Implements TimestampMixin
  - [ ] Index on user_id, status, client_timestamp
  - [ ] Migration creates the table correctly
- **Priority**: Phase 2
- **Complexity**: M (Medium)

---

## Dependency Graph

```
DB-001 (Alembic Setup)
  |
  +-- DB-002 (Base Mixins)
  |     |
  |     +-- DB-100 (Company) ----+
  |     |     |                  |
  |     |     +-- DB-200 (Project)
  |     |     |     |
  |     |     |     +-- DB-201 (Project Companies)
  |     |     |     |
  |     |     |     +-- DB-202 (Area)
  |     |     |           |
  |     |     |           +-- DB-203 (System)
  |     |     |                 |
  |     |     |                 +-- DB-204 (Subsystem)
  |     |     |                       |
  |     |     +-- DB-102 (User) ------+-- DB-205 (Equipment)
  |     |     |     |                       |
  |     |     |     +-- DB-103 (Refresh Token)
  |     |     |
  |     +-- DB-101 (Discipline) --+
  |           |                   |
  |           +-- DB-300 (Protocol Template)
  |                 |
  |                 +-- DB-301 (Template Item)
  |                       |
  +-- DB-003 (Enums) -----+-- DB-400 (Protocol)
                          |     |
                          |     +-- DB-401 (Protocol Equipment Company)
                          |     |
                          |     +-- DB-402 (Protocol Item)
                          |     |     |
                          |     |     +-- DB-500 (Pendency)
                          |     |           |
                          |     |           +-- DB-501 (Pendency History)
                          |     |
                          |     +-- DB-403 (Status History)
                          |     |
                          |     +-- DB-700 (Protocol Signature)
                          |
                          +-- DB-600 (Attachment)
                          |
                          +-- DB-800 (Audit Log)

Post-MVP:
DB-900 (Import Job) -- depends on DB-102

Phase 2:
DB-1000 (Sync Queue) -- depends on DB-102
```

---

## Suggested Implementation Order

### Sprint 1: Foundation (Days 1-3)
1. DB-001: Initialize Alembic
2. DB-002: Create Base Mixins
3. DB-003: Create Enum Types
4. DB-100: Create Company Model
5. DB-101: Create Discipline Model

### Sprint 2: Users and Hierarchy (Days 4-6)
6. DB-102: Create User Model
7. DB-103: Create Refresh Token Model
8. DB-200: Create Project Model
9. DB-201: Create Project Companies
10. DB-202: Create Area Model

### Sprint 3: Hierarchy Completion (Days 7-8)
11. DB-203: Create System Model
12. DB-204: Create Subsystem Model
13. DB-205: Create Equipment Model

### Sprint 4: Templates (Days 9-10)
14. DB-300: Create Protocol Template Model
15. DB-301: Create Protocol Template Item Model

### Sprint 5: Protocols (Days 11-13)
16. DB-400: Create Protocol Model
17. DB-401: Create Protocol Equipment Company
18. DB-402: Create Protocol Item Model
19. DB-403: Create Protocol Status History

### Sprint 6: Supporting Tables (Days 14-16)
20. DB-500: Create Pendency Model
21. DB-501: Create Pendency History
22. DB-600: Create Attachment Model
23. DB-700: Create Protocol Signature Model
24. DB-800: Create Audit Log Model

### Post-MVP
25. DB-900: Create Import Job Model

### Phase 2
26. DB-1000: Create Sync Queue Model

---

## Database Constraints and Considerations

### Soft Delete Policy
All entities except the following use soft delete (is_active = False):
- **audit_logs**: Immutable, never deleted
- **protocol_status_history**: Immutable, never deleted
- **pendencies**: Cannot be deleted per business rule
- **pendency_history**: Immutable, never deleted
- **protocol_signatures**: Immutable, never deleted

### Foreign Key Considerations
- Use `ON DELETE RESTRICT` for critical relationships (User -> Company, Protocol -> Template)
- Use `ON DELETE CASCADE` for dependent child records (Area -> Systems)
- Always filter queries by `is_active = True` unless specifically needed

### Index Strategy
- Primary keys are auto-indexed
- Foreign keys should be indexed for JOIN performance
- Add composite indexes for common query patterns
- Consider partial indexes on `is_active = True` for frequently queried tables

### JSON Field Guidelines
- Use JSON for flexible configurations (template item config)
- Use JSON for typed values (protocol item values)
- Consider JSONB in PostgreSQL for better query performance
- Document expected JSON schemas in model comments

---

## Risk Considerations

1. **High Volume Audit Logs**: Consider table partitioning by timestamp for the audit_logs table if volume exceeds millions of records
2. **Protocol Status Enum**: May need to add new statuses in future - use database-level enum carefully
3. **Equipment Tag Uniqueness**: Global uniqueness of tag_id - verify with stakeholder if this should be per-project
4. **Cascade Deletes**: Review cascade behavior carefully - soft delete should prevent most issues but test thoroughly

---

**End of Database Tasks Document**
