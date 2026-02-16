---
layout: page
nav_order: 5
title: Verifications
---

## Conceptual Overview

---

In Reqvire, **verification** is the process of confirming that a system or its components meet specified **requirements**. Verification ensures that the system has been **built right**, checking whether it conforms to the defined functional and non-functional requirements at each level of system decomposition.

Verification activities are integrated into the **requirements structure**, maintaining clear **traceability** between requirements and their associated **verification methods** and **test artifacts**.

## Verification Element Types

---

Reqvire supports several verification element types that align with standard systems engineering verification methods:

### Primary Verification Types
- **verification** - Default verification through testing (equivalent to test-verification)
- **test-verification** - Formal testing with documented test procedures and expected outcomes
- **analysis-verification** - Verification through systematic analysis of documentation or code without physical testing
- **inspection-verification** - Verification through formal examination of documentation, code, or physical components
- **demonstration-verification** - Verification through showing functionality in an operational-like environment

### SatisfiedBy Requirements by Type
- **test-verification**: MUST have `satisfiedBy` relations pointing to actual test implementations
- **analysis-verification, inspection-verification, demonstration-verification**: Do NOT require `satisfiedBy` relations (considered satisfied by default)
- **trace relations**: Always allowed for any verification type

## Coverage Philosophy

---

Reqvire uses a **Verification Roll-up approach**, where verification of detailed requirements provides coverage for their parent requirements through the requirements hierarchy.

### Leaf Requirements Focus

Reqvire's verification coverage focuses on **leaf requirements** - requirements that do not have forward relations to other requirements. These represent the actual testable functionality.

**Preferable Verification Approach:**

The recommended strategy is to **verify leaf requirements** rather than intermediate or parent requirements. This approach provides several key advantages:

- **Verification Roll-up**: When leaf requirements are verified, their verification status automatically rolls up through the traceability chain to cover all parent requirements up to the root
- **Efficiency**: A single verification can verify multiple leaf requirements, providing coverage for entire requirement chains
- **Coverage Completeness**: Verifying the most detailed, specific requirements ensures that all higher-level requirements they derive from are implicitly verified
- **Clear Test Scope**: Leaf requirements represent concrete, testable functionality with well-defined acceptance criteria

**Coverage Rules:**
- **Leaf requirements** MUST be verified - these represent the actual testable functionality
- **Parent/intermediate requirements** MAY be verified but it's not necessary as they are covered through verification of their leaf requirements
- One verification may verify multiple leaf requirements (N:1 relationship), covering entire chains of parent requirements

### Coverage Metrics
The verification coverage system tracks:
- **Verified leaf requirements**: Leaf requirements with `verifiedBy` relations pointing to verification elements
- **Satisfied test-verifications**: Test-verification elements with `satisfiedBy` relations pointing to actual test implementations
- **Unsatisfied test-verifications**: Test-verification elements missing `satisfiedBy` relations (flagged as incomplete)
- **Coverage percentages**: Calculated separately for leaf requirements verification and test-verification satisfaction

## Two-Level Verification System

---

Reqvire implements a two-level verification approach:

1. **Requirements → Verifications**: Requirements link to verification elements via `verifiedBy` relations
2. **Test-Verifications → Test Implementations**: Test-verification elements link to actual test scripts/artifacts via `satisfiedBy` relations

```markdown
### My Requirement
The system shall process data within 500ms.

#### Relations
  * verifiedBy: [Performance Test](tests/PerformanceTests.md#response-time-test)

---

### Performance Test
This test verifies response time requirements.

#### Metadata
  * type: test-verification

#### Relations
  * verify: [My Requirement](Requirements.md#my-requirement)
  * satisfiedBy: [test.sh](../../tests/test-performance/test.sh)
```

### Bidirectional Traceability
Reqvire automatically maintains bidirectional relations:
- When a requirement has `verifiedBy: [Test A]`, the Test A element shows `verify: [Requirement]`
- This ensures complete traceability in both directions

## Verification Coverage

---

Generate coverage reports to track verification status of your requirements.

### Coverage Command

```bash
# Generate coverage report
reqvire coverage

# Generate JSON coverage report for programmatic analysis
reqvire coverage --json
```

The coverage report includes a **verification coverage** section focused on leaf requirements:
- Percentage of verified/unverified leaf requirements
- Breakdown by file
- Breakdown by verification type
- Test-verification satisfaction status

For requirement implementation coverage details, see:
- [Implementation Coverage](implementation_coverage.md)

### Coverage Strategy

The coverage system implements the **verification roll-up approach**:

- **Leaf requirements** MUST be verified - these represent the actual testable functionality
- **Parent requirements** are automatically covered when their leaf requirements are verified
- One verification can verify multiple leaf requirements, providing coverage for entire requirement chains
- **Test-verifications** require `satisfiedBy` relations to actual test implementations
- **Analysis/inspection/demonstration verifications** are considered satisfied by default (no `satisfiedBy` required)

### Coverage Flags

The coverage system will flag:
- ✅ **Satisfied test-verifications**: Those with valid `satisfiedBy` relations to test implementations
- ❌ **Unsatisfied test-verifications**: Those missing `satisfiedBy` relations
- ✅ **Analysis/inspection/demonstration verifications**: Considered satisfied by default (no `satisfiedBy` required)

---
