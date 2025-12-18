# ProtoWorks System (PWS) - Frontend Tasks

**Version:** 1.0
**Date:** December 17, 2025
**Scope:** React components, pages, state management, PWA features

---

## Overview

This document contains all frontend tasks for the PWS project. Tasks are organized by module and priority. Frontend tasks depend on the corresponding backend endpoints being available.

---

## Task Categories

- **FE-0XX**: Core infrastructure, configuration, and shared components
- **FE-1XX**: Authentication and session management
- **FE-2XX**: Layout, navigation, and common UI
- **FE-3XX**: Administration module (users, imports)
- **FE-4XX**: CRUD modules (Companies, Projects, hierarchy)
- **FE-5XX**: Protocol templates
- **FE-6XX**: Protocols and execution
- **FE-7XX**: Pendencies
- **FE-8XX**: Audit and reporting
- **FE-9XX**: PWA and offline features

---

## Priority: MVP (Must Have)

### Epic 1: Project Infrastructure

#### FE-001: Project Setup and Configuration
- **Description**: Initialize React project with Vite, TypeScript, and essential configurations.
- **Dependencies**: None
- **Acceptance Criteria**:
  - [ ] Create React project using Vite with TypeScript template
  - [ ] Configure ESLint and Prettier for code quality
  - [ ] Set up path aliases (@/components, @/pages, @/hooks, etc.)
  - [ ] Configure environment variables (.env for API URL)
  - [ ] Set up project folder structure per PRD section 8.3
  - [ ] Add README with setup instructions
  - [ ] Configure Husky for pre-commit hooks
  - [ ] Add .editorconfig for consistent formatting
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-002: Install and Configure UI Component Library
- **Description**: Set up Material-UI (MUI) or Ant Design as the primary UI component library.
- **Dependencies**: FE-001
- **Acceptance Criteria**:
  - [ ] Install chosen UI library (recommend MUI for React)
  - [ ] Configure theme with PWS brand colors
  - [ ] Set up dark/light mode support (optional for MVP)
  - [ ] Configure Portuguese (pt-BR) locale for date/number formatting
  - [ ] Create theme file with custom overrides
  - [ ] Add global CSS reset
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-003: Configure State Management
- **Description**: Set up Zustand for global state management with TypeScript support.
- **Dependencies**: FE-001
- **Acceptance Criteria**:
  - [ ] Install Zustand
  - [ ] Create store structure (auth, protocols, ui, sync)
  - [ ] Set up persistence middleware for auth state
  - [ ] Create TypeScript types for all store states
  - [ ] Add devtools for development debugging
  - [ ] Document store usage patterns
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-004: Configure API Client
- **Description**: Set up Axios or TanStack Query for API communication with interceptors.
- **Dependencies**: FE-001, FE-003
- **Acceptance Criteria**:
  - [ ] Install Axios and TanStack Query
  - [ ] Create API client instance with base URL from env
  - [ ] Configure request interceptor to add JWT token
  - [ ] Configure response interceptor for token refresh
  - [ ] Handle 401 errors with redirect to login
  - [ ] Handle network errors gracefully
  - [ ] Create typed API service functions for each endpoint
  - [ ] Add request/response logging in development
- **Priority**: MVP
- **Complexity**: M (Medium)

#### FE-005: Configure React Router
- **Description**: Set up React Router with route protection and lazy loading.
- **Dependencies**: FE-001, FE-003
- **Acceptance Criteria**:
  - [ ] Install React Router v6
  - [ ] Define route structure matching PRD pages
  - [ ] Create ProtectedRoute component for auth-required pages
  - [ ] Create RoleBasedRoute component for role-restricted pages
  - [ ] Implement lazy loading for all page components
  - [ ] Add 404 Not Found page
  - [ ] Add loading fallback for lazy components
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-006: Configure React Hook Form
- **Description**: Set up React Hook Form with Zod validation for form management.
- **Dependencies**: FE-001
- **Acceptance Criteria**:
  - [ ] Install React Hook Form and Zod
  - [ ] Create reusable form field components (TextField, Select, Checkbox, etc.)
  - [ ] Create validation schema patterns for common fields (email, password, CNPJ)
  - [ ] Integrate with MUI/AntD form components
  - [ ] Add error display components
  - [ ] Document form component usage
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 2: Shared Components

#### FE-010: Create Page Layout Components
- **Description**: Create the main layout structure with header, sidebar, and content area.
- **Dependencies**: FE-002, FE-005
- **Acceptance Criteria**:
  - [ ] MainLayout component with sidebar and header
  - [ ] Sidebar with navigation menu (collapsible)
  - [ ] Header with user info and logout button
  - [ ] Breadcrumb component
  - [ ] Page container with consistent padding
  - [ ] Responsive behavior for tablet screens
  - [ ] Loading overlay component
- **Priority**: MVP
- **Complexity**: M (Medium)

