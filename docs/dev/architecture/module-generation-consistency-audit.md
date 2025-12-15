# Module Generation Consistency Audit

**Date:** 2025-12-15
**Status:** Complete
**Type:** Architecture Decision Record (ADR)

---

## Context

After October 2025 refactoring and rapid feature development, we conducted a system-wide consistency audit to ensure module generation remains unified across Metis (standalone) and Themis (course-aware) workflows. This audit verifies schema alignment, identifies code duplication, and validates architectural patterns.

---

## Executive Summary

**Overall Finding:** ✅ Architecture is **fundamentally sound** with proper code reuse patterns established.

**Key Strengths:**
- Module generation logic properly unified via shared utilities
- Prompt construction uses composable shared components
- SSE streaming, retry logic, and validation centralized
- Both workflows use identical generation pipeline

**Issues Identified:**
1. ⚠️ **Module validation logic duplicated** in courseValidator (should reuse moduleValidator)
2. ⚠️ **Schema documentation divergence** (minor comment differences between schemas)
3. ℹ️ **Type definitions** across Zod and TypeScript could be more aligned

---

## Audit Findings

### 1. Schema Consistency ✅ **ALIGNED**

**Files Examined:**
- `src/data/templates/metis/outputSchema.xml` (173 lines)
- `src/data/templates/themis/courseSchema.xml` (lines 181-328: `<ModuleSpecification>`)

**Finding:**
Schemas are **structurally identical** with expected differences:

| Aspect | Metis outputSchema.xml | Themis courseSchema.xml | Status |
|--------|----------------------|----------------------|--------|
| **Root element** | `<Module>` | `<ModuleSpecification>` | ✅ Expected |
| **Child structure** | Metadata → ModuleOverview → ResearchTopics → Projects → AdditionalSkills → Notes | Identical | ✅ Aligned |
| **Cardinality** | Min 3 objectives, 5 topics, 2 briefs | Identical | ✅ Aligned |
| **Comments** | "Quick memorable name" | "Memorable name" | ⚠️ Minor |

**How it works:**
1. Modules generated with `<Module>` root via both Metis and Themis
2. Course serializer (`outputSerialiser.ts:59`) calls `extractModuleSpecificationContent()`
3. Function strips `<Module>` wrapper, preserves inner content
4. Content wrapped in `<ModuleSpecification>` tags for course XML

**Code Evidence:**
```typescript
// outputSerialiser.ts line 410-415
if (module.moduleData?.xmlContent && module.status === 'complete') {
    lines.push('              <ModuleSpecification>');
    const moduleSpecContent = extractModuleSpecificationContent(module.moduleData.xmlContent);
    const indentedContent = indentXml(moduleSpecContent, 8);
    lines.push(indentedContent);
    lines.push('              </ModuleSpecification>');
}
```

**Decision:** ✅ Schema relationship is **correct by design**. Course schema intentionally embeds Metis module schema.

**Recommendation:**
- Add XML comment in courseSchema.xml referencing metis/outputSchema.xml
- Standardize comment wording between schemas (cosmetic improvement)

---

### 2. Code Duplication Analysis

#### 2.1 Generation Pipeline ✅ **PROPERLY UNIFIED**

**Files Examined:**
- `src/routes/api/metis/update/+server.ts`
- `src/routes/api/themis/module/+server.ts`

**Finding:** Zero duplication in generation logic.

**Evidence:**
Themis endpoint converts course-aware request to Metis format, then reuses entire pipeline:

```typescript
// themis/module/+server.ts line 72-80
const metisBody = convertToMetisFormat(body);

return createSSEStream({
    body: metisBody,
    model,
    maxRetries: 3,
    promptBuilder: (requestBody, validationErrors) => {
        return buildCourseAwareModulePrompt(/* ... */);
    }
});
```

**Shared Utilities:**
- ✅ `sseHandler.ts` - SSE streaming (150 lines)
- ✅ `retryHandler.ts` - Retry orchestration
- ✅ `agentClientFactory.ts` - Claude client creation
- ✅ `responseParser.ts` - XML extraction/cleaning
- ✅ `domainResolver.ts` - Domain configuration resolution

