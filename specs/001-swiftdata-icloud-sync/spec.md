# Feature Specification: SwiftData Migration with iCloud Sync

**Feature Branch**: `001-swiftdata-icloud-sync`  
**Created**: 2025-11-04  
**Status**: Draft  
**Input**: User description: "把 app 改成 swiftData，同步到 icloud"

## User Scenarios & Testing *(mandatory)*

<!--
  IMPORTANT: User stories should be PRIORITIZED as user journeys ordered by importance.
  Each user story/journey must be INDEPENDENTLY TESTABLE - meaning if you implement just ONE of them,
  you should still have a viable MVP (Minimum Viable Product) that delivers value.
  
  Assign priorities (P1, P2, P3, etc.) to each story, where P1 is the most critical.
  Think of each story as a standalone slice of functionality that can be:
  - Developed independently
  - Tested independently
  - Deployed independently
  - Demonstrated to users independently
-->

### User Story 1 - Migrate app to SwiftData storage (Priority: P1)

The IdeaBox app currently stores ideas in CoreData. Users need the data to be stored in SwiftData instead, while maintaining all existing functionality and data preservation. This is the foundational migration that enables cross-device sync.

**Why this priority**: P1 - This is the blocking prerequisite for iCloud sync. Without SwiftData as the storage layer, iCloud synchronization cannot work. Users cannot benefit from any sync features until this migration is complete.

**Independent Test**: Can be tested by launching the app and verifying that:
- All previously stored ideas are accessible and display correctly
- Users can create, edit, delete ideas without errors
- App closes and relaunches correctly with data persistence
- Data is stored in SwiftData format (can verify via CoreData → SwiftData migration completion)

**Acceptance Scenarios**:

1. **Given** the app has existing CoreData ideas, **When** user updates to the new build, **Then** all ideas are successfully migrated to SwiftData and display correctly
2. **Given** a fresh app install, **When** user adds a new idea, **Then** the idea is created and persists in SwiftData format
3. **Given** ideas stored in SwiftData, **When** user edits an idea, **Then** changes are saved and remain after app restart
4. **Given** ideas stored in SwiftData, **When** user deletes an idea, **Then** the idea is removed and does not reappear after restart

---

### User Story 2 - Enable iCloud synchronization for ideas (Priority: P2)

Once SwiftData is in place, users need ideas to sync across their devices via iCloud CloudKit. If a user creates an idea on iPhone, it should appear on iPad and Mac automatically without manual action.

**Why this priority**: P2 - Critical for cross-device experience but depends on SwiftData migration (P1) being complete first. Enables multi-device workflows which increase product value.

**Independent Test**: Can be tested by:
- Setting up two devices signed in to the same iCloud account
- Creating, editing, and deleting ideas on one device
- Verifying changes appear on the second device within a reasonable time (e.g., under 10 seconds)
- No user action required to trigger sync

**Acceptance Scenarios**:

1. **Given** two devices with the same iCloud account running IdeaBox, **When** user creates an idea on Device A, **Then** the idea appears on Device B within 10 seconds
2. **Given** an idea is synced to Device B, **When** user edits the idea on Device A, **Then** the change propagates to Device B and displays updated content
3. **Given** ideas synced across devices, **When** user deletes an idea on Device A, **Then** the deletion is reflected on Device B
4. **Given** Device B is offline, **When** user creates an idea on Device A, **Then** when Device B reconnects, it receives the new idea

---

### User Story 3 - Handle sync conflicts and offline scenarios gracefully (Priority: P3)

Users should be protected from data loss in edge cases like network failures, simultaneous edits, or account sign-out. The app should recover gracefully and continue functioning.

**Why this priority**: P3 - Improves reliability and user confidence but is not a blocker for basic sync functionality. Can be added after P1 and P2 are working.

**Independent Test**: Can be tested by:
- Simulating network disconnection (using Xcode network conditions)
- Making simultaneous edits on multiple devices
- Signing out of iCloud account
- Verifying app continues to function and doesn't crash or lose data

**Acceptance Scenarios**:

