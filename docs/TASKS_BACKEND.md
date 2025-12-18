# ProtoWorks System (PWS) - Backend Tasks

**Version:** 1.0
**Date:** December 17, 2025
**Scope:** FastAPI endpoints, services, authentication, business logic

---

## Overview

This document contains all backend API tasks for the PWS project. Tasks are organized by module and priority. Backend tasks depend on the corresponding database models being completed first.

---

## Task Categories

- **BE-0XX**: Core infrastructure, configuration, and middleware
- **BE-1XX**: Authentication and authorization
- **BE-2XX**: User management and administration
- **BE-3XX**: Organizational hierarchy CRUD (Companies, Projects, Areas, Systems, Subsystems, Disciplines, Equipment)
- **BE-4XX**: Protocol templates
- **BE-5XX**: Protocols and execution
- **BE-6XX**: Pendencies
- **BE-7XX**: Signatures and attachments
- **BE-8XX**: Audit and reporting
- **BE-9XX**: Import/Export

---

## Priority: MVP (Must Have)

### Epic 1: Backend Infrastructure

#### BE-001: Project Structure and Configuration
- **Description**: Set up the FastAPI project structure with proper configuration management, environment variables, and dependency injection.
- **Dependencies**: None
- **Acceptance Criteria**:
  - [ ] Project follows structure from PRD section 8.3 (pws-api)
  - [ ] Configuration loaded from environment variables using Pydantic Settings
  - [ ] Separate configs for development, staging, production
  - [ ] CORS middleware configured
  - [ ] Proper logging setup (structured JSON logs)
  - [ ] Health check endpoint at /health
  - [ ] API versioning with /api/v1 prefix
  - [ ] OpenAPI documentation enabled at /docs and /redoc
  - [ ] requirements.txt with all dependencies
  - [ ] Dockerfile for containerization
- **Priority**: MVP
- **Complexity**: M (Medium)

#### BE-002: Database Session Management
- **Description**: Implement async SQLAlchemy session management with proper connection pooling and dependency injection for FastAPI.
- **Dependencies**: BE-001, DB-001
- **Acceptance Criteria**:
  - [ ] Async SQLAlchemy engine configuration
  - [ ] Session factory with proper scoping
  - [ ] FastAPI dependency for database sessions (get_db)
  - [ ] Connection pool configuration (min, max, overflow)
  - [ ] Proper session cleanup on request completion
  - [ ] Transaction management helpers
  - [ ] Unit tests for session management
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-003: Error Handling and Response Schemas
- **Description**: Implement global error handling, custom exception classes, and standardized response schemas.
- **Dependencies**: BE-001
- **Acceptance Criteria**:
  - [ ] Custom exception classes (NotFoundError, ValidationError, AuthorizationError, etc.)
  - [ ] Global exception handler middleware
  - [ ] Standardized error response schema: {error: {code, message, details}}
  - [ ] Standardized success response schema: {data, meta}
  - [ ] Pagination response schema: {data, meta: {total, page, per_page, pages}}
  - [ ] HTTP status codes properly mapped to exceptions
  - [ ] Stack traces hidden in production, shown in development
  - [ ] Unit tests for error handling
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-004: Audit Logging Middleware
- **Description**: Implement middleware/service to automatically log all operations to the audit_logs table.
- **Dependencies**: BE-002, DB-800
- **Acceptance Criteria**:
  - [ ] Capture user_id from JWT token
  - [ ] Capture IP address from request (handle proxies with X-Forwarded-For)
  - [ ] Capture user agent from request headers
  - [ ] Log CREATE, UPDATE, DELETE operations with before/after data
  - [ ] Log LOGIN, LOGOUT events
  - [ ] Log APPROVE, REJECT, SIGN actions for protocols
  - [ ] Async log writing (non-blocking)
  - [ ] Service method for manual audit logging
  - [ ] Unit tests for audit logging
- **Priority**: MVP
- **Complexity**: M (Medium)

---

### Epic 2: Authentication and Authorization

#### BE-100: Pydantic Schemas for Authentication
- **Description**: Create Pydantic v2 schemas for all authentication-related request/response models.
- **Dependencies**: BE-001
- **Acceptance Criteria**:
  - [ ] LoginRequest: email (EmailStr), password (min 8 chars)
  - [ ] LoginResponse: access_token, refresh_token, token_type, expires_in
  - [ ] RefreshTokenRequest: refresh_token
  - [ ] UserResponse: id, email, name, company, role, discipline, is_active
  - [ ] ChangePasswordRequest: current_password, new_password
  - [ ] All schemas have proper validation and examples
  - [ ] Schemas documented with Field descriptions
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### BE-101: Password Hashing Service
- **Description**: Implement secure password hashing using bcrypt with configurable cost factor.
- **Dependencies**: BE-001
- **Acceptance Criteria**:
  - [ ] Use passlib with bcrypt backend
  - [ ] Cost factor of 12 (configurable)
  - [ ] hash_password(plain_text) -> hashed_password
  - [ ] verify_password(plain_text, hashed_password) -> bool
  - [ ] Timing-safe comparison
  - [ ] Unit tests for hashing and verification
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### BE-102: JWT Token Service
- **Description**: Implement JWT token generation and validation with access and refresh tokens.
- **Dependencies**: BE-001, BE-100
- **Acceptance Criteria**:
  - [ ] Use python-jose for JWT handling
  - [ ] Access token with configurable expiration (default 30 min)
  - [ ] Refresh token with configurable expiration (default 7 days)
  - [ ] Token payload: user_id, email, role, company_id, discipline_id, exp, iat
  - [ ] create_access_token(user) -> token_string
  - [ ] create_refresh_token(user) -> token_string
  - [ ] decode_token(token) -> payload or raise exception
  - [ ] Token blacklisting support (for logout)
  - [ ] Unit tests for token operations
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-103: Authentication Endpoints
- **Description**: Implement login, logout, refresh token, and me (current user) endpoints.
- **Dependencies**: BE-100, BE-101, BE-102, BE-002, DB-102, DB-103
- **Acceptance Criteria**:
  - [ ] POST /api/v1/auth/login - authenticate user, return tokens
  - [ ] POST /api/v1/auth/logout - revoke refresh token
  - [ ] POST /api/v1/auth/refresh - get new access token using refresh token
  - [ ] GET /api/v1/auth/me - get current user info
  - [ ] Login logs audit entry (success and failure)
  - [ ] Failed login returns 401 with generic message (no user enumeration)
  - [ ] Refresh token stored in database with device info
  - [ ] Rate limiting on login endpoint (100 req/min per IP)
  - [ ] Integration tests for all endpoints
