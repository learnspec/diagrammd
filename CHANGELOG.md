# Changelog

All notable changes to the DiagramMD specification are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/).

---

## [0.2] — 2026-08-25

### Added
- Companion `anim` blocks: a catalogue entry MAY carry a sibling ```anim for:<id>``` fence attaching an AnimMD step-reveal script (embedding defined normatively in the AnimMD spec, §Embedding in Host Formats). One block per entry, later duplicates ignored, orphan blocks preserved by parsers, companion removed with its entry.
- Lenient-mode validation rules for companion blocks (orphan `for:`, duplicate block, script failing AnimMD grammar validation).

### Note
- The v0.2 draft predates this changelog entry; earlier v0.2 additions (per-type sections, `stock.diagram.md` default-reference convention) are documented in SPEC.md only.

## [0.1] — 2026-05-10

### Added
- Initial draft of the DiagramMD specification as part of the LearnSpec suite.