#### FE-011: Create Data Table Component
- **Description**: Create a reusable data table component with sorting, filtering, and pagination.
- **Dependencies**: FE-002
- **Acceptance Criteria**:
  - [ ] Support for column definitions with custom renderers
  - [ ] Server-side pagination support
  - [ ] Column sorting (single and multi)
  - [ ] Row selection (single and multi)
  - [ ] Row actions (edit, delete, view)
  - [ ] Loading and empty states
  - [ ] Responsive behavior (horizontal scroll on mobile)
  - [ ] Export to CSV option
- **Priority**: MVP
- **Complexity**: M (Medium)

#### FE-012: Create Form Components
- **Description**: Create reusable form input components integrated with React Hook Form.
- **Dependencies**: FE-006, FE-002
- **Acceptance Criteria**:
  - [ ] FormTextField - text input with label and error
  - [ ] FormSelect - dropdown with options
  - [ ] FormMultiSelect - multi-select dropdown
  - [ ] FormDatePicker - date selection
  - [ ] FormCheckbox - checkbox with label
  - [ ] FormRadioGroup - radio button group
  - [ ] FormFileUpload - file upload with preview
  - [ ] FormTextArea - multiline text input
  - [ ] All components support validation and error messages
  - [ ] All components are accessible (ARIA)
- **Priority**: MVP
- **Complexity**: M (Medium)

#### FE-013: Create Dialog/Modal Components
- **Description**: Create reusable modal components for confirmations and forms.
- **Dependencies**: FE-002
- **Acceptance Criteria**:
  - [ ] ConfirmDialog - yes/no confirmation
  - [ ] FormDialog - modal with form content
  - [ ] AlertDialog - informational message
  - [ ] DeleteConfirmDialog - deletion confirmation with entity name
  - [ ] Support for custom buttons and actions
  - [ ] Keyboard navigation (Escape to close)
  - [ ] Focus trap within modal
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-014: Create Feedback Components
- **Description**: Create components for user feedback (toasts, alerts, loading).
- **Dependencies**: FE-002
- **Acceptance Criteria**:
  - [ ] Toast/Snackbar notification system
  - [ ] Alert banner component (success, warning, error, info)
  - [ ] Loading spinner component
  - [ ] Skeleton loading components for cards and lists
  - [ ] Empty state component with illustration
  - [ ] Error boundary component with retry
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-015: Create Filter Components
- **Description**: Create reusable filter components for list pages.
- **Dependencies**: FE-012
- **Acceptance Criteria**:
  - [ ] FilterBar component with multiple filters
  - [ ] Filter chips showing active filters
  - [ ] Clear all filters button
  - [ ] Filter presets (save/load filter combinations)
  - [ ] Date range filter
  - [ ] Search input with debounce
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 3: Authentication

#### FE-100: Create Auth Store
- **Description**: Implement authentication state management with Zustand.
- **Dependencies**: FE-003, BE-103 (Backend auth endpoints)
- **Acceptance Criteria**:
  - [ ] Store user data (id, email, name, role, company, discipline)
  - [ ] Store access token and refresh token
  - [ ] Persist tokens to localStorage
  - [ ] login(email, password) action
  - [ ] logout() action - clears tokens and state
  - [ ] refreshToken() action
  - [ ] isAuthenticated computed property
  - [ ] hasRole(role) helper function
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-101: Create Login Page
- **Description**: Implement the login page with email/password form.
- **Dependencies**: FE-100, FE-006, FE-002
- **Acceptance Criteria**:
  - [ ] Email field with validation
  - [ ] Password field with show/hide toggle
  - [ ] Submit button with loading state
  - [ ] Error message display for invalid credentials
  - [ ] Redirect to dashboard on successful login
  - [ ] Redirect to login if accessing protected route while unauthenticated
  - [ ] Remember me option (optional)
  - [ ] Responsive design for tablet/desktop
  - [ ] PWS logo and branding
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-102: Create Auth Guard Components
- **Description**: Create route protection components based on authentication and roles.
- **Dependencies**: FE-100, FE-005
- **Acceptance Criteria**:
  - [ ] ProtectedRoute - redirects to login if not authenticated
  - [ ] AdminRoute - allows only ADMIN role
  - [ ] RoleRoute - allows specified roles
  - [ ] Redirect to unauthorized page for role violations
  - [ ] Show loading while checking auth status
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-103: Implement Token Refresh Logic
- **Description**: Implement automatic token refresh before expiration.
- **Dependencies**: FE-100, FE-004
- **Acceptance Criteria**:
  - [ ] Check token expiration before each API call
  - [ ] Refresh token if expiring within 5 minutes
  - [ ] Queue requests during refresh
  - [ ] Logout if refresh fails
  - [ ] Background refresh interval (optional)
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 4: Dashboard

#### FE-110: Create Dashboard Page
- **Description**: Create the main dashboard page shown after login.
- **Dependencies**: FE-010, FE-100
- **Acceptance Criteria**:
  - [ ] Welcome message with user name
  - [ ] Summary cards (protocols by status, pending actions)
  - [ ] Quick action buttons based on user role
  - [ ] Recent activity list (last 10 items)
  - [ ] Navigation to main modules
  - [ ] Responsive layout
- **Priority**: MVP
- **Complexity**: M (Medium)