- **Priority**: MVP
- **Complexity**: M (Medium)

#### BE-104: Authentication Dependencies
- **Description**: Create FastAPI dependencies for extracting and validating current user from JWT token.
- **Dependencies**: BE-102, BE-002
- **Acceptance Criteria**:
  - [ ] get_current_user dependency - extracts user from Bearer token
  - [ ] get_current_active_user dependency - ensures user is_active=True
  - [ ] get_current_admin dependency - ensures user role is ADMIN
  - [ ] HTTPBearer security scheme configured
  - [ ] Proper 401/403 responses for invalid/missing tokens
  - [ ] User object includes company and discipline relationships
  - [ ] Unit tests for dependencies
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-105: Role-Based Access Control (RBAC) Service
- **Description**: Implement RBAC service and decorators for endpoint authorization based on user roles.
- **Dependencies**: BE-104, DB-003
- **Acceptance Criteria**:
  - [ ] Permission definitions for each role (as per PRD section 3.3)
  - [ ] require_roles(*roles) decorator for endpoints
  - [ ] require_permissions(*permissions) decorator for fine-grained control
  - [ ] check_permission(user, permission) -> bool service method
  - [ ] Discipline-based filtering for Technicians, Managers, Engineering
  - [ ] Company-type-based access for protocol workflows
  - [ ] 403 Forbidden response for unauthorized access
  - [ ] Unit tests for all role/permission combinations
- **Priority**: MVP
- **Complexity**: M (Medium)

---

### Epic 3: User Management

#### BE-200: Pydantic Schemas for Users
- **Description**: Create Pydantic schemas for user CRUD operations.
- **Dependencies**: BE-001, DB-003
- **Acceptance Criteria**:
  - [ ] UserCreate: email, password, name, company_id, role, discipline_id (optional)
  - [ ] UserUpdate: name, company_id, role, discipline_id, is_active (all optional)
  - [ ] UserResponse: id, email, name, company, role, discipline, signature_image_path, is_active, created_at
  - [ ] UserListResponse: list of UserResponse with pagination
  - [ ] UserFilter: company_id, role, discipline_id, is_active (for filtering)
  - [ ] Proper validation messages
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### BE-201: User Service
- **Description**: Implement business logic service for user management operations.
- **Dependencies**: BE-200, BE-101, BE-002, DB-102
- **Acceptance Criteria**:
  - [ ] create_user(data) - creates user with hashed password
  - [ ] get_user(id) - returns user or raises NotFound
  - [ ] get_users(filters, pagination) - returns paginated list
  - [ ] update_user(id, data) - updates user fields
  - [ ] deactivate_user(id) - sets is_active=False (soft delete)
  - [ ] reactivate_user(id) - sets is_active=True
  - [ ] Email uniqueness validation
  - [ ] Prevent self-deactivation
  - [ ] Audit logging for all operations
  - [ ] Unit tests for all service methods
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-202: User Endpoints
- **Description**: Implement REST API endpoints for user management (Admin only).
- **Dependencies**: BE-201, BE-104, BE-105
- **Acceptance Criteria**:
  - [ ] GET /api/v1/users - list users with filters (Admin only)
  - [ ] GET /api/v1/users/{id} - get single user (Admin only)
  - [ ] POST /api/v1/users - create user (Admin only)
  - [ ] PUT /api/v1/users/{id} - update user (Admin only)
  - [ ] DELETE /api/v1/users/{id} - deactivate user (Admin only)
  - [ ] POST /api/v1/users/{id}/reactivate - reactivate user (Admin only)
  - [ ] All endpoints require ADMIN role
  - [ ] Pagination on list endpoint
  - [ ] Integration tests for all endpoints
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-203: User Signature Upload
- **Description**: Implement endpoint for uploading user signature images.
- **Dependencies**: BE-202, BE-700 (Attachment Service)
- **Acceptance Criteria**:
  - [ ] POST /api/v1/users/{id}/signature - upload signature image
  - [ ] Accept JPG, PNG formats
  - [ ] Max file size 2MB
  - [ ] Image stored in cloud storage
  - [ ] Path saved to user.signature_image_path
  - [ ] Previous signature is not deleted (for audit)
  - [ ] Returns updated user with signature path
  - [ ] Admin only OR self (user can upload own signature)
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 4: Company Management

