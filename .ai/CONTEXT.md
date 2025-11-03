# AI Agent Context

**Purpose**: Explains the documentation structure for AI agents working in this repository.

**Last Updated**: 2025-11-03

---

## Directory Structure

```
D:\bma\
├── INDEX.md                          # END-USER: Navigation index for business stakeholders
├── docs/                         # END-USER: Detailed business analysis documents
│   ├── *_Business_Architecture.md
│   └── *_Analysis.md
└── .ai/                              # AI AGENTS: Meta content, context, templates
    ├── CONTEXT.md                    # This file - explains structure
    ├── CONTENT_GUIDELINES.md         # How to add new content
    └── TEMPLATES.md                  # Document templates
```

---

## File Roles

### End-User Content (Human Consumption)
- **INDEX.md**: Navigation index with links and brief summaries
- **docs/**: Complete business analysis documents

### AI Agent Meta Content (Machine Consumption)
- **.ai/CONTEXT.md**: Understanding the documentation structure
- **.ai/CONTENT_GUIDELINES.md**: Instructions for adding new content
- **.ai/TEMPLATES.md**: Document structure templates

---

## Key Principles

1. **Separation of Concerns**
   - End-users read: INDEX.md + docs/
   - AI agents reference: .ai/

2. **INDEX.md is End-User Facing**
   - It's a navigation tool, not AI instruction
   - Business stakeholders use it to find information
   - Keep it clean, professional, and business-focused

3. **Artifacts are Detailed Content**
   - Complete business analysis documents
   - Self-contained (can be read standalone)
   - Follow consistent structure

4. **.ai/ is AI Agent Reference**
   - Context about how things work
   - Guidelines for modifications
   - Templates for new content
   - Not visible in end-user documentation

---

## When AI Agents Work Here

**Read First:**
1. `.ai/CONTEXT.md` (this file) - Understand structure
2. `.ai/CONTENT_GUIDELINES.md` - Learn content rules
3. `INDEX.md` - See current end-user navigation
4. Existing artifacts - Study patterns

**Before Making Changes:**
1. Determine if content is end-user or AI meta
2. Follow hierarchy: Business → Strategic → Technical
3. Use toned-down language (no hype)
4. Keep INDEX.md brief (index only)
5. Put detailed content in docs/

**Never:**
- Mix AI instructions into INDEX.md or docs/
- Embed detailed content in INDEX.md
- Put end-user content in .ai/
- Use hyperbolic language or specific percentages

---

## Quick Reference

**Adding new component?**
→ Create `docs/ComponentName_Business_Architecture.md`
→ Add link in INDEX.md under appropriate section
→ See `.ai/CONTENT_GUIDELINES.md` for details

**Updating existing content?**
→ Edit the artifact directly
→ Update INDEX.md summary only if major change

**Adding AI instructions?**
→ Add to `.ai/` directory
→ Never in INDEX.md or docs/

---

<div align="right"><a href="CONTENT_GUIDELINES.md">Next: Content Guidelines →</a></div>