#### 2.2 Prompt Construction ✅ **COMPOSABLE & REUSABLE**

**Files Examined:**
- `src/lib/factories/prompts/metisPromptFactory.ts`
- `src/lib/utils/prompt/shared-components.ts`

**Finding:** Proper composition pattern with zero duplication.

**Architecture:**
```
shared-components.ts (191 lines)
├── buildResearchInstructions()
├── buildRetrySection()
├── buildBritishEnglishInstructions()
├── formatInputData()
└── [6 other reusable functions]

metisPromptFactory.ts
├── buildGenerationPrompt() ────> standalone Metis
└── buildCourseAwareModulePrompt() ────> calls buildGenerationPrompt(), injects course context
```

**Code Evidence:**
```typescript
// metisPromptFactory.ts line 215-216
export function buildCourseAwareModulePrompt(...) {
    const basePrompt = buildGenerationPrompt(body, validationErrors);
    // Insert course context after </Overview>
    return basePrompt.replace('</Overview>', `</Overview>\n${courseContextSection}`);
}
```

**Decision:** ✅ Prompt architecture is **exemplary**. Course-aware generation wraps standalone generation with additive context.

#### 2.3 Validation Logic ⚠️ **DUPLICATION FOUND**

**Files Examined:**
- `src/lib/schemas/moduleValidator.ts` (501 lines, 7 validation functions)
- `src/lib/schemas/courseValidator.ts` (641 lines, 9 validation functions)

**Problem:**
`courseValidator.ts` has its own `validateModuleSpecification()` function (lines 515-600+) that duplicates module validation logic instead of reusing `validateModuleXML()`.

**Current State:**
```typescript
// courseValidator.ts line 8 - imports but doesn't use!
import { validateModuleXML } from './moduleValidator';

// courseValidator.ts line 515 - reimplements validation
function validateModuleSpecification(spec: Element, context: string, errors: string[], warnings: string[]): void {
    // Manually validates:
    // - ModuleOverview (min 3 objectives)
    // - ResearchTopics (min 5 primary topics)
    // - Projects (min 2 briefs, 3 skills, 3 examples)
    // - AdditionalSkills (min 1 category, 1 skill)
    // ... ~85 lines of duplicate validation logic
}
```

**Impact:**
- Maintenance burden: Changes to module validation rules must be made in two places
- Inconsistency risk: Validators could drift over time
- **However:** Both validators currently enforce identical rules (min 3 objectives, min 5 topics, etc.)

**Recommendation:** 🔧 **Refactor to reuse**

**Proposed Fix:**
```typescript
// courseValidator.ts line 515
function validateModuleSpecification(
    spec: Element,
    context: string,
    errors: string[],
    warnings: string[]
): void {
    // Wrap module spec content in <Module> tags temporarily
    const wrappedXml = `<Module>${spec.innerHTML}</Module>`;

    // Reuse moduleValidator
    const result = validateModuleXML(wrappedXml);

    // Prefix errors with context
    errors.push(...result.errors.map(e => `${context}: ${e}`));
    warnings.push(...result.warnings.map(w => `${context}: ${w}`));
}
```

**Benefits:**
- Single source of truth for module validation
- Automatic consistency as validation rules evolve
- ~85 lines of code eliminated

**Risk:**
- Low - both validators currently identical
- Testing required to ensure context strings format correctly

---

### 3. Type Safety Audit ✅ **CONSISTENT**

**Files Examined:**
- `src/lib/schemas/apiValidator.ts` (Zod schemas)
- `src/lib/types/themis.ts` (TypeScript interfaces)
- `src/lib/types/theia.ts` (TypeScript interfaces)

**Finding:** Type definitions properly aligned.

**Key Types:**