#### BE-300: Pydantic Schemas for Companies
- **Description**: Create Pydantic schemas for company CRUD operations.
- **Dependencies**: BE-001, DB-003
- **Acceptance Criteria**:
  - [ ] CompanyCreate: name, type, cnpj (optional), address (optional), phone (optional), email (optional)
  - [ ] CompanyUpdate: all fields optional
  - [ ] CompanyResponse: all fields plus id, is_active, created_at, updated_at
  - [ ] CompanyListResponse with pagination
  - [ ] CompanyFilter: type, is_active
  - [ ] CNPJ validation format (XX.XXX.XXX/XXXX-XX)
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### BE-301: Company Service
- **Description**: Implement business logic service for company management.
- **Dependencies**: BE-300, BE-002, DB-100
- **Acceptance Criteria**:
  - [ ] create_company(data) - creates company
  - [ ] get_company(id) - returns company or raises NotFound
  - [ ] get_companies(filters, pagination) - returns paginated list
  - [ ] update_company(id, data) - updates company
  - [ ] deactivate_company(id) - soft delete
  - [ ] Check for dependent users before deactivation
  - [ ] Audit logging for all operations
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-302: Company Endpoints
- **Description**: Implement REST API endpoints for company management.
- **Dependencies**: BE-301, BE-104, BE-105
- **Acceptance Criteria**:
  - [ ] GET /api/v1/companies - list companies (Admin only)
  - [ ] GET /api/v1/companies/{id} - get single company (Admin only)
  - [ ] POST /api/v1/companies - create company (Admin only)
  - [ ] PUT /api/v1/companies/{id} - update company (Admin only)
  - [ ] DELETE /api/v1/companies/{id} - deactivate company (Admin only)
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 5: Discipline Management

#### BE-310: Pydantic Schemas for Disciplines
- **Description**: Create Pydantic schemas for discipline CRUD operations.
- **Dependencies**: BE-001
- **Acceptance Criteria**:
  - [ ] DisciplineCreate: name, code (max 10), legend (max 5)
  - [ ] DisciplineUpdate: all fields optional
  - [ ] DisciplineResponse: all fields plus id, is_active, created_at
  - [ ] DisciplineListResponse with pagination
  - [ ] Code uniqueness validation message
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### BE-311: Discipline Service
- **Description**: Implement business logic service for discipline management.
- **Dependencies**: BE-310, BE-002, DB-101
- **Acceptance Criteria**:
  - [ ] Standard CRUD operations
  - [ ] Code uniqueness check
  - [ ] Check for dependent users/templates before deactivation
  - [ ] Audit logging
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### BE-312: Discipline Endpoints
- **Description**: Implement REST API endpoints for discipline management.
- **Dependencies**: BE-311, BE-104, BE-105
- **Acceptance Criteria**:
  - [ ] Full CRUD at /api/v1/disciplines
  - [ ] Admin only for create/update/delete
  - [ ] All authenticated users can list/view
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

---

### Epic 6: Project Hierarchy Management

#### BE-320: Pydantic Schemas for Projects
- **Description**: Create Pydantic schemas for project CRUD operations.
- **Dependencies**: BE-001
- **Acceptance Criteria**:
  - [ ] ProjectCreate: name, code, description, client_company_id, start_date, expected_end_date, participating_company_ids
  - [ ] ProjectUpdate: all fields optional
  - [ ] ProjectResponse: all fields plus id, client_company (nested), participating_companies, is_active, created_at
  - [ ] ProjectListResponse with pagination
  - [ ] ProjectFilter: client_company_id, is_active
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-321: Project Service
- **Description**: Implement business logic service for project management.
- **Dependencies**: BE-320, BE-002, DB-200, DB-201
- **Acceptance Criteria**:
  - [ ] CRUD operations with participating companies management
  - [ ] Code uniqueness check
  - [ ] Validate client_company_id is type CLIENTE
  - [ ] Validate participating companies are MONTADORA or GERENCIADORA
  - [ ] Check for dependent areas before deactivation
  - [ ] Audit logging
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-322: Project Endpoints
- **Description**: Implement REST API endpoints for project management.
- **Dependencies**: BE-321, BE-104, BE-105
- **Acceptance Criteria**:
  - [ ] Full CRUD at /api/v1/projects
  - [ ] Admin only
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-330: Area Schemas, Service, and Endpoints
- **Description**: Implement complete area management (schemas, service, endpoints).
- **Dependencies**: BE-322, DB-202
- **Acceptance Criteria**:
  - [ ] AreaCreate: project_id, name, code, description
  - [ ] AreaUpdate, AreaResponse, AreaListResponse
  - [ ] Full CRUD service with audit logging
  - [ ] Code unique within project
  - [ ] Check for dependent systems before deactivation
  - [ ] GET /api/v1/projects/{project_id}/areas - list areas in project
  - [ ] Full CRUD at /api/v1/areas
  - [ ] Admin only
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-340: System Schemas, Service, and Endpoints
- **Description**: Implement complete system management.
- **Dependencies**: BE-330, DB-203
- **Acceptance Criteria**:
  - [ ] SystemCreate: area_id, name, code, description
  - [ ] Full CRUD service with audit logging
  - [ ] Code unique within area
  - [ ] Check for dependent subsystems before deactivation
  - [ ] GET /api/v1/areas/{area_id}/systems - list systems in area
  - [ ] Full CRUD at /api/v1/systems
  - [ ] Admin only
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-350: Subsystem Schemas, Service, and Endpoints
- **Description**: Implement complete subsystem management.
- **Dependencies**: BE-340, DB-204
- **Acceptance Criteria**:
  - [ ] SubsystemCreate: system_id, name, code, description
  - [ ] Full CRUD service with audit logging
  - [ ] Code unique within system
  - [ ] Check for dependent equipment before deactivation
  - [ ] GET /api/v1/systems/{system_id}/subsystems
  - [ ] Full CRUD at /api/v1/subsystems
  - [ ] Admin only
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-360: Equipment Schemas, Service, and Endpoints
- **Description**: Implement complete equipment management.
- **Dependencies**: BE-350, BE-312, DB-205
- **Acceptance Criteria**:
  - [ ] EquipmentCreate: subsystem_id, discipline_id (optional), tag_id, application
  - [ ] tag_id globally unique validation
  - [ ] Full CRUD service with audit logging
  - [ ] GET /api/v1/subsystems/{subsystem_id}/equipment
  - [ ] GET /api/v1/equipment with filters (subsystem_id, discipline_id, tag_id search)
  - [ ] Full CRUD at /api/v1/equipment
  - [ ] Admin only
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 7: Protocol Templates