---

### Epic 5: User Management (Admin)

#### FE-200: Create User List Page
- **Description**: Create the user management list page for administrators.
- **Dependencies**: FE-011, FE-015, FE-102, BE-202 (Backend user endpoints)
- **Acceptance Criteria**:
  - [ ] Data table with columns: name, email, company, role, discipline, status
  - [ ] Filters: company, role, discipline, status
  - [ ] Search by name/email
  - [ ] Pagination
  - [ ] Actions: view, edit, deactivate/reactivate
  - [ ] Create new user button
  - [ ] Admin role required
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-201: Create User Form (Create/Edit)
- **Description**: Create the form for creating and editing users.
- **Dependencies**: FE-012, FE-013, BE-202
- **Acceptance Criteria**:
  - [ ] Fields: name, email, password (create only), company, role, discipline
  - [ ] Password field only shown for new users
  - [ ] Company dropdown populated from API
  - [ ] Role dropdown with all roles
  - [ ] Discipline dropdown (optional, filtered by company if applicable)
  - [ ] Validation for all required fields
  - [ ] Email uniqueness validated on blur
  - [ ] Success/error notifications
  - [ ] Modal or full page form
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-202: Create User Signature Upload
- **Description**: Create interface for uploading user signature images.
- **Dependencies**: FE-201, BE-203 (Backend signature upload)
- **Acceptance Criteria**:
  - [ ] Image upload field (JPG, PNG only)
  - [ ] Image preview before upload
  - [ ] Max file size validation (2MB)
  - [ ] Crop/resize tool (optional)
  - [ ] Current signature display if exists
  - [ ] Upload progress indicator
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 6: Company Management

#### FE-300: Create Company List Page
- **Description**: Create the company management list page.
- **Dependencies**: FE-011, FE-015, FE-102, BE-302 (Backend company endpoints)
- **Acceptance Criteria**:
  - [ ] Data table with columns: name, type, CNPJ, email, phone, status
  - [ ] Filters: type, status
  - [ ] Search by name
  - [ ] Pagination
  - [ ] Actions: view, edit, deactivate
  - [ ] Create new company button
  - [ ] Admin role required
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-301: Create Company Form
- **Description**: Create the form for creating and editing companies.
- **Dependencies**: FE-012, FE-013, BE-302
- **Acceptance Criteria**:
  - [ ] Fields: name, type (dropdown), CNPJ, address, phone, email
  - [ ] CNPJ mask and validation (XX.XXX.XXX/XXXX-XX)
  - [ ] Email and phone validation
  - [ ] Validation for required fields
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

---

### Epic 7: Discipline Management

#### FE-310: Create Discipline List Page
- **Description**: Create the discipline management list page.
- **Dependencies**: FE-011, FE-102, BE-312 (Backend discipline endpoints)
- **Acceptance Criteria**:
  - [ ] Data table with columns: name, code, legend, status
  - [ ] Search by name/code
  - [ ] Pagination
  - [ ] Actions: edit, deactivate
  - [ ] Create new discipline button
  - [ ] Admin role required
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### FE-311: Create Discipline Form
- **Description**: Create the form for creating and editing disciplines.
- **Dependencies**: FE-012, FE-013, BE-312
- **Acceptance Criteria**:
  - [ ] Fields: name, code (max 10), legend (max 5)
  - [ ] Code uniqueness validation
  - [ ] Validation for required fields
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

---

### Epic 8: Project Hierarchy Management

#### FE-320: Create Project List Page
- **Description**: Create the project management list page.
- **Dependencies**: FE-011, FE-015, FE-102, BE-322 (Backend project endpoints)
- **Acceptance Criteria**:
  - [ ] Data table with columns: name, code, client, start date, end date, status
  - [ ] Filters: client company, status
  - [ ] Search by name/code
  - [ ] Pagination
  - [ ] Actions: view details, edit, archive
  - [ ] Create new project button
  - [ ] Admin role required
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-321: Create Project Form
- **Description**: Create the form for creating and editing projects.
- **Dependencies**: FE-012, FE-013, BE-322
- **Acceptance Criteria**:
  - [ ] Fields: name, code, description, client company, start date, end date
  - [ ] Multi-select for participating companies (Montadora, Gerenciadora)
  - [ ] Client company dropdown (filtered by type=CLIENTE)
  - [ ] Date pickers for start/end dates
  - [ ] Code uniqueness validation
  - [ ] Validation for required fields
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-322: Create Project Detail Page
- **Description**: Create the project detail page with tabs for areas, systems, etc.
- **Dependencies**: FE-321, BE-330, BE-340, BE-350
- **Acceptance Criteria**:
  - [ ] Project header with basic info
  - [ ] Tab navigation: Areas, Systems, Subsystems, Equipment
  - [ ] Nested data display (area -> systems -> subsystems -> equipment)
  - [ ] Quick add buttons for each level
  - [ ] Tree view or accordion for hierarchy visualization
  - [ ] Breadcrumb navigation
- **Priority**: MVP
- **Complexity**: M (Medium)