| Purpose | Zod Schema | TypeScript Type | Alignment |
|---------|------------|-----------------|-----------|
| Metis generation | `GenerateRequestSchema` | `GenerateRequest` | ✅ z.infer |
| Themis generation | `ModuleGenerationRequestSchema` | `ModuleGenerationRequest` | ✅ z.infer |
| Module slot | `ModuleSlotSchema` | `ModuleSlot` | ⚠️ Partial overlap |
| Domain config | `DomainConfigSchema` | `DomainConfig` | ✅ z.infer |

**Minor Issue: ModuleSlot vs ModuleSlotSchema**

`ModuleSlot` (themis.ts) has additional fields not in Zod schema:
- `titleInput: TitleInput` (undefined/prompt/literal)
- `themeInput?: TitleInput`
- `overview?: ModuleOverview`
- `moduleData?: { xmlContent: string }`

`ModuleSlotSchema` (apiValidator.ts) is **subset** used for API transmission.

**Analysis:** ✅ This is **correct by design**
- Full `ModuleSlot` type used in UI state (Themis stores)
- Slimmed `ModuleSlotData` sent to API (removes client-side fields)
- Prevents sending unnecessary data over wire

**Decision:** Accept as-is. This is proper API boundary design.

---

## Recommendations

### Priority 1: Eliminate Validation Duplication (Medium Priority)

**Action:** Refactor `courseValidator.ts` to reuse `validateModuleXML()`

**Steps:**
1. Create wrapper function to adapt `<ModuleSpecification>` → `<Module>`
2. Call `validateModuleXML(wrappedXml)`
3. Map errors/warnings with context prefix
4. Add regression tests
5. Remove ~85 lines of duplicate code

**Effort:** 2-3 hours
**Risk:** Low
**Benefit:** Single source of truth, reduced maintenance

### Priority 2: Schema Documentation Alignment (Low Priority)

**Action:** Add cross-references between schema files

**Changes:**
```xml
<!-- courseSchema.xml line 180 -->
<!-- ==========================================
     MODULE SPECIFICATION: Full Metis-style content
     Structure matches: src/data/templates/metis/outputSchema.xml
     Root <Module> tags stripped during serialization
     ========================================== -->
<ModuleSpecification>
```

```xml
<!-- metis/outputSchema.xml line 1 -->
<!--
  Rhea Module Specification Schema

  This schema defines the structure for standalone module generation (Metis).
  When embedded in courses (Themis), the <Module> root is replaced with
  <ModuleSpecification> wrapper. See: courseSchema.xml
-->
<Module>
```

**Effort:** 15 minutes
**Benefit:** Clearer architecture documentation

### Priority 3: Add Regression Tests (Medium Priority)

**Action:** Create automated tests to prevent schema drift

**Test Suite:**
```typescript
// Test 1: Verify identical module validation
test('Metis and Themis modules pass identical validation', () => {
    const metisModule = generateViaMetis(testInputs);
    const themisModule = generateViaThemis(testInputs);

    const metisSpec = extractSpecificationContent(metisModule);
    const themisSpec = extractSpecificationContent(themisModule);

    expect(metisSpec).toEqualIgnoringWhitespace(themisSpec);
});

// Test 2: Verify schema structural equality
test('ModuleSpecification matches Module schema', () => {
    const moduleSchema = parseSchema('metis/outputSchema.xml');
    const specSchema = parseSchema('courseSchema.xml', 'ModuleSpecification');

    expect(specSchema.children).toEqual(moduleSchema.children);
});
```

**Effort:** 4-6 hours
**Benefit:** Prevent future drift, CI/CD confidence

---

## Conclusions

### Architecture Health: ✅ **EXCELLENT**

The October 2025 refactoring successfully established:
- Unified generation pipeline (zero duplication)
- Composable prompt factories (proper reuse)
- Centralized utilities (SSE, retry, validation, domain resolution)
- Type-safe API boundaries (Zod schemas throughout)

### Schema Alignment: ✅ **CORRECT BY DESIGN**

Metis `<Module>` and Themis `<ModuleSpecification>` are intentionally identical in content, differing only in root element wrapper. Serialization logic correctly handles transformation.

### Code Reuse: 🟡 **MOSTLY EXCELLENT, ONE ISSUE**