#### BE-400: Pydantic Schemas for Protocol Templates
- **Description**: Create Pydantic schemas for protocol template management.
- **Dependencies**: BE-001, DB-003
- **Acceptance Criteria**:
  - [ ] ProtocolTemplateCreate: type (C1/C2/C3/C4), code, description, discipline_id (required for C1)
  - [ ] ProtocolTemplateUpdate: code, description (only if no derived protocols)
  - [ ] ProtocolTemplateResponse: all fields plus items count, version, is_active
  - [ ] ProtocolTemplateListResponse with pagination
  - [ ] ProtocolTemplateFilter: type, discipline_id, is_active
  - [ ] Validation: discipline_id required when type=C1
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-401: Pydantic Schemas for Template Items
- **Description**: Create Pydantic schemas for protocol template item management.
- **Dependencies**: BE-400, DB-003
- **Acceptance Criteria**:
  - [ ] TemplateItemCreate: code, description, help_text, order, item_type, config, required
  - [ ] TemplateItemUpdate: all fields optional
  - [ ] TemplateItemResponse: all fields plus id
  - [ ] TemplateItemReorder: list of {id, order} for bulk reordering
  - [ ] Config validation based on item_type:
    - NUMBER: {unit, min, max}
    - NUMBER_RANGE: {label_1, label_2, unit}
    - CHECKBOX_OK_NOK_NA: {options} (optional, defaults to [OK, NOK, N/A])
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-402: Protocol Template Service
- **Description**: Implement business logic service for protocol template management.
- **Dependencies**: BE-400, BE-401, BE-002, DB-300, DB-301
- **Acceptance Criteria**:
  - [ ] create_template(data) - creates template with validation
  - [ ] get_template(id) - returns template with items
  - [ ] get_templates(filters, pagination) - returns paginated list
  - [ ] update_template(id, data) - only if no derived protocols exist
  - [ ] deactivate_template(id) - soft delete
  - [ ] version_template(id) - creates new version when template has derived protocols
  - [ ] add_item(template_id, item_data) - adds item to template
  - [ ] update_item(item_id, data) - updates item
  - [ ] remove_item(item_id) - soft delete item
  - [ ] reorder_items(template_id, items) - bulk update order
  - [ ] get_template_usage_count(id) - count of derived protocols
  - [ ] Audit logging for all operations
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: M (Medium)

#### BE-403: Protocol Template Endpoints
- **Description**: Implement REST API endpoints for protocol template management.
- **Dependencies**: BE-402, BE-104, BE-105
- **Acceptance Criteria**:
  - [ ] GET /api/v1/protocol-templates - list templates with filters
  - [ ] GET /api/v1/protocol-templates/{id} - get template with items
  - [ ] POST /api/v1/protocol-templates - create template (Admin only)
  - [ ] PUT /api/v1/protocol-templates/{id} - update template (Admin only)
  - [ ] DELETE /api/v1/protocol-templates/{id} - deactivate template (Admin only)
  - [ ] POST /api/v1/protocol-templates/{id}/items - add item
  - [ ] PUT /api/v1/protocol-templates/{id}/items/{item_id} - update item
  - [ ] DELETE /api/v1/protocol-templates/{id}/items/{item_id} - remove item
  - [ ] PUT /api/v1/protocol-templates/{id}/items/reorder - bulk reorder
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: M (Medium)

---

### Epic 8: Protocols (C1 Flow Complete)

#### BE-500: Pydantic Schemas for Protocols
- **Description**: Create Pydantic schemas for protocol management.
- **Dependencies**: BE-001, DB-003
- **Acceptance Criteria**:
  - [ ] ProtocolCreate: template_id, equipment_company_assignments (list of {equipment_id, company_id})
  - [ ] ProtocolResponse: id, template, status, items, signatures, equipment_companies, created_at
  - [ ] ProtocolListResponse with pagination
  - [ ] ProtocolFilter: template_id, status, company_id, discipline_id, equipment_id, subsystem_id
  - [ ] ProtocolStatusUpdate: new_status, reason (for rejections)
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-501: Pydantic Schemas for Protocol Items
- **Description**: Create Pydantic schemas for protocol item values.
- **Dependencies**: BE-500
- **Acceptance Criteria**:
  - [ ] ProtocolItemUpdate: value (JSON), based on item type
  - [ ] ProtocolItemResponse: id, template_item, value, approval_status, filled_at, filled_by
  - [ ] ProtocolItemBulkUpdate: list of {item_id, value}
  - [ ] Value validation schemas per item type:
    - CHECKBOX_OK_NOK_NA: {selected: "OK"|"NOK"|"N/A"}
    - TEXT_SINGLE: {text: string}
    - TEXT_MULTI: {text: string}
    - NUMBER: {number: float}
    - NUMBER_RANGE: {value_1: float, value_2: float}
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-502: Protocol Service - Creation and Retrieval
- **Description**: Implement protocol creation and retrieval logic.
- **Dependencies**: BE-500, BE-501, BE-002, DB-400, DB-401, DB-402
- **Acceptance Criteria**:
  - [ ] create_protocol(data) - creates protocol from template, copies all items
  - [ ] get_protocol(id) - returns protocol with items, pendencies, signatures
  - [ ] get_protocols(filters, pagination) - filtered list based on user role/discipline
  - [ ] get_protocols_for_user(user) - applies role-based filtering automatically
  - [ ] Validate equipment_company assignments
  - [ ] Protocol inherits discipline from template (for C1)
  - [ ] Initial status: CREATED
  - [ ] Audit logging
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: M (Medium)