#### FE-330: Create Area Form
- **Description**: Create the form for creating and editing areas within a project.
- **Dependencies**: FE-012, FE-013, BE-330
- **Acceptance Criteria**:
  - [ ] Fields: name, code, description
  - [ ] Project ID passed from parent context
  - [ ] Code uniqueness within project
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### FE-340: Create System Form
- **Description**: Create the form for creating and editing systems within an area.
- **Dependencies**: FE-012, FE-013, BE-340
- **Acceptance Criteria**:
  - [ ] Fields: name, code, description
  - [ ] Area ID passed from parent context
  - [ ] Code uniqueness within area
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### FE-350: Create Subsystem Form
- **Description**: Create the form for creating and editing subsystems within a system.
- **Dependencies**: FE-012, FE-013, BE-350
- **Acceptance Criteria**:
  - [ ] Fields: name, code, description
  - [ ] System ID passed from parent context
  - [ ] Code uniqueness within system
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### FE-360: Create Equipment Form
- **Description**: Create the form for creating and editing equipment within a subsystem.
- **Dependencies**: FE-012, FE-013, BE-360
- **Acceptance Criteria**:
  - [ ] Fields: tag_id, application, discipline
  - [ ] Subsystem ID passed from parent context
  - [ ] Discipline dropdown
  - [ ] tag_id uniqueness validation
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### FE-361: Create Equipment List Page
- **Description**: Create standalone equipment list with advanced filtering.
- **Dependencies**: FE-011, FE-015, BE-360
- **Acceptance Criteria**:
  - [ ] Data table with columns: tag_id, application, subsystem, system, area, project, discipline
  - [ ] Filters: project, area, system, subsystem, discipline
  - [ ] Search by tag_id
  - [ ] Pagination
  - [ ] Actions: view, edit
  - [ ] Hierarchical filter cascade (project -> area -> system -> subsystem)
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 9: Protocol Templates

#### FE-400: Create Protocol Template List Page
- **Description**: Create the protocol template management list page.
- **Dependencies**: FE-011, FE-015, FE-102, BE-403 (Backend template endpoints)
- **Acceptance Criteria**:
  - [ ] Data table with columns: code, type (C1/C2/C3/C4), discipline, items count, version, status
  - [ ] Filters: type, discipline, status
  - [ ] Search by code
  - [ ] Pagination
  - [ ] Actions: view/edit items, duplicate, deactivate
  - [ ] Create new template button
  - [ ] Admin role required
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-401: Create Protocol Template Form
- **Description**: Create the form for creating and editing protocol templates.
- **Dependencies**: FE-012, FE-013, BE-403
- **Acceptance Criteria**:
  - [ ] Fields: type (C1/C2/C3/C4), code, description, discipline
  - [ ] Discipline required when type=C1
  - [ ] Code uniqueness validation
  - [ ] Warning if template has derived protocols (edit restricted)
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-402: Create Template Item Editor
- **Description**: Create the interface for managing items within a template.
- **Dependencies**: FE-401, BE-403
- **Acceptance Criteria**:
  - [ ] List of template items with drag-and-drop reordering
  - [ ] Add new item button
  - [ ] Inline or modal editing of items
  - [ ] Item fields: code, description, help_text, item_type, config, required
  - [ ] Type-specific config UI:
    - NUMBER: unit, min, max fields
    - NUMBER_RANGE: label_1, label_2, unit fields
    - CHECKBOX_OK_NOK_NA: custom options (optional)
  - [ ] Delete item with confirmation
  - [ ] Preview of how item will appear to technician
  - [ ] Save all changes button
- **Priority**: MVP
- **Complexity**: L (Large)

#### FE-403: Create Template Item Type Components
- **Description**: Create preview/input components for each template item type.
- **Dependencies**: FE-002
- **Acceptance Criteria**:
  - [ ] CheckboxOkNokNaPreview - shows 3 radio buttons (OK/NOK/N/A)
  - [ ] TextSinglePreview - single line text input
  - [ ] TextMultiPreview - multiline textarea
  - [ ] NumberPreview - number input with unit
  - [ ] NumberRangePreview - two number inputs with labels
  - [ ] PhotoCapturePreview - placeholder for photo capture
  - [ ] DatePreview - date picker
  - [ ] SignaturePreview - signature pad placeholder
  - [ ] All components work in read-only preview mode
- **Priority**: MVP
- **Complexity**: M (Medium)

---

### Epic 10: Protocols

#### FE-500: Create Protocol List Page
- **Description**: Create the protocol list page with role-based filtering.
- **Dependencies**: FE-011, FE-015, FE-102, BE-505 (Backend protocol endpoints)
- **Acceptance Criteria**:
  - [ ] Data table with columns: ID, template code, type, status, discipline, equipment count, created date
  - [ ] Filters: template, type, status, discipline, company
  - [ ] Cascading filters for subsystem/system/area/project
  - [ ] Status displayed with color-coded badges
  - [ ] Actions based on user role and protocol status
  - [ ] Pagination
  - [ ] Role-based data filtering (users see only relevant protocols)
