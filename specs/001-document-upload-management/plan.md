# Implementation Plan: Document Upload and Management

**Branch**: `001-document-upload-management` | **Date**: 2026-09-15 | **Spec**: `spec.md`
**Input**: Feature specification from `/specs/001-document-upload-management/spec.md`

## Summary

This feature adds a secure document management capability to the existing ContosoDashboard Blazor Server application. It extends the current layered architecture with a new document domain, local filesystem storage abstraction, metadata management, access control, and notifications while preserving the project’s offline-first training model.

## Technical Context

**Language/Version**: C# on ASP.NET Core 8.0  
**Primary Dependencies**: ASP.NET Core, Blazor Server, Entity Framework Core, SQL Server LocalDB, Bootstrap 5, Microsoft.AspNetCore.Components.Authorization  
**Storage**: Local filesystem for uploaded files in a dedicated `AppData/uploads` structure plus SQL Server LocalDB for metadata and relationships  
**Testing**: xUnit for business logic, bUnit for component behavior, and integration checks for authorization and storage flows recommended for this feature  
**Target Platform**: Windows development environment with ASP.NET Core web app  
**Project Type**: Web application (Blazor Server)  
**Performance Goals**: Upload under 30 seconds for files up to 25 MB, list retrieval under 2 seconds for up to 500 documents, search under 2 seconds  
**Constraints**: Offline-only training deployment, no cloud dependencies, local file persistence, role-based authorization, integer-based IDs, text-based categories  
**Scale/Scope**: Single-tenant dashboard app used by a small internal team with document metadata and policy enforcement for projects and users

## Constitution Check

The feature aligns with the project’s established security and architecture principles:

- Security-by-default remains intact through service-level checks, user isolation, and no direct web-root file serving.
- The solution respects the offline-first requirement by using a local `IFileStorageService` abstraction with local filesystem implementation.
- The architecture remains layered and incremental rather than rewriting the application.
- The design keeps document metadata in the data layer and file persistence in a storage abstraction so cloud migration remains feasible.

No constitution violations are identified for this feature; the design is a focused extension of the current patterns.

## Project Structure

### Documentation (this feature)

```text
specs/001-document-upload-management/
├── spec.md
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
└── tasks.md
```

### Source Code (repository root)

```text
ContosoDashboard/
├── Data/
│   └── ApplicationDbContext.cs
├── Models/
│   ├── Document.cs
│   ├── DocumentShare.cs
│   ├── DocumentActivityLog.cs
│   ├── User.cs
│   ├── TaskItem.cs
│   ├── Project.cs
│   └── ProjectMember.cs
├── Services/
│   ├── IFileStorageService.cs
│   ├── LocalFileStorageService.cs
│   ├── DocumentService.cs
│   ├── NotificationService.cs
│   ├── ProjectService.cs
│   └── UserService.cs
├── Pages/
│   ├── Documents.razor
│   ├── ProjectDetails.razor
│   ├── Tasks.razor
│   ├── Index.razor
│   └── Login.cshtml
├── Shared/
│   └── MainLayout.razor
├── wwwroot/
│   └── css/
└── Program.cs
```

**Structure Decision**: The feature will follow the existing single-project Blazor Server architecture. New functionality will be added within the current `Models`, `Services`, `Data`, and `Pages` folders without introducing a major rewrite or separate backend application.

## Phase 0: Research and Design Decisions

1. Storage strategy: Use a `Document` metadata model with a dedicated `FilePath` column and a separate secure upload directory under `AppData/uploads`.
2. Security model: Authorize document access by owner, project membership, role, or explicit share records.
3. Identity strategy: Reuse existing `User` and `Project` abstractions and current mock authentication flow.
4. File handling: Generate GUID-based safe filenames before persistence and validate MIME type and extension on upload.
5. Sharing model: Add a `DocumentShare` entity and notification trigger to cover “shared with me” scenarios.
6. Audit model: Add a simple `DocumentActivityLog` table to track uploads, downloads, replacements, deletes, and share actions.

## Data Model Summary

- `Document` stores metadata, file path, size, type, uploader, project, category, tags, and access properties.
- `DocumentShare` links documents to recipients and records the share event.
- `DocumentActivityLog` records audit events for administrators and reporting.
- Existing `Project` and `User` models remain the authorization anchors.

## Storage and Security Approach

- Files live in a local directory outside the web root.
- Each upload generates a unique relative path before the database record is written.
- `IFileStorageService` abstracts upload, delete, and download behavior to support future Azure Blob migration.
- Authorization checks happen in the service layer before returning document records or download streams.
- Document preview and download endpoints must validate permission against document ownership, project membership, or explicit share records.

## Dependencies and Integration Points

- `ApplicationDbContext`: add `DbSet<Document>`, `DbSet<DocumentShare>`, and `DbSet<DocumentActivityLog>`.
- `ProjectService`: reuse project membership rules to determine project document access.
- `NotificationService`: trigger notifications for shared documents and project updates.
- `Index.razor`: add recent documents summary widget.
- `ProjectDetails.razor`: render project document list and upload affordance.
- `Tasks.razor`: add task-linked document attachments if needed for the final feature scope.

## Complexity Tracking

No constitution violations require a special exception. This feature stays within the project’s layered, service-driven design and avoids unnecessary architectural expansion.