#### BE-503: Protocol Service - Item Filling
- **Description**: Implement protocol item filling logic for technicians.
- **Dependencies**: BE-502
- **Acceptance Criteria**:
  - [ ] fill_item(protocol_id, item_id, value, user) - fills single item
  - [ ] fill_items_bulk(protocol_id, items, user) - fills multiple items
  - [ ] Validate value against item type
  - [ ] Record filled_at, filled_by, filled_ip
  - [ ] Only allow filling for correct status and role
  - [ ] Partial save support (not all required items filled)
  - [ ] Audit logging
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: M (Medium)

#### BE-504: Protocol Status Machine - C1 Flow
- **Description**: Implement the complete C1 protocol state machine with all transitions.
- **Dependencies**: BE-502, DB-403
- **Acceptance Criteria**:
  - [ ] Implement all C1 states from PRD section 5.2
  - [ ] transition_status(protocol_id, new_status, user, reason) - validates and executes transition
  - [ ] Validate transition is allowed from current status
  - [ ] Validate user role and company type for transition
  - [ ] Record status change in protocol_status_history
  - [ ] submit_protocol(protocol_id, user) - CREATED -> EXECUTED_ASSEMBLER
  - [ ] approve_assembler(protocol_id, user) - EXECUTED_ASSEMBLER -> APPROVED_ASSEMBLER
  - [ ] reject_assembler(protocol_id, user, reason) - returns to CREATED, clears data
  - [ ] validate_engineering(protocol_id, user, pendency_level) - APPROVED_ASSEMBLER -> VALIDATED_ENGINEERING_*
  - [ ] approve_management(protocol_id, user, pendency_level) - VALIDATED_* -> APPROVED_MANAGEMENT_*
  - [ ] reject_management(protocol_id, user, reason) - returns to VALIDATED_*
  - [ ] approve_commissioning(protocol_id, user) - APPROVED_MANAGEMENT_* -> FINALIZED_*
  - [ ] reject_commissioning(protocol_id, user, reason) - returns to APPROVED_MANAGEMENT_*
  - [ ] Status reflects highest open pendency level
  - [ ] Audit logging for all transitions
  - [ ] Unit tests for all state transitions
- **Priority**: MVP
- **Complexity**: L (Large)

#### BE-505: Protocol Endpoints - CRUD
- **Description**: Implement REST API endpoints for protocol CRUD operations.
- **Dependencies**: BE-502, BE-104, BE-105
- **Acceptance Criteria**:
  - [ ] GET /api/v1/protocols - list protocols with role-based filtering
  - [ ] GET /api/v1/protocols/{id} - get protocol with full details
  - [ ] POST /api/v1/protocols - create protocol (Admin only)
  - [ ] DELETE /api/v1/protocols/{id} - archive protocol (Admin only)
  - [ ] GET /api/v1/protocols/{id}/history - get status history
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-506: Protocol Endpoints - Execution
- **Description**: Implement REST API endpoints for protocol execution (filling and status changes).
- **Dependencies**: BE-503, BE-504, BE-104, BE-105
- **Acceptance Criteria**:
  - [ ] PUT /api/v1/protocols/{id}/items/{item_id} - fill single item (Technician)
  - [ ] PUT /api/v1/protocols/{id}/items - bulk fill items (Technician)
  - [ ] POST /api/v1/protocols/{id}/submit - submit protocol (Technician)
  - [ ] POST /api/v1/protocols/{id}/approve - approve protocol (Manager/Engineering)
  - [ ] POST /api/v1/protocols/{id}/reject - reject protocol (Manager)
  - [ ] POST /api/v1/protocols/{id}/validate - validate protocol (Engineering)
  - [ ] POST /api/v1/protocols/{id}/finalize - finalize protocol (Commissioning Manager)
  - [ ] Role-based access control on all endpoints
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: M (Medium)

#### BE-507: Protocol Equipment Company Management
- **Description**: Implement endpoints for managing protocol-equipment-company relationships.
- **Dependencies**: BE-502
- **Acceptance Criteria**:
  - [ ] GET /api/v1/protocols/{id}/assignments - list equipment-company assignments
  - [ ] POST /api/v1/protocols/{id}/assignments - add assignment
  - [ ] DELETE /api/v1/protocols/{id}/assignments/{assignment_id} - remove assignment
  - [ ] Filtering by company, discipline, subsystem, system
  - [ ] Admin only
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 9: Pendencies

#### BE-600: Pydantic Schemas for Pendencies
- **Description**: Create Pydantic schemas for pendency management.
- **Dependencies**: BE-001, DB-003
- **Acceptance Criteria**:
  - [ ] PendencyCreate: protocol_item_id, type (PA/PB/PC), description
  - [ ] PendencyUpdate: type (for level change), description
  - [ ] PendencyResolve: resolution_description
  - [ ] PendencyResponse: id, protocol_item, type, status, description, created_by, created_at, resolved_by, resolved_at, resolution_description
  - [ ] PendencyListResponse with pagination
  - [ ] PendencyFilter: protocol_id, type, status
  - [ ] PendencyComment: comment text for history
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-601: Pendency Service
- **Description**: Implement business logic service for pendency management.
- **Dependencies**: BE-600, BE-002, DB-500, DB-501
- **Acceptance Criteria**:
  - [ ] create_pendency(data, user) - creates pendency, records IP
  - [ ] get_pendency(id) - returns pendency with history
  - [ ] get_pendencies(filters, pagination) - filtered list
  - [ ] get_pendencies_by_protocol(protocol_id) - all pendencies for protocol
  - [ ] change_level(pendency_id, new_type, user) - changes PA/PB/PC level
  - [ ] add_comment(pendency_id, comment, user) - adds comment to history
  - [ ] resolve_pendency(pendency_id, resolution, user) - marks as resolved
  - [ ] Cannot delete pendencies (per business rule)
  - [ ] Cannot reopen resolved pendencies
  - [ ] Record all changes in pendency_history
  - [ ] Audit logging
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: M (Medium)