- **Priority**: MVP
- **Complexity**: M (Medium)

#### FE-501: Create Protocol Create Form
- **Description**: Create the form for creating new protocols from templates.
- **Dependencies**: FE-012, FE-013, BE-505
- **Acceptance Criteria**:
  - [ ] Template selection dropdown (filtered by type)
  - [ ] Equipment-company assignment interface
  - [ ] Multi-select for equipment
  - [ ] Company assignment per equipment
  - [ ] Import from spreadsheet option (future)
  - [ ] Preview of selected template items
  - [ ] Admin role required
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: M (Medium)

#### FE-502: Create Protocol Detail Page
- **Description**: Create the protocol detail page showing all information.
- **Dependencies**: FE-500, BE-505
- **Acceptance Criteria**:
  - [ ] Protocol header: template, status, created date, discipline
  - [ ] Equipment-company assignments list
  - [ ] Items list with current values
  - [ ] Pendencies section
  - [ ] Signatures section
  - [ ] Status history timeline
  - [ ] Actions based on user role and current status
  - [ ] Print/PDF button (post-MVP)
- **Priority**: MVP
- **Complexity**: M (Medium)

#### FE-503: Create Protocol Execution Page
- **Description**: Create the page for technicians to fill protocol items.
- **Dependencies**: FE-403, BE-506 (Backend execution endpoints)
- **Acceptance Criteria**:
  - [ ] Header with protocol info and status
  - [ ] Scrollable list of items to fill
  - [ ] Each item rendered based on its type using FE-403 components
  - [ ] Interactive inputs for each item type
  - [ ] Save draft button (partial save)
  - [ ] Submit button (changes status)
  - [ ] Validation before submit (all required items filled)
  - [ ] Progress indicator (X of Y items completed)
  - [ ] Optimized for tablet use
  - [ ] Auto-save drafts periodically
- **Priority**: MVP
- **Complexity**: L (Large)

#### FE-504: Create Protocol Item Input Components
- **Description**: Create interactive input components for protocol item filling.
- **Dependencies**: FE-403
- **Acceptance Criteria**:
  - [ ] CheckboxOkNokNaInput - 3 large touch-friendly radio buttons
  - [ ] TextSingleInput - text input with keyboard
  - [ ] TextMultiInput - expanding textarea
  - [ ] NumberInput - number pad on mobile, unit display
  - [ ] NumberRangeInput - two labeled number inputs
  - [ ] PhotoCaptureInput - camera access, gallery selection, preview (post-MVP)
  - [ ] DateInput - date picker
  - [ ] SignatureInput - signature pad (post-MVP)
  - [ ] All inputs optimized for tablet touch interaction
  - [ ] Clear visual feedback for filled vs empty
  - [ ] Add pendency button on text/number fields (per PRD requirement)
- **Priority**: MVP
- **Complexity**: L (Large)

#### FE-505: Create Protocol Approval Page
- **Description**: Create the page for managers to review and approve protocols.
- **Dependencies**: FE-502, BE-506
- **Acceptance Criteria**:
  - [ ] Protocol header with status and actions
  - [ ] Items list with filled values (read-only)
  - [ ] Approve/Reject buttons for each item (optional)
  - [ ] Bulk approve all items
  - [ ] Reject protocol with reason (required text)
  - [ ] Create pendency on any item
  - [ ] View existing pendencies
  - [ ] Submit approval (changes status)
  - [ ] Signature capture on approval
- **Priority**: MVP
- **Complexity**: M (Medium)

#### FE-506: Create Protocol Status Display Component
- **Description**: Create component to display protocol status with appropriate styling.
- **Dependencies**: FE-002
- **Acceptance Criteria**:
  - [ ] Color-coded status badges
  - [ ] Status descriptions in Portuguese
  - [ ] Icon for each status type
  - [ ] Tooltip with status meaning
  - [ ] Support for all C1 and C2/C3/C4 statuses
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### FE-507: Create Protocol Equipment Assignment Interface
- **Description**: Create interface for managing protocol-equipment-company assignments.
- **Dependencies**: FE-501, BE-507
- **Acceptance Criteria**:
  - [ ] List of current assignments
  - [ ] Add assignment modal
  - [ ] Equipment search/select
  - [ ] Company select
  - [ ] Remove assignment
  - [ ] Filters: discipline, subsystem
  - [ ] Bulk import from spreadsheet (post-MVP)
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 11: Pendencies

