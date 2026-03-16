# Design: "Reliability" Trust Section for llmock Docs Site

## Summary

Add a new section to the llmock docs site (`docs/index.html`) between "Fixture-driven. Zero boilerplate." (code examples) and "llmock vs MSW" (comparison table). The section explains why users can trust that llmock's response shapes match real provider APIs, and how three-way drift detection keeps it that way.

## Placement

```
Features ("Stop paying for flaky tests")
Code Examples ("Fixture-driven. Zero boilerplate.")
→ NEW: Reliability ("Verified against real APIs. Every day.")
Comparison ("llmock vs MSW")
Claude Code Integration
Real-World Usage
Footer
```

## Section Structure

### Header

- **Section label**: `RELIABILITY`
- **Headline**: "Verified against real APIs. Every day."
- **Description paragraph**: "A mock that doesn't match reality is worse than no mock — your tests pass, but production breaks. llmock runs three-way drift detection that compares SDK types, real API responses, and mock output to catch shape mismatches before you do."

### Triangle Diagram

SVG-based diagram showing three nodes arranged in a triangle:

- **Top center**: "SDK Types" (blue border, `{ }` icon) — "What TypeScript types say the shape should be"
- **Bottom left**: "Real API" (green border, `↔` icon) — "What OpenAI, Claude, Gemini actually return"
- **Bottom right**: "llmock" (purple border, `⚙` icon) — "What the mock produces for the same request"

Dashed connector lines between all three nodes with horizontal labels at each midpoint:

- Left edge: "SDK = Real?"
- Right edge: "SDK = Mock?"
- Bottom edge: "Real = Mock?"

### Diagnosis Cards (3-column grid)

Three cards explaining the possible outcomes:

1. **Red dot — "Mock doesn't match real"**: llmock needs updating — test fails immediately. The SDK comparison tells us why it drifted.
2. **Amber dot — "Provider changed, SDK is behind"**: Early warning — the real API has new fields that neither the SDK nor llmock know about yet.
3. **Green dot — "All three agree"**: No drift — the mock matches reality and the SDK types are current.

Key principle: any mismatch between real API and mock is a failure, regardless of SDK state. The SDK layer diagnoses _why_ drift happened, it doesn't gate severity.

### Drift Report Snippet

Monospace terminal-style block showing `$ pnpm test:drift` output with three distinct examples:

1. `[critical] LLMOCK DRIFT` — missing field (`choices[].message.refusal`: SDK has it, real has it, mock doesn't)
2. `[critical] TYPE MISMATCH` — wrong type (`content[].input`: SDK says object, real says object, mock says string)
3. `[warning] PROVIDER ADDED FIELD` — new field (`choices[].message.annotations`: only real API has it)

Footer line: "2 critical (test fails) · 1 warning (logged) · detected before any user reported it"

### CI Footer

Badge showing "Daily CI" with green dot, text: "Drift tests across 4 providers run automatically every day."

## Styling

All styles must use the site's CSS custom properties (not hardcoded hex):

- Background: `var(--bg-deep)` (page) / `var(--bg-card)` (cards)
- Borders: `var(--border)`
- Text: `var(--text-primary)` (headings) / `var(--text-secondary)` (body) / `var(--text-dim)` (labels)
- Accent: `var(--accent)` (green)
- Uses existing `.section-label`, `.section-title`, `.section-desc` CSS classes
- Section uses `class="reveal"` for scroll-triggered animation
- Triangle diagram uses inline SVG for connector lines

## CI Cadence Change

The drift CI workflow (`.github/workflows/test-drift.yml`) will be updated from weekly (Monday 6am UTC) to daily (6am UTC every day). The cron changes from `0 6 * * 1` to `0 6 * * *`.

DRIFT.md and the site footer text will be updated to say "every day" instead of "every week."

## Files to Modify

| File                               | Change                                                                                                                |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `docs/index.html`                  | Insert new section between code examples and comparison. New CSS for triangle diagram, diagnosis cards, drift report. |
| `.github/workflows/test-drift.yml` | Change cron from `0 6 * * 1` to `0 6 * * *`                                                                           |
| `DRIFT.md`                         | Update schedule references from weekly to daily; update cost estimate in Cost section for daily cadence               |

## Validated Mockup

The approved design is in `.superpowers/brainstorm/84286-1773621431/trust-section-v4.html`.
