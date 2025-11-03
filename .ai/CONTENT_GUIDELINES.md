# AI Agent Guidelines: Incorporating New Content

**Purpose**: Instructions for AI agents on how to add new business/product content to this documentation structure.

**Last Updated**: 2025-11-03

---

## Documentation Architecture

### File Structure
```
D:\bma\
├── INDEX.md                          # END-USER: Navigation index
├── docs/                         # END-USER: Detailed business analysis
│   ├── *_Business_Architecture.md
│   └── *_Analysis.md
└── .ai/                               # AI AGENTS: Meta content
    ├── CONTEXT.md                     # Documentation structure
    ├── CONTENT_GUIDELINES.md          # This file - how to add content
    └── TEMPLATES.md                   # Document templates
```

### Roles
- **INDEX.md**: End-user navigation index (not AI instructions)
- **docs/**: End-user detailed business analysis documents
- **.ai/**: AI agent meta content (context, guidelines, templates)

---

## Content Directives

### 1. Tone and Language
- Express metrics in general terms, not specific percentages
- Use toned-down rhetoric (avoid hyperbole, superlatives)
- Focus on facts and proven capabilities
- Example: "significant improvement" NOT "98.7% faster" or "revolutionary breakthrough"

### 2. Content Organization Hierarchy
```
Business/Product Capabilities (TOP)
  ├── What we do (capabilities)
  ├── What products/services we offer
  └── Business value delivered

Strategic Opportunities (MIDDLE)
  └── Future growth, partnerships, expansion

Technical Architecture (BOTTOM)
  ├── How it works (implementation)
  └── Technology stack details
```

### 3. Business Focus First
- Lead with business purpose and value
- Push technical implementation to bottom sections
- Technical details should support business narrative, not lead it

---

## Adding New Content: Step-by-Step

### Step 1: Determine Content Type

**Is this a new component/service?**
→ Create artifact: `docs/ComponentName_Business_Architecture.md`

**Is this additional capability?**
→ Add to existing artifact or INDEX.md Capabilities Index

**Is this strategic/partnership content?**
→ Create artifact in Strategic Opportunities section

### Step 2: Create Artifact (if applicable)

**Template Structure:**
```markdown
# [Component Name] - Business Architecture

**Last Updated**: YYYY-MM-DD

---

## Overview
[Brief business purpose - what problem does this solve?]

---

## Business Capabilities
[What can it do? Focus on business value]

### [Capability Category 1]
[Description in business terms]

### [Capability Category 2]
[Description in business terms]

---

## Product/Service Offerings
[Specific products, services, or integrations]

---

## Business Impact
[Value delivered - revenue, efficiency, risk mitigation, cost]

---

## Technical Integration (bottom section)
[Minimal technical details - architecture, protocols, patterns]

---

<div align="right"><a href="../INDEX.md">↑ Back to Index</a></div>
```

### Step 3: Update INDEX.md Index

**Location in INDEX.md depends on content type:**

**For Business Capability:**
Add under `### Business/Product Capabilities` section:
```markdown
**[Component Name - Business Architecture](docs/ComponentName_Business_Architecture.md)**

Brief one-line summary of business value and key capabilities
```

**For Strategic Opportunity:**
Add under `### Strategic Opportunities` section

**For Technical Detail:**
Add under `### Technical Architecture` section

**Important:** Keep INDEX.md entries brief (1-2 sentences max). Detailed content belongs in artifacts.

---

## Content Review Checklist

Before finalizing new content, verify:

- [ ] Business purpose stated clearly at top
- [ ] Technical details pushed to bottom sections
- [ ] Language is toned-down (no hype, no specific percentages)
- [ ] Detailed content in artifact (not embedded in INDEX.md)
- [ ] INDEX.md has brief summary + link only
- [ ] Proper hierarchy: Business → Strategic → Technical
- [ ] Follows existing artifact formatting patterns
- [ ] Back navigation link included: `<div align="right"><a href="../INDEX.md">↑ Back to Index</a></div>`

---

## Examples

### ✅ Good: Brief Index Entry
```markdown
**[Supplier Integrations - Business Architecture](docs/Supplier_Integrations_Business_Architecture.md)**

13+ payment service provider integrations across mobile services, utilities, bill payments, financial services, fuel rebates, and vouchers. Plugin architecture enables zero-code supplier addition with consistent transaction patterns, error handling, and audit trails
```

### ❌ Bad: Embedded Content
```markdown
## Supplier Integrations

### Payment Service Providers
| Supplier | Products | Business Model |
|----------|----------|----------------|
| ... (50+ lines of content embedded) ...
```

### ✅ Good: Business-First Artifact Structure
```markdown
## Overview
Processes fuel rebate transactions with automated multi-stakeholder distribution...

## Business Capabilities
- Multi-stakeholder payment distribution
- Configurable stake rules
- Wallet-based settlements

## Business Impact
Revenue: Enables new fuel rebate product line
Efficiency: Automated distribution vs manual processing

## Technical Integration (bottom)
- Plugin module within Realtime Switch
- OAuth 2.0 authentication
```

### ❌ Bad: Technical-First Structure
```markdown
## Architecture
Built on .NET 9 with modular plugin architecture...

## Technical Specifications
- OAuth 2.0 client credentials flow
- HTTP REST API with JSON payloads

## Business Use Cases (at bottom)
```

---

## Special Cases

### Adding Supplier Integration
1. Update `docs/Supplier_Integrations_Business_Architecture.md`
2. Add row to Payment Service Providers table
3. Add product category if new type
4. No change needed to INDEX.md (already linked)

### Adding Component
1. Create new artifact: `docs/ComponentName_Business_Architecture.md`
2. Follow template structure (business first, technical last)
3. Add link to appropriate INDEX.md section
4. Consider adding to "Additional Navigation" section for cross-references

### Adding Metrics/Capabilities
1. Update appropriate section in INDEX.md Capabilities Index
2. Use general terms (avoid specific percentages)
3. Include status indicator (✓ Production, Infrastructure, Planned)

---

## Maintenance

### When to Update INDEX.md
- New component/service added (add link)
- Major capability change (update summary)
- Section reorganization (maintain hierarchy)

### When to Update Artifacts
- Business capability changes
- New products/services offered
- Business impact evidence
- Technical implementation changes (bottom section only)

### When NOT to Touch INDEX.md
- Adding detail to existing content → Update artifact instead
- Technical implementation changes → Update artifact only
- Minor capability additions → Update artifact, not index

---

## Questions to Ask Before Adding Content

1. **Is this business/product focused or technical implementation?**
   - Business → Higher in hierarchy
   - Technical → Lower in hierarchy

2. **Does this belong in INDEX.md or artifact?**
   - Summary/link → INDEX.md
   - Detailed content → artifact

3. **What's the business value?**
   - Lead with this, not technical details

4. **Is the language toned down?**
   - Remove hype, specific percentages, superlatives

5. **Does it fit the existing structure?**
   - Follow established patterns
   - Maintain consistency with existing artifacts

---

## Reference Files

For questions about content organization or structure, refer to:
- `.ai/CONTEXT.md` - Documentation structure explanation
- `.ai/TEMPLATES.md` - Document templates
- This file - Content guidelines
- Existing artifacts as examples

---

<div align="right"><a href="CONTEXT.md">← Back to Context</a> | <a href="TEMPLATES.md">Templates →</a></div>