#### FE-600: Create Pendency List Page
- **Description**: Create the pendency list page with filtering.
- **Dependencies**: FE-011, FE-015, FE-102, BE-602 (Backend pendency endpoints)
- **Acceptance Criteria**:
  - [ ] Data table with columns: ID, protocol, item, type (PA/PB/PC), status, created by, created at
  - [ ] Filters: protocol, type, status
  - [ ] Color-coded type badges (PA=red, PB=yellow, PC=blue)
  - [ ] Search
  - [ ] Pagination
  - [ ] Actions: view, change level, resolve
  - [ ] Navigate to related protocol item
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-601: Create Pendency Form
- **Description**: Create the form for creating pendencies on protocol items.
- **Dependencies**: FE-012, FE-013, BE-602
- **Acceptance Criteria**:
  - [ ] Type selection (PA/PB/PC) with descriptions
  - [ ] Description textarea
  - [ ] Photo attachment (post-MVP)
  - [ ] Protocol item context displayed
  - [ ] Created from protocol execution/approval pages
  - [ ] Success/error notifications
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-602: Create Pendency Detail/Resolution Page
- **Description**: Create the page for viewing and resolving pendencies.
- **Dependencies**: FE-600, BE-602
- **Acceptance Criteria**:
  - [ ] Pendency details: type, description, created by, created at
  - [ ] Protocol item context
  - [ ] History timeline (level changes, comments)
  - [ ] Add comment form
  - [ ] Change level action (Manager only)
  - [ ] Resolve pendency form with description
  - [ ] View attachments
  - [ ] No delete option (per business rule)
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-603: Create Inline Pendency Indicator
- **Description**: Create component to show pendency status on protocol items.
- **Dependencies**: FE-002
- **Acceptance Criteria**:
  - [ ] Icon indicator on items with pendencies
  - [ ] Color based on highest severity (PA > PB > PC)
  - [ ] Click to view/manage pendencies
  - [ ] Count badge if multiple pendencies
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

---

### Epic 12: Signatures

#### FE-700: Create Signature Display Component
- **Description**: Create component to display digital signatures on protocols.
- **Dependencies**: FE-002
- **Acceptance Criteria**:
  - [ ] Signature image display
  - [ ] Signer name and role
  - [ ] Signature timestamp
  - [ ] Action taken (approved, validated, finalized)
  - [ ] Compact and expanded views
- **Priority**: MVP
- **Complexity**: XS (Extra Small)

#### FE-701: Create Signature Capture Component
- **Description**: Create component for capturing signatures during approval (post-MVP enhancement).
- **Dependencies**: FE-002
- **Acceptance Criteria**:
  - [ ] For MVP: Use pre-uploaded signature from user profile
  - [ ] Display user's registered signature
  - [ ] Confirmation dialog before signing
  - [ ] Loading state during signature submission
  - [ ] Success notification
- **Priority**: MVP
- **Complexity**: S (Small)

---

### Epic 13: Audit Logs (Admin)

#### FE-800: Create Audit Log Page
- **Description**: Create the audit log viewing page for administrators.
- **Dependencies**: FE-011, FE-015, FE-102, BE-801 (Backend audit endpoints)
- **Acceptance Criteria**:
  - [ ] Data table with columns: timestamp, user, action, entity type, entity ID, IP
  - [ ] Filters: user, entity type, action, date range
  - [ ] Search by entity ID
  - [ ] Pagination
  - [ ] Expandable rows to show data changes (before/after)
  - [ ] Export to CSV button
  - [ ] Admin role required
- **Priority**: MVP
- **Complexity**: S (Small)

#### FE-801: Create Entity Audit History Component
- **Description**: Create reusable component to show audit history for any entity.
- **Dependencies**: FE-800
- **Acceptance Criteria**:
  - [ ] Display audit log entries for specific entity
  - [ ] Timeline visualization
  - [ ] User and timestamp for each entry
  - [ ] Before/after data comparison
  - [ ] Usable in detail pages for any entity
- **Priority**: MVP
- **Complexity**: S (Small)

---

## Priority: Post-MVP (Should Have)

### Epic 14: Data Import

#### FE-900: Create Import Page
- **Description**: Create the data import page for administrators.
- **Dependencies**: FE-102, BE-903 (Backend import endpoints)
- **Acceptance Criteria**:
  - [ ] Entity type selection (companies, projects, areas, etc.)
  - [ ] Download template button for selected entity
  - [ ] File upload dropzone (XLSX, CSV)
  - [ ] Validation before upload
  - [ ] Upload progress indicator
  - [ ] Import job status display
  - [ ] Error report with row-by-row details
  - [ ] Re-upload corrected file
  - [ ] Admin role required
- **Priority**: Post-MVP
- **Complexity**: M (Medium)

#### FE-901: Create Import History Page
- **Description**: Create page to view past import jobs and their results.
- **Dependencies**: FE-900
- **Acceptance Criteria**:
  - [ ] List of import jobs with status
  - [ ] Filter by entity type, status, date
  - [ ] View detailed results for each job
  - [ ] Download error report
  - [ ] Download original file
- **Priority**: Post-MVP
- **Complexity**: S (Small)

---

### Epic 15: PDF Export

#### FE-910: Create Protocol PDF Preview
- **Description**: Create in-browser PDF preview/download for protocols.
- **Dependencies**: FE-502, BE-911 (Backend PDF endpoint)
- **Acceptance Criteria**:
  - [ ] PDF preview in modal or new tab
  - [ ] Download PDF button
  - [ ] Print button
  - [ ] Loading state during generation
  - [ ] Error handling
- **Priority**: Post-MVP
- **Complexity**: S (Small)

---

### Epic 16: Enhanced Photo Features

