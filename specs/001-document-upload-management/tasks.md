---

description: "Task list for the document upload and management feature"
---

# Tasks: Document Upload and Management

**Input**: Design documents from `/specs/001-document-upload-management/`  
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Prepare the project for document storage, data access, and authorization support.

- [ ] T001 Create the feature directory and initialize the local document storage layout under `AppData/uploads`
- [ ] T002 Add configuration entries for upload limits, allowed extensions, and local storage root in `appsettings.json`
- [ ] T003 [P] Extend `ApplicationDbContext` with `DbSet<Document>`, `DbSet<DocumentShare>`, and `DbSet<DocumentActivityLog>`
- [ ] T004 [P] Add the `Document`, `DocumentShare`, and `DocumentActivityLog` model classes in `ContosoDashboard/Models/`

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Establish the secure storage abstraction and authorization contract before user-facing work begins.

- [ ] T005 Create the `IFileStorageService` contract with `UploadAsync`, `DeleteAsync`, `DownloadAsync`, and `GetUrlAsync`
- [ ] T006 Implement `LocalFileStorageService` using a secure local directory outside `wwwroot`
- [ ] T007 Add validation logic for allowed file types, size limits, and unique GUID-based filenames
- [ ] T008 Implement `DocumentService` with upload, metadata update, delete, search, and authorization checks
- [ ] T009 Add service-level project membership and document ownership validation for role-based access control
- [ ] T010 Add database indexes and seed or migration scaffolding for document entities and relationships

**Checkpoint**: Foundation ready - document upload and access logic can now be implemented in parallel.

---

## Phase 3: User Story 1 - Upload and organize work documents (Priority: P1) 🎯 MVP

**Goal**: Allow users to upload supported documents, attach metadata, and view them in a personal list.

**Independent Test**: A user can upload a valid file, see the document recorded in their list, and receive a clear validation message when the file is invalid.

### Tests for User Story 1

- [ ] T011 [P] [US1] Add validation test for unsupported file types and exceeding the 25 MB limit in `ContosoDashboard.Tests`
- [ ] T012 [P] [US1] Add integration test for successful upload and metadata persistence in `ContosoDashboard.Tests`

### Implementation for User Story 1

- [ ] T013 [P] [US1] Add a document upload page or component in `ContosoDashboard/Pages/Documents.razor`
- [ ] T014 [P] [US1] Add upload form validation for title, category, project selection, and optional tags
- [ ] T015 [US1] Implement file upload workflow: validate -> generate safe path -> save to storage -> save metadata to database
- [ ] T016 [US1] Add success/error message handling and upload progress state in the UI
- [ ] T017 [US1] Add document metadata editing and replace-file support in the document detail flow
- [ ] T018 [US1] Display the user’s uploaded documents in a sortable table or grid with category and project columns

**Checkpoint**: At this point, User Story 1 should be fully functional and testable independently.

---

## Phase 4: User Story 2 - Browse, filter, and search documents (Priority: P2)

**Goal**: Enable quick document discovery across the user’s files and project collections.

**Independent Test**: A user can search by metadata and filter by project/category to find the correct document without unauthorized records appearing.

### Tests for User Story 2

- [ ] T019 [P] [US2] Add search/filter test covering title, tag, and uploader criteria in `ContosoDashboard.Tests`
- [ ] T020 [P] [US2] Add authorization test verifying users only see documents they can access

### Implementation for User Story 2

- [ ] T021 [P] [US2] Add My Documents page or tabs with sorting and filter controls
- [ ] T022 [US2] Implement category and project filtering logic in `DocumentService`
- [ ] T023 [US2] Implement searchable document queries by title, description, tag, uploader, and associated project
- [ ] T024 [US2] Show project documents on `ProjectDetails.razor` with access checks and file list rendering
- [ ] T025 [US2] Add preview and download actions that enforce authorization before serving the file

**Checkpoint**: At this point, Users can browse and locate their permitted documents without assistance.

---

## Phase 5: User Story 3 - Share access and monitor activity (Priority: P3)

**Goal**: Extend the feature to collaboration, notifications, and audit reporting.

**Independent Test**: A document owner can share a document and a recipient can access it through the shared section, while an admin can review the activity log.

### Tests for User Story 3

- [ ] T026 [P] [US3] Add share flow test covering recipient notification and shared-item visibility
- [ ] T027 [P] [US3] Add audit-log test for upload, delete, and download events

### Implementation for User Story 3

- [ ] T028 [P] [US3] Create `DocumentShare` model and persistence logic for user-sharing relationships
- [ ] T029 [US3] Add notification generation for shared document events and project-level document activity
- [ ] T030 [US3] Add shared-with-me display and filtering in the document UI
- [ ] T031 [US3] Implement `DocumentActivityLog` recording for uploads, downloads, deletions, replacements, and shares
- [ ] T032 [US3] Add admin reporting or summary views showing upload frequency, top document types, and top uploaders
- [ ] T033 [US3] Update the dashboard or summary widgets with recent document activity and document counts

**Checkpoint**: All user stories should now be independently functional and ready for polish.

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Final review, security hardening, and UX cleanup across the feature.

- [ ] T034 [P] Review and harden authorization checks for downloads, deletes, sharing, and project visibility
- [ ] T035 [P] Add final UI copy, empty states, and error handling across the document pages
- [ ] T036 [P] Validate that storage files are organized by user/project path and are never stored under `wwwroot`
- [ ] T037 [P] Run a quick regression pass on the dashboard, project details, and task-related document flows
- [ ] T038 Document the feature in stakeholder-facing guidance and migration notes for future Azure storage implementation

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies
- **Foundational (Phase 2)**: Must complete before any user story work begins
- **User Story 1 (Phase 3)**: Depends on the foundation, no dependency on other stories
- **User Story 2 (Phase 4)**: Depends on the foundation and can proceed after US1 or in parallel once the core upload service is stable
- **User Story 3 (Phase 5)**: Depends on the foundation and benefits from US1/US2 completion
- **Polish (Phase 6)**: Depends on all desired user stories being complete

### Within Each User Story

- Tests are written first when possible and must reflect real user behavior.
- Service and domain logic are implemented before the UI is finalized.
- Authorization rules are validated before considering the story complete.
- User-facing messages and empty states are added last.

---

## Parallel Opportunities

- `T003` and `T004` can run in parallel.
- `T011` and `T012` can run in parallel during the MVP.
- `T019` and `T020` can run in parallel during the browse/search story.
- `T026` and `T027` can run in parallel during the sharing/audit story.
- `T034`, `T035`, `T036`, and `T037` can run in parallel during final polish.
