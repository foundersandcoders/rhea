# Project Status
> [!NOTE]
> Since Rhea is the aggregate of all submodules, this section summarises roadmaps for all submodules actively in development.

---

## 1. Submodule Roadmaps

### 1.1. Core Modules
- 1.1.1. [**Rhea:**](Rhea-MVP.md) _Platform_

### 1.2. Workflow Modules
- 1.2.1. [**Themis:**](Themis-MVP.md) _Course Builder_
- 1.2.2. [**Tethys:**](Tethys-MVP.md) _Arc Designer_
- 1.2.3. [**Metis:**](Metis-MVP.md) _Module Generator_

### 1.3. Utility Modules
- 1.3.1. [**Theia:**](Theia-MVP.md) _Content Preview & Export_
- 1.3.2. [**Thalassa:**](Thalassa-MVP.md) _Atomic Learning Assembler_
- 1.3.3. [**Mnemosyne:**](Mnemosyne-MVP.md) _Storage & Retrieval_

---

## 2. Current State
> [!NOTE]
> Following major architectural refactoring in October 2025-10, the codebase now features factories, utilities, and centralized configurations that enable rapid feature development.

### 2.1. Rhea: Core Platform
<details><summary>Status: ~98% MVP ✅</summary>
  <ul>
    <li>Foundation complete</li>
    <li>Component architecture established</li>
    <li>✅ British English enforcement in AI generation</li>
    <li>✅ Emoji removed from UI and build scripts</li>
    <li>✅ Dark mode implementation complete</li>
    <li>UI polish ongoing (ARIA violations pending)</li>
  </ul>
</details>

<details><summary>Implemented</summary>
  <p>The platform provides a hub-based navigation structure with module-specific colour palettes and centralized error handling.</p>
  <p>Recent work focused on establishing reusable patterns for state management (<code>persistedStore()</code>), AI client configuration (agent factories), and prompt construction (composable prompt components).</p>
  <p>✅ <strong>British English enforcement</strong>: All AI-generated content now uses British spelling and terminology via <code>buildBritishEnglishInstructions()</code> integrated into all prompt factories.</p>
  <p>✅ <strong>Professional UI polish</strong>: Removed 54+ emoji instances from UI components and build scripts; improved accessibility and CI/CD compatibility.</p>
  <p>✅ <strong>Dark mode support</strong> (2025-10-29): User-selectable light/dark/system themes with ThemeSelector component, automatic system preference detection, and dark palette variants for all five workflows. 1,807 insertions across 31 files.</p>
</details>

<details><summary>Next Up</summary>
  <ul>
    <li>Address ARIA violations for accessibility</li>
  </ul>
</details>

### 2.2. Workflow Modules

#### 2.2.1. Themis: Course Builder
<details><summary>Status: 100% MVP ✅</summary>
  <p>End-to-end workflow complete, component refactoring complete, XML export implemented, polish pending</p>
</details>

<details><summary>Implemented</summary>
  <ul>
    <li>✅ Foundation (hub, types, stores, config form)</li>
    <li>✅ Arc-based structure planning with thematic organization</li>
    <li>✅ AI structure generation with web research integration</li>
    <li>✅ Structure review interface with editable narratives</li>
    <li>✅ <code>localStorage</code> persistence for auto-save/restore</li>
    <li>✅ Module generation orchestration (ModuleGenerationList refactored: 896→441 lines)</li>
    <li>✅ Component breakdown (ProgressSummary, ModuleCard, ArcSection, ModulePreviewModal)</li>
    <li>✅ Centralized store utilities (moduleStoreHelpers.ts)</li>
    <li>✅ <strong>NEW:</strong> Module coherence system with overview-first generation</li>
    <li>✅ <strong>NEW:</strong> Smart title system (undefined/prompt/literal modes)</li>
    <li>✅ <strong>NEW:</strong> Knowledge context builder for cumulative learner progression</li>
    <li>✅ Course-aware module generation API endpoint (193 lines)</li>
    <li>✅ Course context integration in prompt factory</li>
    <li>✅ CourseOverview component for final review and export (1462 lines)</li>
    <li>✅ <strong>NEW:</strong> Course XML schema and validator (courseSchema.xml, courseValidator.ts)</li>
    <li>✅ <strong>NEW:</strong> Comprehensive validation with temporal consistency checks (15 test cases)</li>
    <li>✅ <strong>NEW:</strong> Complete course XML export with embedded module specifications</li>
    <li>✅ <strong>NEW:</strong> Pre-download validation with user feedback banners</li>
    <li>✅ <strong>NEW:</strong> Export button with success/error validation in CourseOverview</li>
    <li>📋 Technical debt: SSE parsing vulnerability, race conditions, error handling improvements</li>
  </ul>
</details>

<details><summary>Next Up</summary>
  <ul>
    <li>Technical debt resolution (SSE, error handling)</li>
    <li>UI polish improvements</li>
    <li>Dark mode implementation</li>
  </ul>