#### BE-602: Pendency Endpoints
- **Description**: Implement REST API endpoints for pendency management.
- **Dependencies**: BE-601, BE-104, BE-105
- **Acceptance Criteria**:
  - [ ] GET /api/v1/pendencies - list pendencies with filters
  - [ ] GET /api/v1/pendencies/{id} - get pendency with history
  - [ ] GET /api/v1/protocols/{protocol_id}/pendencies - pendencies for protocol
  - [ ] POST /api/v1/pendencies - create pendency (Technician C2+, Manager, Engineering)
  - [ ] PUT /api/v1/pendencies/{id}/level - change level (Manager)
  - [ ] POST /api/v1/pendencies/{id}/comments - add comment
  - [ ] POST /api/v1/pendencies/{id}/resolve - resolve pendency (Manager)
  - [ ] No DELETE endpoint
  - [ ] Role-based access control
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-603: Pendency Impact on Protocol Status
- **Description**: Implement logic to update protocol status based on open pendencies.
- **Dependencies**: BE-601, BE-504
- **Acceptance Criteria**:
  - [ ] When pendency is created, update protocol final status suffix (PA/PB/PC)
  - [ ] When pendency is resolved, recalculate protocol status suffix
  - [ ] Highest severity wins (PA > PB > PC)
  - [ ] No open pendencies = no suffix (FINALIZED vs FINALIZED_PA)
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 10: Signatures

#### BE-700: Attachment Service
- **Description**: Implement service for file uploads to cloud storage.
- **Dependencies**: BE-002, DB-600
- **Acceptance Criteria**:
  - [ ] upload_file(file, entity_type, entity_id, user) - uploads to cloud storage
  - [ ] get_attachment(id) - returns attachment metadata
  - [ ] get_attachments(entity_type, entity_id) - returns all attachments for entity
  - [ ] delete_attachment(id) - soft delete
  - [ ] generate_presigned_url(attachment_id) - for direct download
  - [ ] Support S3/GCS/Azure Blob (configurable)
  - [ ] File type validation (JPG, PNG, PDF)
  - [ ] File size validation (max 10MB)
  - [ ] Image compression for large images
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: M (Medium)

#### BE-701: Attachment Endpoints
- **Description**: Implement REST API endpoints for file attachments.
- **Dependencies**: BE-700, BE-104
- **Acceptance Criteria**:
  - [ ] POST /api/v1/attachments - upload file with entity_type and entity_id
  - [ ] GET /api/v1/attachments/{id} - get attachment metadata
  - [ ] GET /api/v1/attachments/{id}/download - get presigned download URL
  - [ ] GET /api/v1/attachments?entity_type=X&entity_id=Y - list attachments
  - [ ] DELETE /api/v1/attachments/{id} - soft delete
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-702: Protocol Signature Service
- **Description**: Implement service for recording digital signatures on protocols.
- **Dependencies**: BE-502, BE-002, DB-700
- **Acceptance Criteria**:
  - [ ] sign_protocol(protocol_id, user, action) - records signature
  - [ ] Copy user's signature_image_path to protocol_signatures
  - [ ] Record signed_at, ip_address, action
  - [ ] get_signatures(protocol_id) - returns all signatures for protocol
  - [ ] Validate user has signature image uploaded
  - [ ] Signatures are immutable
  - [ ] Audit logging
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-703: Signature Integration with Protocol Flow
- **Description**: Integrate signature recording into protocol approval/validation/finalization flow.
- **Dependencies**: BE-702, BE-504
- **Acceptance Criteria**:
  - [ ] Auto-sign when Manager approves protocol
  - [ ] Auto-sign when Engineering validates protocol
  - [ ] Auto-sign when Commissioning Manager finalizes protocol
  - [ ] Require signature image before allowing these actions
  - [ ] Signatures visible in protocol response
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 11: Audit Endpoints

#### BE-800: Audit Log Service
- **Description**: Implement service for querying audit logs.
- **Dependencies**: BE-002, DB-800
- **Acceptance Criteria**:
  - [ ] get_audit_logs(filters, pagination) - filtered, paginated query
  - [ ] get_audit_logs_for_entity(entity_type, entity_id) - all logs for entity
  - [ ] get_audit_logs_for_user(user_id, date_range) - all logs by user
  - [ ] export_audit_logs(filters, format) - export to CSV/JSON
  - [ ] No create/update/delete operations (logs are immutable)
  - [ ] Unit tests
- **Priority**: MVP
- **Complexity**: S (Small)

#### BE-801: Audit Log Endpoints
- **Description**: Implement REST API endpoints for querying audit logs.
- **Dependencies**: BE-800, BE-104, BE-105
- **Acceptance Criteria**:
  - [ ] GET /api/v1/audit-logs - list logs with filters (Admin only)
  - [ ] GET /api/v1/audit-logs/entity/{type}/{id} - logs for entity (Admin only)
  - [ ] GET /api/v1/audit-logs/export - export logs (Admin only)
  - [ ] Filters: user_id, entity_type, entity_id, action, date_from, date_to
  - [ ] Pagination required
  - [ ] Integration tests