#### FE-920: Create Photo Capture Component
- **Description**: Create component for capturing photos on protocol items.
- **Dependencies**: FE-504
- **Acceptance Criteria**:
  - [ ] Camera access on tablet devices
  - [ ] Gallery/file selection fallback
  - [ ] Image preview before save
  - [ ] Multiple photos per item
  - [ ] Photo compression before upload
  - [ ] Upload progress indicator
  - [ ] View/zoom photos in gallery
  - [ ] Delete photos
- **Priority**: Post-MVP
- **Complexity**: M (Medium)

---

## Priority: Phase 2 (Could Have)

### Epic 17: PWA and Offline Features

#### FE-1000: PWA Configuration
- **Description**: Configure Progressive Web App capabilities.
- **Dependencies**: FE-001
- **Acceptance Criteria**:
  - [ ] Web manifest configured (icons, name, display mode)
  - [ ] Service worker registered
  - [ ] Install prompt on supported browsers
  - [ ] Offline fallback page
  - [ ] Cache static assets
  - [ ] App installable on tablets
- **Priority**: Phase 2
- **Complexity**: M (Medium)

#### FE-1001: Offline Data Store
- **Description**: Implement IndexedDB storage for offline data.
- **Dependencies**: FE-1000
- **Acceptance Criteria**:
  - [ ] Install Dexie.js for IndexedDB
  - [ ] Define offline data schema
  - [ ] Store user's assigned protocols
  - [ ] Store template items for rendering
  - [ ] Store protocol item values
  - [ ] Store pendencies
  - [ ] Automatic cleanup of old data
- **Priority**: Phase 2
- **Complexity**: M (Medium)

#### FE-1002: Offline Sync Queue
- **Description**: Implement queue for offline changes pending sync.
- **Dependencies**: FE-1001
- **Acceptance Criteria**:
  - [ ] Queue structure for offline operations
  - [ ] Add operations to queue when offline
  - [ ] Process queue when online
  - [ ] Retry failed operations
  - [ ] Conflict detection
  - [ ] User notification of sync status
- **Priority**: Phase 2
- **Complexity**: L (Large)

#### FE-1003: Offline Mode UI
- **Description**: Create UI indicators and controls for offline mode.
- **Dependencies**: FE-1002
- **Acceptance Criteria**:
  - [ ] Offline indicator in header
  - [ ] Manual sync button
  - [ ] Pending changes count
  - [ ] Sync progress indicator
  - [ ] Conflict resolution interface
  - [ ] Last sync timestamp
- **Priority**: Phase 2
- **Complexity**: M (Medium)

#### FE-1004: Offline Protocol Execution
- **Description**: Enable protocol filling while offline.
- **Dependencies**: FE-1001, FE-1002, FE-503
- **Acceptance Criteria**:
  - [ ] Load protocol from IndexedDB
  - [ ] Fill items while offline
  - [ ] Save to IndexedDB
  - [ ] Queue for server sync
  - [ ] Photo capture and storage offline
  - [ ] Clear offline mode indicator
- **Priority**: Phase 2
- **Complexity**: L (Large)

---

### Epic 18: C2/C3/C4 Protocol Flows

#### FE-1100: C2/C3/C4 Protocol Execution
- **Description**: Extend protocol execution for C2/C3/C4 flows.
- **Dependencies**: FE-503, BE-1000 (Backend C2/C3/C4)
- **Acceptance Criteria**:
  - [ ] Started status (partial fill)
  - [ ] Technician can create pendencies in C2/C3/C4
  - [ ] Supplier integration UI
  - [ ] Client approval UI
  - [ ] Dependency warnings (C3 requires C2, C4 requires C3)
- **Priority**: Phase 2
- **Complexity**: M (Medium)

---

### Epic 19: Dashboard Enhancements

#### FE-1200: Advanced Dashboard
- **Description**: Create advanced dashboard with charts and metrics.
- **Dependencies**: FE-110
- **Acceptance Criteria**:
  - [ ] Protocol status distribution chart
  - [ ] Protocols by discipline chart
  - [ ] Pending pendencies by type
  - [ ] Timeline of recent activity
  - [ ] KPI cards (completion rate, avg time)
  - [ ] Date range selector
  - [ ] Export dashboard data
- **Priority**: Phase 2
- **Complexity**: M (Medium)

---

## Dependency Graph