</details>
</text>

<old_text line=158>
## 3. Next Milestones
> [!NOTE]
> The 5 most significant or important tasks to tackle next.

1. **[Add Course XML Schema and Validator](Themis-MVP.md#2-mvp-milestones)** (Themis 2.2) - Define course-level XML schema wrapping multiple modules with validation
2. **[Implement XML Export Functionality](Themis-MVP.md#2-mvp-milestones)** (Themis 2.3) - Complete course XML export (depends on 2.2)
3. **[Add Dark Mode to UI](Rhea-MVP.md#1a2-other-tasks)** (Rhea 1a2b) - User-selectable light/dark/system theme with dark palettes for all workflows
4. **[Implement Metis boilerplate text insertion](Metis-MVP.md)** - Add standard module text sections for consistent structure
5. **[Address ARIA violations](Rhea-MVP.md#2-mvp-milestones)** (Rhea 2b) - Improve accessibility across all workflow components

#### 2.2.2. Tethys: Arc Designer
<details><summary>Status: 0% MVP</summary>
  <p>Not yet started</p>
</details>

<details><summary>Implemented</summary>
  <p><strong>Tethys</strong> will provide standalone arc design capabilities outside of full course generation.</p>
  <p>Currently, arc features are embedded within <strong>Themis</strong> workflow.</p>
</details>

<details><summary>Next Up</summary>
  <ul>
    <li>On hold until after <strong>Themis</strong> & <strong>Metis</strong> fully implemented</li>
  </ul>
</details>

#### 2.2.3. Metis: Module Generator
<details><summary>Status: ~98% MVP ✅</summary>
  <p>Feature complete, domain configuration implemented, polish pending</p>
</details>

<details><summary>Implemented</summary>
  <ul>
    <li>✅ Complete module generation workflow with file upload and structured input</li>
    <li>✅ AI-powered generation with Claude Sonnet 4.5</li>
    <li>✅ Deep research capability with web search integration</li>
    <li>✅ <strong>NEW:</strong> Domain whitelist configuration with category-based selection</li>
    <li>✅ <strong>NEW:</strong> Domain blacklisting and whitelist ignoring options</li>
    <li>✅ XML schema validation with automatic retry logic (max 3 attempts)</li>
    <li>✅ Changelog and provenance tracking with confidence scoring</li>
    <li>✅ SSE streaming for progress feedback</li>
    <li>✅ Intelligent step navigation with automatic advancement</li>
    <li>✅ UI updated to echo Themis multi-step workflow pattern</li>
    <li>📋 Boilerplate module text insertion</li>
    <li>📋 Tech stack adherence validation</li>
    <li>📋 Subsection toggling functionality</li>
  </ul>
</details>

<details><summary>Next Up</summary>
  <ul>
    <li>Boilerplate text insertion</li>
    <li>Tech stack validation</li>
    <li>Overview/full generation options (from Themis)</li>
  </ul>
</details>

### 2.3. Utility Modules

#### 2.3.1. Theia: Content Preview & Export
<details><summary>Status: ~100% MVP ✅</summary>
  <p>Core functionality complete and production-ready</p>
</details>

<details><summary>Implemented</summary>
  <ul>
    <li>✅ Reusable content exporter for human-readable previews</li>
    <li>✅ Supports both Themis and Metis output</li>
    <li>✅ Configurable detail levels (minimal, summary, detailed, complete)</li>
    <li>✅ Selective export (specific arcs, modules, or sections)</li>
    <li>✅ Multiple formats (Markdown, HTML, JSON; PDF planned)</li>
    <li>✅ Live preview before export</li>
    <li>✅ Client-side processing (no server storage)</li>
    <li>✅ Integrated into Metis and Themis workflows</li>
    <li>✅ <strong>NEW:</strong> Standalone <code>/theia</code> route with course upload interface</li>
    <li>✅ <strong>NEW:</strong> JSON course upload with drag-and-drop</li>
    <li>✅ <strong>NEW:</strong> Comprehensive validation for course structure</li>
    <li>✅ <strong>NEW:</strong> Round-trip workflow (Themis → export JSON → upload → continue)</li>
    <li>✅ <strong>NEW:</strong> Theia branding with magenta/cyan palette</li>
  </ul>
  <p>Initial export implementation: October 2025-10-23 via PR #14 (2,813 lines)</p>
  <p><strong>Course upload implementation: October 2025-10-24 via PR #21 (2,417 lines)</strong></p>
</details>

<details><summary>Next Up</summary>
  <h5>Module XML Upload & Workflow Resume (Q1 2026)</h5>
  <ul>
    <li>Upload module XML → continue in Metis or preview/export</li>
    <li>Upload course XML → continue in Themis (after course XML schema exists)</li>
    <li>XML validation and type detection</li>
  </ul>
  <h5>Polish</h5>
  <ul>
    <li>PDF export format</li>
    <li>Export analytics/usage tracking</li>
  </ul>
</details>

#### 2.3.2. Thalassa: Atomic Learning Assembler
<details><summary>Status: ~0% MVP</summary>
  <p>Not yet started</p>
</details>

<details><summary>Implemented</summary>
  <ul>
    <li></li>
  </ul>
</details>

<details><summary>Next Up</summary>
  <ul>
    <li></li>
  </ul>
</details>

#### 2.3.3. Mnemosyne: Storage & Retrieval
<details><summary>Status: ~0% MVP ✅</summary>
  <p>Not yet started</p>
</details>

<details><summary>Implemented</summary>
  <ul>
    <li></li>
  </ul>
</details>

<details><summary>Next Up</summary>
  <ul>
    <li></li>
  </ul>
</details>

---

## 3. Next Milestones
> [!NOTE]
> The 5 most significant or important tasks to tackle next.

1. **[Address ARIA violations](Rhea-MVP.md#2-mvp-milestones)** (Rhea 2b) - Improve accessibility across all workflow components
2. **[Implement Metis boilerplate text insertion](Metis-MVP.md)** - Add standard module text sections for consistent structure
3. **[Resolve Technical Debt in Themis](Themis-MVP.md#1-tasks)** - Fix SSE parsing vulnerability, race conditions, and error handling gaps
4. **[Implement Module XML Upload in Theia](Theia-MVP.md)** - Upload module XML to continue in Metis or preview/export
5. **[Implement overview/full generation options in Metis](Metis-MVP.md#2-mvp-milestones)** (Metis 2.1) - Port Themis two-phase workflow to standalone module generation

---

## 4. Recent Wins
> [!NOTE]
> 7 most recent achievements in this codebase

1. **[Metis: Domain Whitelist Configuration System](Metis-MVP.md#4110-domain-whitelist-configuration-system--completed-2025-10-29)** (2025-10-29) - Complete research domain configuration with hierarchical selection (course → arc → module), blacklisting, and whitelist ignoring. DomainSelector (273 lines) for Metis, ResearchConfigSelector (229 lines) for Themis, plus configuration resolution utilities. 2,811 insertions across 46 files. Addresses MVP milestones 2.4, 2.5, 2.6 and unblocks tasks 1.2.1-1.2.5.
2. **[Rhea: Dark Mode Implementation](Rhea-MVP.md#4b1a-add-dark-mode-to-ui--completed-2025-10-29)** (2025-10-29) - User-selectable light/dark/system themes with ThemeSelector component (102 lines) and themeStore (150 lines). Generated dark palette variants for all five workflows. Automatic system preference detection with localStorage persistence. 1,807 insertions across 31 files.
3. **[Themis: Complete Course XML Export](Themis-MVP.md#2-mvp-milestones)** (2025-10-28) - Implemented complete course XML export with embedded module specifications. Generates validatable XML matching courseSchema.xml, validates before download, and provides user feedback via success/error banners. Export button in CourseOverview with automatic validation. Themis MVP now 100% feature-complete. 575 lines across 3 files: outputSerialiser.ts (rewrite), CourseOverview.svelte (+127 lines), documentation.
4. **[Themis: Course XML Schema and Validator](Themis-MVP.md#2-mvp-milestones)** (2025-10-28) - Created comprehensive course XML schema with hierarchical structure (Course → Arcs → Modules → Specifications). Implemented validator with temporal consistency checks, cardinality constraints, and 15 test cases. Validates complete courses including embedded module specifications. 1,652 lines across 4 files: schema template, validator, tests, and documentation.
5. **[Themis: Module Coherence with Overview-First Generation](Themis-MVP.md#412-radically-improve-module-to-module-coherence--completed-2025-10-2728)** (2025-10-27/28) - Implemented two-phase workflow: smart title system (undefined/prompt/literal modes) and lightweight module overviews before full generation. Knowledge context builder tracks learner progression cumulatively, reducing content repetition. Review 10 overviews in ~5min vs 10 full modules in ~20min. 1,479 insertions across 21 files (PR #27).
6. **[British English & Emoji Cleanup](Rhea-MVP.md#4a3-british-english--emoji-cleanup--completed-2025-10-28)** (2025-10-28) - Added British English instructions to all prompt factories (Metis standalone, course-aware, overview; Themis structure). Removed emoji from build scripts. All AI-generated content now uses British spelling/terminology consistently (49 lines changed, zero breaking changes).
7. **[Themis: Component Refactoring Complete](Themis-MVP.md#411-break-over-large-themis-components-into-subcomponents--completed-2025-10-27)** (2025-10-27) - Split 896-line ModuleGenerationList into focused components (ProgressSummary, ModuleCard, ArcSection, ModulePreviewModal) and centralized store utilities. Main component reduced 51%, improved maintainability and testability.