- **Priority**: MVP
- **Complexity**: S (Small)

---

## Priority: Post-MVP (Should Have)

### Epic 12: Data Import

#### BE-900: Import Service - Base
- **Description**: Implement base import service for Excel/CSV file processing.
- **Dependencies**: BE-002, DB-900
- **Acceptance Criteria**:
  - [ ] upload_import_file(file, entity_type, user) - creates import job
  - [ ] process_import(job_id) - processes import asynchronously
  - [ ] get_import_job(job_id) - returns job status and results
  - [ ] Support XLSX and CSV formats
  - [ ] Validate file structure before processing
  - [ ] Track progress (total_rows, processed_rows, success_rows, error_rows)
  - [ ] Store error details per row
  - [ ] Store original file for audit
  - [ ] Unit tests
- **Priority**: Post-MVP
- **Complexity**: M (Medium)

#### BE-901: Import Service - Entity Importers
- **Description**: Implement specific importers for each entity type.
- **Dependencies**: BE-900
- **Acceptance Criteria**:
  - [ ] CompanyImporter - import companies from template
  - [ ] DisciplineImporter - import disciplines
  - [ ] ProjectImporter - import projects with company associations
  - [ ] AreaImporter - import areas with project reference
  - [ ] SystemImporter - import systems with area reference
  - [ ] SubsystemImporter - import subsystems with system reference
  - [ ] EquipmentImporter - import equipment with subsystem/discipline reference
  - [ ] ProtocolTemplateImporter - import templates with items
  - [ ] Each importer validates required fields
  - [ ] Each importer handles relationships
  - [ ] Unit tests for each importer
- **Priority**: Post-MVP
- **Complexity**: L (Large)

#### BE-902: Import Template Generation
- **Description**: Implement service to generate import template files.
- **Dependencies**: BE-900
- **Acceptance Criteria**:
  - [ ] generate_template(entity_type, format) - returns XLSX or CSV template
  - [ ] Templates include all required fields
  - [ ] Templates include field descriptions/examples
  - [ ] Templates include validation rules in comments
  - [ ] Unit tests
- **Priority**: Post-MVP
- **Complexity**: S (Small)

#### BE-903: Import Endpoints
- **Description**: Implement REST API endpoints for data import.
- **Dependencies**: BE-900, BE-901, BE-902
- **Acceptance Criteria**:
  - [ ] POST /api/v1/imports/{entity_type} - upload import file
  - [ ] GET /api/v1/imports/{job_id} - get import job status
  - [ ] GET /api/v1/imports/{job_id}/errors - get detailed errors
  - [ ] GET /api/v1/imports/templates/{entity_type} - download import template
  - [ ] Admin only
  - [ ] Integration tests
- **Priority**: Post-MVP
- **Complexity**: S (Small)

---

### Epic 13: PDF Export

#### BE-910: PDF Generation Service
- **Description**: Implement service to generate PDF documents from protocols.
- **Dependencies**: BE-502
- **Acceptance Criteria**:
  - [ ] generate_protocol_pdf(protocol_id) - generates PDF
  - [ ] PDF includes header with project/company info
  - [ ] PDF includes all items with values
  - [ ] PDF includes photos as embedded images
  - [ ] PDF includes signatures rendered visually
  - [ ] PDF includes pendency list
  - [ ] PDF includes status history
  - [ ] PDF includes generation timestamp and user
  - [ ] Use WeasyPrint or ReportLab
  - [ ] Unit tests
- **Priority**: Post-MVP
- **Complexity**: M (Medium)

#### BE-911: PDF Export Endpoint
- **Description**: Implement REST API endpoint for PDF export.
- **Dependencies**: BE-910
- **Acceptance Criteria**:
  - [ ] GET /api/v1/protocols/{id}/pdf - download protocol as PDF
  - [ ] Returns PDF file directly
  - [ ] Caching for repeated requests
  - [ ] Integration tests
- **Priority**: Post-MVP
- **Complexity**: XS (Extra Small)

---

## Priority: Phase 2 (Could Have)

### Epic 14: C2/C3/C4 Protocol Flows

#### BE-1000: Protocol Status Machine - C2/C3/C4 Flows
- **Description**: Implement state machines for C2, C3, and C4 protocol types.
- **Dependencies**: BE-504
- **Acceptance Criteria**:
  - [ ] Implement all C2/C3/C4 states from PRD section 5.4
  - [ ] C2/C3/C4 flow: CREATED -> STARTED -> EXECUTED -> APPROVED_MANAGER_* -> FINALIZED_*
  - [ ] Client approval step
  - [ ] Supplier integration (optional step)
  - [ ] Rejection flows
  - [ ] Unit tests
- **Priority**: Phase 2
- **Complexity**: M (Medium)

#### BE-1001: Protocol Dependency Validation
- **Description**: Implement dependency checks between protocol types (C3 requires C2, C4 requires C3).
- **Dependencies**: BE-1000
- **Acceptance Criteria**:
  - [ ] C3 can only be created when all C2 in subsystem have no PA pendencies
  - [ ] C4 can only be created when all C3 in subsystem have no PA pendencies
  - [ ] Validation service for checking dependencies
  - [ ] Clear error messages when dependencies not met
  - [ ] Unit tests
- **Priority**: Phase 2
- **Complexity**: S (Small)

---

### Epic 15: Offline Sync Support

#### BE-1100: Sync API Endpoints
- **Description**: Implement API endpoints for offline data synchronization.
- **Dependencies**: BE-502, DB-1000
- **Acceptance Criteria**:
  - [ ] GET /api/v1/sync/data - download user's assigned protocols and related data
  - [ ] POST /api/v1/sync/push - push offline changes
  - [ ] POST /api/v1/sync/pull - get changes since last sync
  - [ ] Conflict detection based on timestamps
  - [ ] Return conflict details for manual resolution
  - [ ] Integration tests