- Generation logic: ✅ Fully unified
- Prompt building: ✅ Exemplary composition
- Validation: ⚠️ One duplication (courseValidator should reuse moduleValidator)
- Type system: ✅ Properly aligned

### Confidence Level: **HIGH**

We can confidently state:
1. Modules generated via Metis and Themis are structurally identical
2. All generation logic flows through shared utilities
3. Schema relationship is properly documented and intentional
4. Type system enforces consistency at API boundaries

**One remediation needed:** Eliminate validation duplication in courseValidator.

---

## Next Steps

1. **Immediate:** Document schema relationship in XML comments (15 min)
2. **Short-term:** Refactor courseValidator to reuse moduleValidator (2-3 hours)
3. **Medium-term:** Add regression test suite (4-6 hours)
4. **Ongoing:** Monitor for drift as features evolve

---

## Appendix A: File Size Comparison

| Component | Lines | Notes |
|-----------|-------|-------|
| **Schemas** |
| metis/outputSchema.xml | 174 | Module structure |
| courseSchema.xml | 548 | Course + embedded modules |
| **Validators** |
| moduleValidator.ts | 501 | 7 validation functions |
| courseValidator.ts | 641 | 9 functions (1 duplicates moduleValidator) |
| **Generation** |
| metisPromptFactory.ts | ~400 | 3 prompt builders |
| shared-components.ts | 191 | 10 reusable functions |
| sseHandler.ts | 150 | SSE streaming |
| retryHandler.ts | ~100 | Retry orchestration |
| agentClientFactory.ts | ~150 | AI client creation |

**Total generation infrastructure:** ~1,540 lines (highly reusable)

---

## Appendix B: Generation Flow Diagram

```
┌─────────────────────────────────────────────────────┐
│                    USER INPUT                       │
├─────────────────────┬───────────────────────────────┤
│                     │                               │
│   METIS WORKFLOW    │   THEMIS WORKFLOW             │
│   (Standalone)      │   (Course-aware)              │
│                     │                               │
└─────────┬───────────┴───────────────────┬───────────┘
          │                               │
          │ XML files +                   │ Course context +
          │ Structured input              │ Module slot data
          │                               │
          ▼                               ▼
   ┌──────────────┐              ┌──────────────────┐
   │ /api/metis/  │              │ /api/themis/     │
   │ update       │              │ module           │
   └──────┬───────┘              └─────────┬────────┘
          │                                │
          │                                │
          │ GenerateRequest                │ ModuleGenerationRequest
          │                                │
          │                                ▼
          │                       ┌─────────────────────┐
          │                       │ convertToMetisFormat()│
          │                       └──────────┬──────────┘
          │                                  │
          │                                  │ GenerateRequest
          │                                  │
          ▼◄─────────────────────────────────┘
   ┌─────────────────────────────────────────┐
   │     SHARED GENERATION PIPELINE          │
   ├─────────────────────────────────────────┤
   │ 1. createSSEStream()                    │
   │ 2. promptBuilder()                      │
   │    ├─ buildGenerationPrompt()           │
   │    └─ OR buildCourseAwareModulePrompt() │
   │       (wraps base + adds course context)│
   │ 3. withRetry()                          │
   │    ├─ Create AI client                  │
   │    ├─ Apply domain config               │
   │    ├─ Stream generation                 │
   │    └─ Validate + retry if needed        │
   │ 4. validateModuleXML()                  │
   │ 5. Return SSE stream                    │
   └─────────────────┬───────────────────────┘
                     │
                     ▼
           ┌──────────────────────┐
           │  <Module>...</Module> │
           │  (Identical structure)│
           └─────────┬─────────────┘
                     │
         ┌───────────┴────────────┐
         │                        │
         ▼                        ▼
    METIS OUTPUT          THEMIS SERIALIZE
    Download XML          ├─ Extract content
                          ├─ Wrap in <ModuleSpecification>
                          └─ Embed in course XML
```

**Key Insight:** Both workflows converge to identical generation pipeline. Course-aware generation is **additive** (injects context) not **duplicative**.
