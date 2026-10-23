# AGENTS.md

## What this repo is

- Spec-only repo: no code yet — just `vegetable_contractor_complete_end_to_end_prompt.md` (v2.6), the single source of truth for a Vegetable Contractor management app (mobile-first, single-user contractor, Telugu-first, ₹).
- **Do NOT generate any app code yet** — this file exists for spec refinement only. Even if the spec looks complete, wait until the user explicitly confirms all requirements are fulfilled and asks to build; until then, work is limited to improving the spec/AGENTS.md, never creating source files.
- No build/test/lint tooling exists yet. Add verified commands to this file as tooling is introduced.

## Read before anything else

- Read the full spec before writing feature code. Every figure in it derives from the canonical dataset in §16 — use §16 as the math acceptance test (e.g., Day 1: cost ₹37,000, revenue ₹43,500, profit +₹6,500; Day 2: profit +₹2,200 at blended ₹31.50/kg; Home pending ₹36,500 = billed pending ₹5,550 + accrued ₹30,950).
- §16.4–§16.6 and §16.9 are marked **Standalone**: decision-flow exercises that never alter the Day 1–2 ledger (§16.2–§16.3) — keep that isolation when editing.
- If §16's canonical numbers ever change, re-derive and update every dependent example (§6.1 Home cards, §6.7 Review analysis, §10 history table, §16.8–§16.9) — the doc contradicts itself otherwise. When amending the spec, also append a change-summary line at the top (the v1→v2.6 pattern is its only changelog).
- §19 is the build checklist/acceptance gate; §17 the screen/state map; §18 the UX state rule (no dead ends; every action gets normal/edit/cancel/error/empty/success/confirmation states).

## Accounting rules agents most often break

- Purchase Rate (market, daily) vs Fixed Tender Rate (per institution/tender) — never one global selling rate; missing price → ask the user flow, never borrow another institution's rate (§2.1, §6.5).
- Never double-count inventory: reused stock's cost is recognized once as Inventory Used Cost; items mixing inventory + fresh purchase allocate at the blended delivered-cost rate (§2.2 f4, §2.3, §8.4); lots go out oldest-first (FEFO).
- History is frozen: confirmed carts store actual rates as immutable snapshots; master-data edits never rewrite transactions (§2.4, §7.3).
- Confirm-then-lock: one daily cart confirmed ONCE for the whole cart (never per item); correctable until end of the calendar day after the transaction (cart date = day started, covers post-midnight confirms), then permanently locked — later fixes are Post-Lock Adjustments or compensating entries, never history edits (§2.8, §2.10, §6.8).
- Correction Mode is a separate cart type that coexists with today's cart; it opens only from Home ("Edit Today's Cart" — today's own transaction only) or Transaction Detail ("Re-open & Correct" — any within-window transaction), never via the Cart tab, which always shows the daily cart (§2.8, §6.1, §10).
- Profit ≠ cash: profit recognized at confirm; payments tracked separately; dues = Billed − Deductions − Received (FIFO); excess collection → institution credit balance; Home pending figure splits Accrued vs Billed (§2.9, §7.5, §16.8).
- On-credit purchases and seller settlements move cash only — never P&L; the cost was recognized at purchase (§9.1, §16.9).
- Rejected/remaining stock is never auto-loss — the contractor chooses inventory vs waste (§2.6); expenses never auto-distribute across institutions — assigned or day-level only (§2.5).
- Non-cart money events (sale/waste/adjustment/settlement) are date-anchored rows that feed daily/monthly P&L on any day — no-cart days or alongside carts — and share the carts' correction window (§10.1, §16.9, §2.8).
- Precision: store exact values; totals are computed from exact values, never summed from rounded displays; fractional quantities ≤ 2 decimals; ₹ with Indian digit grouping e.g. ₹5,00,000 (§2.7).
- Ledger discipline (§2.11): every fact = typed, dated, attributed row; no free-text numbers (reasons come from editable dropdowns); deactivate masters, never delete them — historical ID references must never dangle.

## Design mandates that look optional but aren't

- Telugu default + English secondary everywhere — labels, validation, alerts, memos/bills; search matches English and Telugu item names (§5.4, §6.2).
- Hugeicons — single icon library, Stroke Rounded family only; icons always paired with text; every icon slot assigned per the §17 map (§5.5).
- Numeric keypad (not the OS keyboard) for all money/quantity input; green = profit, red = loss on every surface (§5.1, §5.2).
- Frosted-glass surfaces (app bar, bottom nav, sheets, modals) with a solid fallback where blur is unsupported (§5.1).

## Scope guardrails

- Build the end-to-end app — not a landing page, dashboard, or disconnected mockups; no invented features, no e-commerce/marketplace or ERP idioms; contractor-only: no institution-facing logins, RBAC, or multi-user anything (§1.4, §19).
- §21 AI/roadmap features are out of v1; v1's only obligation is capturing §2.11-shaped ledger data from day one.
- Memos/bills are generated only when the institution asks and shared as screenshot/PDF — never auto-generated or auto-shared (§7.5.5).