```
FE-001 (Project Setup)
  |
  +-- FE-002 (UI Library)
  |     |
  |     +-- FE-010 (Layout) ---------------+
  |     |                                   |
  |     +-- FE-011 (Data Table) -----------+
  |     |                                   |
  |     +-- FE-012 (Form Components) ------+-- FE-015 (Filters)
  |     |                                   |
  |     +-- FE-013 (Dialogs) --------------+
  |     |                                   |
  |     +-- FE-014 (Feedback) -------------+
  |
  +-- FE-003 (State Management)
  |     |
  |     +-- FE-100 (Auth Store)
  |           |
  |           +-- FE-101 (Login Page)
  |           |
  |           +-- FE-102 (Auth Guards)
  |           |
  |           +-- FE-103 (Token Refresh)
  |
  +-- FE-004 (API Client) -- requires FE-003, FE-103
  |
  +-- FE-005 (Router) -- requires FE-003
  |
  +-- FE-006 (Forms) -- requires FE-002

Dashboard (requires FE-010, FE-100):
FE-110

User Management (requires FE-011, FE-012, FE-102):
FE-200 -> FE-201 -> FE-202

Company/Discipline (requires FE-011, FE-012, FE-102):
FE-300 -> FE-301
FE-310 -> FE-311

Project Hierarchy (requires FE-011, FE-012, FE-102):
FE-320 -> FE-321 -> FE-322 -> FE-330 -> FE-340 -> FE-350 -> FE-360 -> FE-361

Templates (requires FE-011, FE-012, FE-102):
FE-400 -> FE-401 -> FE-402 -> FE-403

Protocols (requires FE-011, FE-403):
FE-500 -> FE-501 -> FE-502 -> FE-503 -> FE-504 -> FE-505 -> FE-506 -> FE-507

Pendencies (requires FE-011):
FE-600 -> FE-601 -> FE-602 -> FE-603

Signatures (requires FE-002):
FE-700 -> FE-701

Audit (requires FE-011):
FE-800 -> FE-801

Post-MVP Import:
FE-900 -> FE-901

Post-MVP PDF:
FE-910

Phase 2 PWA:
FE-1000 -> FE-1001 -> FE-1002 -> FE-1003 -> FE-1004

Phase 2 C2/C3/C4:
FE-1100

Phase 2 Dashboard:
FE-1200
```

---

## Suggested Implementation Order

### Sprint 1: Infrastructure (Days 1-5)
1. FE-001: Project Setup
2. FE-002: UI Library
3. FE-003: State Management
4. FE-004: API Client
5. FE-005: Router
6. FE-006: Forms

### Sprint 2: Shared Components (Days 6-10)
7. FE-010: Layout Components
8. FE-011: Data Table
9. FE-012: Form Components
10. FE-013: Dialog Components
11. FE-014: Feedback Components
12. FE-015: Filter Components

### Sprint 3: Authentication (Days 11-14)
13. FE-100: Auth Store
14. FE-101: Login Page
15. FE-102: Auth Guards
16. FE-103: Token Refresh
17. FE-110: Dashboard Page

### Sprint 4: User & Company Management (Days 15-18)
18. FE-200, FE-201, FE-202: User Management
19. FE-300, FE-301: Company Management
20. FE-310, FE-311: Discipline Management

### Sprint 5: Project Hierarchy (Days 19-24)
21. FE-320, FE-321, FE-322: Project Management
22. FE-330: Area Form
23. FE-340: System Form
24. FE-350: Subsystem Form
25. FE-360, FE-361: Equipment Management

### Sprint 6: Protocol Templates (Days 25-30)
26. FE-400, FE-401: Template List & Form
27. FE-402: Template Item Editor
28. FE-403: Template Item Type Components

### Sprint 7: Protocols - Part 1 (Days 31-36)
29. FE-500: Protocol List
30. FE-501: Protocol Create Form
31. FE-502: Protocol Detail Page
32. FE-506: Status Display Component
33. FE-507: Equipment Assignment Interface

### Sprint 8: Protocols - Part 2 (Days 37-42)
34. FE-503: Protocol Execution Page
35. FE-504: Protocol Item Input Components
36. FE-505: Protocol Approval Page

### Sprint 9: Pendencies & Signatures (Days 43-48)
37. FE-600, FE-601, FE-602, FE-603: Pendencies
38. FE-700, FE-701: Signatures

### Sprint 10: Audit & Polish (Days 49-52)
39. FE-800, FE-801: Audit Logs
40. UI polish and responsive testing
41. Cross-browser testing
42. Performance optimization

---

## Responsive Design Guidelines

All pages must be responsive for:
- **Desktop**: 1280px and above
- **Tablet Landscape**: 1024px - 1279px
- **Tablet Portrait**: 768px - 1023px

### Key Considerations:
- Sidebar collapses to icons on tablet
- Data tables use horizontal scroll on smaller screens
- Form fields stack vertically on narrow screens
- Touch targets minimum 44x44px for tablet use
- Protocol execution optimized for 10" tablets

---

## Accessibility Guidelines

All components must meet WCAG 2.1 AA standards:
- [ ] Keyboard navigation for all interactive elements
- [ ] ARIA labels on icons and buttons
- [ ] Color contrast ratios meet standards
- [ ] Focus indicators visible
- [ ] Screen reader compatibility
- [ ] Form error announcements

---

## Risk Considerations

1. **Complex Protocol Execution UI**: The item filling interface is critical for field use - invest in UX testing
2. **Template Item Editor**: Drag-and-drop with complex item types - consider using existing libraries
3. **Tablet Optimization**: Field technicians use tablets - test extensively on actual devices
4. **Offline Complexity**: Phase 2 offline features are complex - consider early prototyping
5. **Large Data Sets**: Protocol lists with thousands of items - implement virtualization

---

**End of Frontend Tasks Document**
