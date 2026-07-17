# Contributing to AI Home OS

Thank you for your interest in contributing. This is an open engineering specification and community-built project. All contributions are welcome — from fixing a typo to proposing a full architectural redesign.

---

## Before You Start

1. Read the [Table of Contents](AI%20Home%20OS%20%E2%80%94%20Table%20of%20Contents.md) to understand the full scope
2. Browse [open issues](https://github.com/donzeg/AI-Home-OS/issues) to see what's being discussed
3. Check [GitHub Discussions](https://github.com/donzeg/AI-Home-OS/discussions) for ongoing debates

---

## Types of Contributions

| Type | Description |
|------|-------------|
| **Chapter authoring** | Write or co-write a specification chapter |
| **Architecture review** | Challenge or improve a design decision |
| **Diagrams** | Create or improve Mermaid diagrams |
| **Prototype code** | Implement a proof-of-concept module |
| **Hardware testing** | Test sensors/hardware and report findings |
| **Security review** | Audit the architecture for risks |
| **Translation** | Translate chapters to other languages |

---

## Branch & Commit Conventions

**Branch naming:**
```
chapter/02-sensor-layer
architecture/memory-graph-redesign
prototype/context-engine-poc
fix/ch03-frigate-gpu-specs
diagram/network-topology-update
docs/improve-readme
```

**Commit format (Conventional Commits):**
```
feat(ch02): add mmWave sensor placement guidelines
fix(ch03): correct Frigate GPU VRAM requirements
arch(memory): propose Neo4j knowledge graph schema
docs(readme): add community discussion section
```

---

## Quality Standards

Every chapter contribution should include:

- [ ] Overview section
- [ ] Architecture section with Mermaid diagrams
- [ ] Design Decisions (explain *why*, not just *what*)
- [ ] Trade-offs (acknowledge alternatives)
- [ ] Hardware Recommendations (with part numbers where possible)
- [ ] Software Recommendations (with versions)
- [ ] Best Practices
- [ ] Risks
- [ ] Future Improvements
- [ ] References

---

## Style Guide

- Write as if this is an internal engineering document for a company building this commercially
- Be specific — cite part numbers, model names, version numbers
- Acknowledge trade-offs honestly
- Use tables for comparisons
- Use Mermaid for all diagrams (no image files)
- Use present tense: "The system stores..." not "The system will store..."
- Avoid marketing language

---

## Code of Conduct

- Be respectful and constructive
- Engineering debate is welcome; personal attacks are not
- All experience levels are welcome
- Credit sources and prior art

---

## Questions?

Open a [GitHub Discussion](https://github.com/donzeg/AI-Home-OS/discussions) — we're happy to help you get started.
