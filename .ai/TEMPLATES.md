# Document Templates

**Purpose**: Reusable templates for creating new documentation.

**Last Updated**: 2025-11-03

---

## Business Architecture Artifact Template

**Use for**: New components, services, or integrations

**File naming**: `docs/ComponentName_Business_Architecture.md`

```markdown
# [Component Name] - Business Architecture

**Last Updated**: YYYY-MM-DD

---

## Overview

[1-2 paragraphs: What is this? What business problem does it solve?]

---

## Business Capabilities

[What can it do? Focus on business value, not technical implementation]

### [Capability Category 1]
[Description in business terms - revenue, efficiency, compliance, etc.]

### [Capability Category 2]
[Description in business terms]

### [Capability Category 3]
[Description in business terms]

---

## Product/Service Offerings

[Specific products, services, or integrations provided]

| Product/Service | Description | Use Case |
|-----------------|-------------|----------|
| [Name] | [What it does] | [Who uses it and why] |

---

## Business Impact

**Revenue Protection**:
- [Capability that protects/generates revenue]

**Operational Efficiency**:
- [Capability that improves efficiency]

**Risk Mitigation**:
- [Capability that reduces risk]

**Cost Optimization**:
- [Capability that reduces costs]

---

## Operational Characteristics

**Transaction Types**:
- [Type 1]
- [Type 2]

**Security/Compliance**:
- [Standard 1]
- [Standard 2]

**Performance**:
- [Metric 1 in general terms]
- [Metric 2 in general terms]

---

## Technical Integration

[Minimal technical details - only what's necessary for understanding]

**Architecture**: [Brief description]

**Integration Points**: [How it connects to other components]

**Protocols**: [Communication protocols used]

---

<div align="right"><a href="../INDEX.md">↑ Back to Index</a></div>
```

---

## Analysis Artifact Template

**Use for**: Strategic analysis, partnership opportunities, platform evolution

**File naming**: `docs/TopicName_Analysis.md`

```markdown
# [Topic Name] - Analysis

**Last Updated**: YYYY-MM-DD

---

## Executive Summary

[3-5 sentences: What is this analysis about? Key findings? Recommendations?]

---

## Context

[Background information needed to understand the analysis]

### Current State
[What exists today]

### Opportunity
[What could be possible]

---

## Analysis

### [Aspect 1]
[Detailed analysis]

### [Aspect 2]
[Detailed analysis]

### [Aspect 3]
[Detailed analysis]

---

## Business Value

[Quantified or qualified business value - avoid specific percentages unless proven]

---

## Recommendations

1. **[Recommendation 1]**
   - Rationale: [Why]
   - Impact: [Business impact]

2. **[Recommendation 2]**
   - Rationale: [Why]
   - Impact: [Business impact]

---

## Next Steps

- [ ] [Action item 1]
- [ ] [Action item 2]
- [ ] [Action item 3]

---

<div align="right"><a href="../INDEX.md">↑ Back to Index</a></div>
```

---

## INDEX.md Section Template

**Use for**: Adding new section to INDEX.md index

```markdown
### [Section Name]

**[Component Name - Business Architecture](docs/ComponentName_Business_Architecture.md)**

[1-2 sentence summary of business value and key capabilities. Keep it brief - detailed content belongs in the artifact.]
```

---

## Capabilities Index Entry Template

**Use for**: Adding new capability to INDEX.md Capabilities Index

```markdown
| **[Capability Name]** | [Component] | [Description in business terms] | ✓ Production |
```

**Status indicators**:
- `✓ Production` - Live and operational
- `✓ Certified` - Certified/compliant
- `Infrastructure` - Built but not fully operational
- `Planned` - Future capability

---

## Quick Checklist

Before using any template:

- [ ] Business purpose stated first
- [ ] Technical details at bottom
- [ ] Language is toned-down (no hype)
- [ ] Metrics in general terms (no specific percentages)
- [ ] Back navigation link included
- [ ] Last Updated date set
- [ ] Follows existing patterns

---

<div align="right"><a href="CONTEXT.md">← Back to Context</a> | <a href="CONTENT_GUIDELINES.md">Content Guidelines →</a></div>