- **Priority**: Phase 2
- **Complexity**: L (Large)

#### BE-1101: Conflict Resolution Service
- **Description**: Implement service for detecting and resolving sync conflicts.
- **Dependencies**: BE-1100
- **Acceptance Criteria**:
  - [ ] detect_conflicts(local_data, server_data) - identifies conflicts
  - [ ] auto_merge(conflicts) - merges non-conflicting field changes
  - [ ] mark_for_resolution(conflicts) - flags conflicts needing manual resolution
  - [ ] resolve_conflict(conflict_id, resolution) - applies user's resolution choice
  - [ ] Unit tests
- **Priority**: Phase 2
- **Complexity**: M (Medium)

---

## Dependency Graph

```
BE-001 (Project Setup)
  |
  +-- BE-002 (DB Session) -- requires DB-001
  |     |
  +-- BE-003 (Error Handling)
  |     |
  +-- BE-004 (Audit Middleware) -- requires DB-800
  |
  +-- BE-100 (Auth Schemas)
  |     |
  |     +-- BE-101 (Password Service)
  |     |
  |     +-- BE-102 (JWT Service)
  |           |
  |           +-- BE-103 (Auth Endpoints) -- requires DB-102, DB-103
  |           |
  |           +-- BE-104 (Auth Dependencies)
  |                 |
  |                 +-- BE-105 (RBAC Service)
  |
  +-- BE-200 (User Schemas)
        |
        +-- BE-201 (User Service) -- requires DB-102
              |
              +-- BE-202 (User Endpoints)
              |
              +-- BE-203 (Signature Upload) -- requires BE-700

Company/Discipline/Project Hierarchy (all depend on BE-104, BE-105):
BE-300 -> BE-301 -> BE-302 (Company)
BE-310 -> BE-311 -> BE-312 (Discipline)
BE-320 -> BE-321 -> BE-322 (Project) -> BE-330 (Area) -> BE-340 (System) -> BE-350 (Subsystem) -> BE-360 (Equipment)

Templates (depends on BE-312):
BE-400 -> BE-401 -> BE-402 -> BE-403

Protocols (depends on BE-402):
BE-500 -> BE-501 -> BE-502 -> BE-503 -> BE-504 -> BE-505, BE-506, BE-507

Pendencies (depends on BE-502):
BE-600 -> BE-601 -> BE-602 -> BE-603

Attachments/Signatures (depends on BE-002):
BE-700 -> BE-701
BE-702 -> BE-703 (integrates with BE-504)

Audit:
BE-800 -> BE-801

Post-MVP Import:
BE-900 -> BE-901 -> BE-902 -> BE-903

Post-MVP PDF:
BE-910 -> BE-911

Phase 2:
BE-1000 -> BE-1001
BE-1100 -> BE-1101
```

---

## Suggested Implementation Order

### Sprint 1: Infrastructure (Days 1-4)
1. BE-001: Project Structure
2. BE-002: Database Session
3. BE-003: Error Handling
4. BE-100: Auth Schemas
5. BE-101: Password Service
6. BE-102: JWT Service

### Sprint 2: Authentication (Days 5-8)
7. BE-103: Auth Endpoints
8. BE-104: Auth Dependencies
9. BE-105: RBAC Service
10. BE-004: Audit Middleware

### Sprint 3: User Management (Days 9-11)
11. BE-200: User Schemas
12. BE-201: User Service
13. BE-202: User Endpoints

### Sprint 4: Company/Discipline (Days 12-14)
14. BE-300, BE-301, BE-302: Company
15. BE-310, BE-311, BE-312: Discipline

### Sprint 5: Project Hierarchy (Days 15-19)
16. BE-320, BE-321, BE-322: Project
17. BE-330: Area
18. BE-340: System
19. BE-350: Subsystem
20. BE-360: Equipment

### Sprint 6: Templates (Days 20-23)
21. BE-400, BE-401: Template Schemas
22. BE-402: Template Service
23. BE-403: Template Endpoints

### Sprint 7: Protocols - Part 1 (Days 24-28)
24. BE-500, BE-501: Protocol Schemas
25. BE-502: Protocol Service (CRUD)
26. BE-503: Protocol Item Filling
27. BE-504: Protocol Status Machine

### Sprint 8: Protocols - Part 2 (Days 29-32)
28. BE-505: Protocol CRUD Endpoints
29. BE-506: Protocol Execution Endpoints
30. BE-507: Equipment Company Management

### Sprint 9: Pendencies (Days 33-36)
31. BE-600: Pendency Schemas
32. BE-601: Pendency Service
33. BE-602: Pendency Endpoints
34. BE-603: Pendency Impact

### Sprint 10: Signatures & Attachments (Days 37-40)
35. BE-700: Attachment Service
36. BE-701: Attachment Endpoints
37. BE-702: Signature Service
38. BE-703: Signature Integration
39. BE-203: User Signature Upload

### Sprint 11: Audit & Testing (Days 41-46)
40. BE-800: Audit Service
41. BE-801: Audit Endpoints
42. Integration testing
43. Performance testing
44. Security review

---

## Risk Considerations

1. **Complex State Machine**: The C1 flow has many states and transitions - consider using a state machine library
2. **RBAC Complexity**: Role/discipline/company filtering logic is complex - write comprehensive tests
3. **File Upload Performance**: Large images may slow API - implement background compression
4. **Audit Log Volume**: High write volume to audit_logs - consider async writes
5. **JWT Security**: Ensure proper token validation and revocation on logout

---

**End of Backend Tasks Document**