1. **Given** two devices editing the same idea simultaneously, **When** sync completes, **Then** one version is retained without data loss (last-write-wins or user-visible merge indicator)
2. **Given** Device A is offline, **When** user creates ideas on Device B and Device A reconnects, **Then** previously offline ideas sync without duplication
3. **Given** user is signed out of iCloud, **When** they return to the app, **Then** local ideas remain accessible and app does not crash
4. **Given** iCloud sync fails temporarily, **When** network recovers, **Then** sync retries automatically without user intervention

## Constitution Compliance (MANDATORY)

本規格文件必須遵守 `.specify/memory/constitution.md` 中定義的「核心原則」與治理條款。提交此規格時，請檢查並註明：

- 是否滿足憲法中關於可測性、MVP 優先與簡潔設計的要求？
- 是否列出與版本發布/破壞性變更相關的影響評估？
- 是否在「User Scenarios & Testing」中提供獨立可測試的驗收案例？

若本規格存在與憲法衝突之處，必須在本節說明並提出替代方案或豁免申請。

### Edge Cases

- What happens when iCloud account is signed out during a sync operation? (App should pause sync, preserve local changes, resume when account is restored)
- How does the system handle idea creation when both devices edit the same idea within seconds? (Last-write-wins or visible conflict indicator)
- What occurs if a device never connects to iCloud after creating local ideas? (Local ideas persist; sync occurs when network becomes available)
- How does system behave if SwiftData encounters corruption during migration from CoreData? (Graceful error message; preserved CoreData as fallback)
- What happens if iCloud quota is exceeded during sync? (User receives clear notification; local ideas remain safe until quota is resolved)

## Requirements *(mandatory)*

<!--
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right functional requirements.
-->

### Functional Requirements

- **FR-001**: System MUST migrate all existing CoreData ideas to SwiftData format without loss or corruption
- **FR-002**: System MUST use SwiftData as the persistent storage layer for all idea data (replacing CoreData)
- **FR-003**: System MUST synchronize ideas across multiple devices using iCloud CloudKit when user is signed in to same Apple ID
- **FR-004**: System MUST propagate idea creation, updates, and deletions to other devices within [NEEDS CLARIFICATION: acceptable sync latency - suggest 10 seconds as typical for CloudKit, but confirm if different target]
- **FR-005**: System MUST preserve local data when iCloud is unavailable and resume sync when connectivity returns
- **FR-006**: System MUST display clear sync status to user (e.g., "syncing", "synced", "error") to indicate data state
- **FR-007**: System MUST handle simultaneous edits on multiple devices by [NEEDS CLARIFICATION: conflict resolution strategy - suggest last-write-wins is simplest MVP, but could also be visible merge/conflict indicator]
- **FR-008**: System MUST not crash, corrupt data, or delete ideas if iCloud operations fail
- **FR-009**: System MUST support sign-out of iCloud account; local ideas remain accessible but won't sync to other devices
- **FR-010**: System MUST work on iOS 15+ (standard for current SwiftData availability)

### Key Entities

- **Idea**: 
  - Attributes: id (unique identifier), title (string), description (text), dateCreated (timestamp), dateModified (timestamp), isCompleted (boolean)
  - Relationships: Belongs to iCloud container; can be synced across devices
  - Sync behavior: Marked for sync to CloudKit; conflicts resolved by last-write-wins or user-visible indicator

- **SyncMetadata** (internal):
  - Attributes: ideaId, localChangeTimestamp, cloudChangeTimestamp, syncStatus (pending/synced/error)
  - Purpose: Track sync state to enable reliable propagation and conflict detection

## Success Criteria *(mandatory)*

<!--
  ACTION REQUIRED: Define measurable success criteria.
  These must be technology-agnostic and measurable.
-->

### Measurable Outcomes

- **SC-001**: All existing ideas migrate successfully with 100% data preservation (zero data loss from CoreData to SwiftData migration)
- **SC-002**: Ideas created on one device appear on a second device within 10 seconds when both are on same iCloud account
- **SC-003**: Sync continues to function automatically; users do not need to manually trigger sync or refresh
- **SC-004**: User can create, edit, and delete ideas offline; changes persist locally and sync when connectivity returns
- **SC-005**: App remains stable and usable even when iCloud services are unavailable or sync fails
- **SC-006**: Users can identify sync status (syncing/synced/failed) via visual indicator in the UI
- **SC-007**: Zero data loss or duplication when handling simultaneous edits or network interruptions
