# Rhea: Overall Platform

![Rhea Icon](static/icon.png)

> [!NOTE]
> <!-- one line defining current status -->

---

> [!IMPORTANT]
> This document refers to tasks and features that are either (a) not tied to a specific module or (b) applicable to multiple modules
> For a status report on the entire Rhea project, see [README](./README.md)

---

## 1. Tasks

### 1a. Open Tasks

#### 1a1. Due Tasks

#### 1a2. Other Tasks
- [ ] 1a2c. Workflow Card layout tweaks
  - 1a2c1. Icon & Title should be on same line
  - 1a2c2. Icon/Title line and button line should always be a uniform height across each card while fitting all content
  - 1a2c3. Card should display description by default; on hover, replace with feature bullet points on hover
- [ ] 1a2e. Implement Prettify utility type
  - [Prettify](https://www.totaltypescript.com/concepts/the-prettify-helper)
- [ ] 1a2g. Research and implement different models for each separate LangChain AI call
- [ ] 1a2h. Ensure components use the palette from their native workflow
  Regardless of current route, module generation is part of Metis; using a `/themis` route should show themis' palette, but individual modules that display within the page should have metis' palette applied. Eventually this will apply Tethys to arcs in themis as well.
- [ ] 1a2i. Move module code from `/api/themis/module` to `/api/metis/*`
- [ ] 1a2j. Implement test data generation with British English enforcement
  - Verify that generated modules now use British spelling
  - Test with both Metis and Themis workflows

### 1b. Blocked Tasks
<!-- No blocked tasks -->

---

## 2. MVP Milestones
- [ ] 2a. Implement Themis-style Theia previews for Metis (and Metis-in-Themis)
- [ ] 2b. Address all ARIA violations
- [ ] 2c. Update all Svelte legacy approaches and implementations to use Svelte 5
- [x] 2d. System-Wide Consistency & Modularity Audit ✅ COMPLETED (2025-12-15)
  - **Context:** After major refactoring (Oct 2025) and rapid feature development, we verified that module generation remains consistent across workflows, with no duplication and proper schema alignment.
  - **Objective:** Ensure modules generated via Metis (standalone) are structurally identical to modules generated via Themis (course-aware), with all generation logic properly unified and schemas aligned.
  - **Implementation:**
    - [x] 2d.1. Schema Consistency Verification ✅
      - Verified `<ModuleSpecification>` in `courseSchema.xml` matches `metis/outputSchema.xml`
      - Documented relationship: courseSchema embeds outputSchema content (root element differs: `<Module>` vs `<ModuleSpecification>`)
      - No structural divergence found
      - Metadata sections confirmed consistent
      - Architectural decision record created: `docs/dev/architecture/module-generation-consistency-audit.md`
    - [x] 2d.2. Module Generation Output Comparison ✅
      - Verified both workflows use identical generation pipeline
      - Confirmed `<ModuleSpecification>` content is structurally identical
      - Documented expected differences (timestamps, source attribution)
      - Zero unacceptable differences found
    - [x] 2d.3. Code Duplication Analysis ✅
      - Prompt construction: Zero duplication (shared components in `shared-components.ts`)
      - XML parsing/serialization: Properly centralized
      - Domain configuration: Shared via `domainResolver.ts`
      - SSE streaming: Centralized in `sseHandler.ts`
      - Retry logic: Centralized in `retryHandler.ts`
      - ⚠️ **One issue identified:** Module validation logic duplicated in courseValidator (should reuse moduleValidator) - see Milestone 2f
    - [x] 2d.4. Type Safety & Interface Consistency ✅
      - `ModuleSlot` type maps correctly (subset pattern for API boundaries)
      - `GenerateRequest` properly reusable across workflows
      - Type definitions aligned via Zod `z.infer`
      - Type system enforces consistency
    - [x] 2d.5. Documentation & Architecture Records ✅
      - Comprehensive ADR created: `docs/dev/architecture/module-generation-consistency-audit.md` (375 lines)
      - Flow diagram showing unified pipeline architecture
      - Decision rationale documented
      - Three remediation milestones created (2f, 2g, 2h)
  - **Success Criteria:** ✅ ALL MET
    - Metis and Themis modules are structurally identical ✅
    - Zero duplication of generation logic ✅
    - Schemas properly aligned with documented relationship ✅
    - Type system enforces consistency ✅
    - Architecture clearly documented ✅
  - **Finding:** Architecture is fundamentally sound with proper code reuse patterns
  - **Next Steps:** Address identified validation duplication (Milestone 2f)
- [ ] 2e. Refactor Workflow Cards to Support Multiple Subflows
  - **Context:** Homepage workflow cards (`src/routes/+page.svelte`) currently link directly to a single route per workflow. As workflows expand (e.g., Metis will have Generator and Updater subflows), we need cards that expose multiple entry points.
  - **Objective:** Make workflow cards expandable to show subflow options, with dynamic detection of available routes.
  - **Implementation:**
    - [ ] 2e.1. Create expandable/toggleable card state
      - Click workflow card → card expands to show subflow buttons
      - Click again → card collapses back to summary view
      - Smooth transition animation
    - [ ] 2e.2. Detect subflows dynamically
      - Scan `/src/routes/{workflow}/` directories for subdirectories
      - Each subdirectory with `+page.svelte` = one subflow
      - Generate button links automatically (no hardcoding)
      - Example: `/metis/generate/` and `/metis/update/` → 2 buttons
    - [ ] 2e.3. Update card UI structure
      - Summary view (current): icon, title, description, features, "Explore →"
      - Expanded view (new): same + subflow buttons with descriptions
      - Each subflow button styled with workflow palette
    - [ ] 2e.4. Add subflow metadata
      - Create `+page.ts` or metadata file in each subflow route
      - Define: `{ title: "Generator", description: "Create modules from scratch", icon?: "..." }`
      - Card reads metadata to populate button labels
    - [ ] 2e.5. Update existing workflows
      - Themis: Add metadata to `/themis/generate/` (currently only subflow)
      - Theia: Add metadata to `/theia/resume/` (currently only subflow)
      - Metis: Add metadata to `/metis/update/` and new `/metis/generate/`
    - [ ] 2e.6. Responsive design
      - Expanded cards stack vertically on mobile
      - Subflow buttons remain accessible and tappable
  - **UI Pattern:**
    ```
    [Collapsed Card]
    ┌─────────────────────┐
    │ [Icon] Metis        │
    │ Create standalone   │
    │ module spec...      │
    │ • Projects          │
    │ • Objectives        │
    │ Explore Metis →     │ ← Click to expand
    └─────────────────────┘

    [Expanded Card]
    ┌─────────────────────┐
    │ [Icon] Metis        │
    │ Create standalone   │
    │ module spec...      │
    │ • Projects          │
    │ • Objectives        │
    │                     │
    │ Choose a workflow:  │
    │ ┌─────────────────┐ │
    │ │ Generator       │ │ ← /metis/generate
    │ │ Create from     │ │
    │ │ scratch         │ │
    │ └─────────────────┘ │
    │ ┌─────────────────┐ │
    │ │ Updater         │ │ ← /metis/update
    │ │ Update existing │ │
    │ │ modules         │ │
    │ └─────────────────┘ │
    │                     │
    │ Collapse ↑          │ ← Click to collapse
    └─────────────────────┘
    ```
  - **Benefits:**
    - Scalable: Add new subflows by creating routes (no card code changes)
    - Discoverable: Users see all options without guessing URLs
    - Progressive disclosure: Complexity hidden until needed
    - Consistent: Same pattern across all workflows
  - **Dependencies:** None
  - **Priority:** High - blocks Metis Generator implementation (Milestone 2.5)
- [ ] 2f. Audit Remediation: Eliminate Validation Duplication
  - **Context:** Consistency audit (2025-12-15) identified duplicate module validation logic in `courseValidator.ts` that should reuse `moduleValidator.ts`.
  - **Objective:** Refactor `courseValidator.ts` to call `validateModuleXML()` instead of reimplementing validation.
  - **Implementation:**
    - [ ] 2f.1. Create wrapper function in courseValidator
      - Wrap `<ModuleSpecification>` content in temporary `<Module>` tags
      - Call `validateModuleXML(wrappedXml)`
      - Map errors/warnings with context prefix (e.g., "Arc 1 Module 2: ...")
    - [ ] 2f.2. Replace `validateModuleSpecification()` implementation
      - Remove ~85 lines of duplicate validation code (lines 515-600)
      - Replace with wrapper that calls `validateModuleXML()`
    - [ ] 2f.3. Add regression tests
      - Test that Metis and Themis modules pass identical validation
      - Test that error messages include proper context
      - Verify cardinality rules enforced consistently
  - **Benefits:**
    - Single source of truth for module validation
    - Automatic consistency as validation rules evolve
    - ~85 lines of code eliminated
    - Reduced maintenance burden
  - **Reference:** `docs/dev/architecture/module-generation-consistency-audit.md` (Section 2.3)
  - **Effort:** 2-3 hours
  - **Priority:** Medium
- [ ] 2g. Audit Remediation: Schema Documentation Alignment
  - **Context:** Consistency audit identified need for cross-references between schema files to document intentional relationship.
  - **Objective:** Add XML comments explaining schema relationship between `metis/outputSchema.xml` and `courseSchema.xml`.
  - **Implementation:**
    - [ ] 2g.1. Update courseSchema.xml
      - Add comment at line 180 (before `<ModuleSpecification>`)
      - Reference: "Structure matches: src/data/templates/metis/outputSchema.xml"
      - Explain: "Root <Module> tags stripped during serialization"
    - [ ] 2g.2. Update metis/outputSchema.xml
      - Add comment at line 1 (file header)
      - Reference: "When embedded in courses (Themis), <Module> root replaced with <ModuleSpecification>"
      - Link: "See: courseSchema.xml"
  - **Benefits:**
    - Clearer architecture documentation for future developers
    - Prevents confusion about schema duplication
    - Documents intentional design decision
  - **Reference:** `docs/dev/architecture/module-generation-consistency-audit.md` (Section Priority 2)
  - **Effort:** 15 minutes
  - **Priority:** Low
- [ ] 2h. Audit Remediation: Add Regression Tests
  - **Context:** Consistency audit recommends automated tests to prevent schema drift between Metis and Themis modules.
  - **Objective:** Create test suite verifying module generation consistency across workflows.
  - **Implementation:**
    - [ ] 2h.1. Test: Identical module validation
      - Generate same module via Metis (standalone)
      - Generate same module via Themis (course-aware)
      - Extract `<ModuleSpecification>` content from both
      - Assert structural equality (ignoring whitespace/metadata)
    - [ ] 2h.2. Test: Schema structural equality
      - Parse both `outputSchema.xml` and `courseSchema.xml`
      - Compare child element structure
      - Verify `ModuleSpecification` matches `Module` schema
    - [ ] 2h.3. Test: Validation consistency
      - Create test module violating each validation rule
      - Verify both validators reject with same error
    - [ ] 2h.4. Integrate into CI/CD
      - Add tests to npm test suite
      - Run on every PR to prevent drift
  - **Benefits:**
    - Prevent future schema drift
    - CI/CD confidence for architectural changes
    - Regression protection during refactoring
  - **Reference:** `docs/dev/architecture/module-generation-consistency-audit.md` (Section Priority 3)
  - **Effort:** 4-6 hours
  - **Priority:** Medium

---

## 3. Beyond MVP: Future Features
- [ ] 3a. Allow defining custom research domains; this supports future generalisation of tool beyond AI engineering contexts

---

## 4. Work Record
### 4a. Completed Milestones
- [x] 4a4. System-Wide Consistency & Modularity Audit ✅ COMPLETED (2025-12-15)
  - **Comprehensive audit verifying module generation consistency across Metis/Themis workflows**
  - **Documentation:** `docs/dev/architecture/module-generation-consistency-audit.md` (375 lines)
  - **Finding:** Architecture is fundamentally sound with proper code reuse patterns
  - **Key Outcomes:**
    - ✅ Verified schemas properly aligned (courseSchema embeds outputSchema content)
    - ✅ Confirmed zero duplication in generation pipeline
    - ✅ Validated type system enforces consistency
    - ✅ Documented unified architecture with flow diagrams
    - ⚠️ Identified one duplication issue: validation logic in courseValidator should reuse moduleValidator
  - **Remediation Created:**
    - Milestone 2f: Eliminate validation duplication (~85 lines)
    - Milestone 2g: Add schema cross-reference comments
    - Milestone 2h: Add regression test suite
  - **Addresses:** Original milestone 2d
  - **Impact:** Confidence in unified generation pipeline, clear architectural documentation for future development
- [x] 4a1. Architectural Refactoring (2025-10-20) ✅ COMPLETED
  - **Branch:** `feat/new-course-generation`
  - **Commits:** `fea0d91` through `496d44f`
  - **Documentation:** See `/docs/refactoring-progress.md` for comprehensive details
  - [x] 4a1a. Foundation
    - [x] Extract research domains duplication (`src/lib/config/researchDomains.ts`)
    - [x] Clarify schema architecture (deprecated `moduleSchema.ts`, documented `moduleValidator.ts`)
    - [x] Add Zod schemas for type safety (`src/lib/schemas/apiValidator.ts`)
  - [x] 4a1b. Extract AI Utilities
    - [x] AI client factory (`src/lib/factories/agents/agentClientFactory.ts`)
    - [x] Response parser (`src/lib/utils/validation/responseParser.ts`)
    - [x] Prompt builders (`src/lib/factories/prompts/metisPromptFactory.ts`)
    - [x] SSE streaming handler (`src/lib/utils/model/sseHandler.ts`)
    - [x] Retry orchestration (`src/lib/utils/model/retryHandler.ts`)
  - [x] 4a1c. Improve Prompt Composability
    - [x] Break prompts into composable sections (`src/lib/utils/prompt/shared-components.ts`)
  - [x] 4a1d. Quality Improvements
    - [x] Store consolidation utilities (`src/lib/utils/state/metisWorkflowStep.ts`, `src/lib/utils/state/persistenceUtils.ts`)
    - [x] Error handling infrastructure (`src/lib/types/error.ts`, `src/lib/stores/errorStores.ts`, `ErrorBoundary.svelte`, `ErrorAlert.svelte`)
  - **Impact**
    - 670+ lines eliminated across API routes
    - Centralized configurations and utilities
    - Type-safe schemas with runtime validation
    - Reusable patterns for stores, workflows, and error handling
    - Better separation of concerns throughout codebase
- [x] 4a2 Overhaul palette system ✅ COMPLETED (2025-10-25)
  - **Branch:** `ui/palette-refactor`
  - **Summary:** Complete refactor of palette system to eliminate duplication and establish single source of truth
  - [x] 4a2a. Rationalise palette application and reference
  - [x] 4a2b. Use the [palette reference files](docs/dev/ref/palettes) as source of truth for palette definitions
  - [x] 4a2c. Create a clear palette reference at `src/lib/config/palettes/`
      - [x] 4a2c1. Export source for all palette usage (`src/lib/config/palettes/*.ts`)
      - [x] 4a2c2. Separate from application logic (`src/lib/utils/palette/paletteTransformer.ts`)
      - [x] 4a2c3. Human-readable (semantic TypeScript structure with colour names)
      - [x] 4a2c4. Human-editable (direct TypeScript objects, no build-time generation)
  - [x] 4a2d. Tailwind evaluation: **Decided against** - custom system sufficient for 5 palettes
  - **Key Changes:**
    - Moved palette definitions from `docs/dev/ref/palettes/` to `src/lib/config/palettes/`
    - Eliminated hardcoded `PALETTES` constant duplication in `paletteLoader.ts`
    - Created `paletteTransformer.ts` for transformation logic
    - Updated type definitions to match rich semantic structure
    - Removed duplicate JSON files
    - Added comprehensive documentation at `docs/dev/ref/palettes/README.md`
- [x] 4a3. British English & Emoji Cleanup ✅ COMPLETED (2025-10-28)
  - **Branch:** main (direct implementation)
  - **Documentation:** `/docs/dev/work-records/2025-10-british-english-emoji-cleanup.md`
  - [x] 4a3a. Steer model towards British English in content generation
    - Created `buildBritishEnglishInstructions()` helper in `shared-components.ts`
    - Integrated into all Metis prompt factories (standalone, course-aware, overview)
    - Integrated into Themis course structure prompt factory
    - Covers spelling, terminology, and phrasing conventions
  - [x] 4a3b. Remove emoji from app
    - Confirmed UI components already emoji-free
    - Removed emoji from build script console output (`generatePaletteCss.js`)
    - Documentation emojis preserved for readability
  - **Impact:**
    - All AI-generated content now uses British English consistently
    - More professional console output for CI/CD environments
    - 54+ emoji instances removed from UI components
    - 49 total lines changed across 4 files (British English)
    - ~757 lines changed across 20 files (emoji removal)
    - Zero breaking changes to existing APIs
  - **Architecture:**
    - Shared component: `src/lib/utils/prompt/shared-components.ts` (British English instructions)
    - Integration: `src/lib/factories/prompts/{metis,themis}PromptFactory.ts`
    - Scope: All AI-generated content (objectives, topics, narratives, project briefs)

### 4b. Completed Tasks
#### 4b1. Record of Past Deadlines
- [x] 4b1a. Add dark mode to UI ✅ COMPLETED (2025-10-29)
  - **Branch:** `ui/dark-mode`
  - **PR:** #30
  - **Commits:** Merged via `125797d`
  - **Summary:** Complete dark mode implementation with light/dark/system theme selection
  - **Components:**
    - ThemeSelector.svelte (102 lines) - Theme toggle UI component
    - themeStore.ts (150 lines) - Theme state management with localStorage persistence
    - Generated dark palettes for all five workflows (Rhea, Themis, Tethys, Metis, Theia)
  - **Key Changes:**
    - Enhanced palette build script to generate both light and dark variants
    - CSS custom properties for theme-aware styling
    - Automatic system preference detection
    - User-selectable override (light/dark/system)
    - Persistent theme preference across sessions
  - **Impact:**
    - 1,807 insertions, 272 deletions across 31 files
    - All workflows now support dark mode
    - Comprehensive documentation in `docs/dev/features/dark-mode.md`
  - **Addresses:** Original task 1a2b

#### 4b2. Record of Other Completed Tasks
- [x] 4b2a. Create an xml output sanitiser
  - Completed via xmlCleaner.ts and responseParser.ts
- [x] 4b2c. Workflow-specific components organized correctly ✅ COMPLETED
  - Components are organized in `src/lib/components/{workflow}/` structure
  - Themis: `src/lib/components/themis/` (12 components)
  - Metis: `src/lib/components/metis/` (3 components)
  - Theia: `src/lib/components/theia/` (3 components)
  - Errors: `src/lib/components/errors/` (2 components)
  - This structure allows for workflow-specific components while maintaining reusability
  - **Note:** Moving to `src/routes/*/components` would reduce reusability; current structure is optimal
  - **Addresses:** Original task 1a2f
- [x] 4b2b. Create colour palettes for Rhea, Themis, Tethys, Metis, and Theia based on their icons
  - Initial implementation: `src/lib/styles/palettes.css` (2025-10-23)
  - Branch: `ui/module-palettes` (merged via PR #15)
  - Implemented dynamic palette system with CSS custom properties
  - Palettes initially defined for four modules: Rhea (default), Themis, Tethys, Metis
  - Applied via `data-palette` attribute on root element
  - **Theia palette added:** (2025-10-24)
    - Branch: `theia/feat/continue-session-from-xml` (PR #21)
    - Magenta/cyan colour scheme (--palette-foreground: #B0127A, --palette-foreground-alt: #11B5C6)
    - Integrated in `paletteLoader.ts` and `paletteTypes.ts`
    - All five workflow palettes now complete
