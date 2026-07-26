---
name: multi-agent-research-cross-check
description: >
  Multi-Agent research workflow with independent cross-check / argue (互查辩论) phase.
  Designed for industry research, competitive analysis, market intelligence, and data-heavy
  investigations where accuracy and logical consistency are paramount.
  Follows a 5-phase pipeline: parallel research → independent review → revision →
  optional re-verification → final delivery.
tags: [research, multi-agent, cross-check, quality-assurance, critique]
---

# Multi-Agent Research with Cross-Check (互查辩论)

## When to Use

Default to this workflow for **any research-oriented task** unless the user explicitly asks for a quick/single-fact lookup.

Examples:
- Industry/competitive landscape analysis
- Market sizing and business model teardowns
- Multi-dimensional intelligence reports
- Data-heavy deliverables that must survive scrutiny
- Competitor policy / terms teardowns

Do NOT use for simple, single-fact lookups or low-stakes summaries **only when the user explicitly opts out**.

> **Quality Gate**: For this user, every research task must produce conclusions with **cited data sources** (URL + exact quote) and **confidence levels** (High / Medium / Low / Speculative). If a conclusion has no direct evidence, either refuse to answer it or clearly label it as speculation.

## The 5-Phase Pipeline

### Phase 1: Parallel Research (并行研究)

1. **Decompose the topic** into 2-4 independent sub-modules.
   - Example: Market Landscape / Business Models / Emotional Value / Tech Stack
2. **Create a structured directory** first:
   ```
   /project/
   ├── 00-project-overview/
   ├── 01-module-a/
   ├── 02-module-b/
   ├── 03-module-c/
   └── 04-data-sources/
   ```
3. **Launch parallel research agents** (up to 3 via `delegate_task` batch mode).
   - Each agent writes structured Markdown to its assigned directory.
   - Mandate: every key data point must cite a source. Estimates must show derivation logic.

### Phase 2: Independent Cross-Review (独立交叉审查)

This is the **critical differentiator**. Do not skip.

1. **Create a `05-review-reports/` directory**.
2. **Launch independent review agents** — they must NOT be the same agents that wrote the drafts.
   - Each review agent reads one module's files + the project README + references.
   - For modules with heavy cross-dependencies, the review agent should also read relevant files from other modules.
3. **Review across 4 dimensions** (四维度审查):

   | Dimension | Questions to Ask |
   |-----------|------------------|
   | **1. Data Accuracy** | Does every key stat have a source? Are there multi-source validations? Are market sizes/CAGRs internally consistent? |
   | **2. Logical Consistency** | Do conclusions across files contradict each other? Are assumptions closed-loop? Do financial models (CAC, LTV, churn) math-check? |
   | **3. Detail Richness** | Are major players or segments missing? Are technical analyses complete? Are edge cases covered? |
   | **4. Citation Authority** | Are sources tier-1 (industry reports, SEC filings, official sites)? Are estimates labeled as such with derivation shown? |

4. **Output structured critique reports** to `05-review-reports/`:
   - Severity ratings: **Critical / Major / Minor / Suggestion**
   - File name + line number for each issue
   - Specific fix recommendations
   - Overall score and prioritized fix list (P0–P3)

### Phase 3: Revision (修订)

1. **Launch revision agents** (one per module, or one per critical cross-cutting issue).
   - Each agent reads the original files + the critique report for its module.
2. **Fix priority order**: P0 (Critical) → P1 (Major) → P2 (Minor) → P3 (Suggestion).
3. **Use `patch` for surgical edits**, not full file overwrites.
4. **Cross-module consistency fixes** are highest priority:
   - Same brand/person/number must be identical across all files.
   - Use `search_files` to find all occurrences before editing.

### Phase 4: Re-Verification (Optional but Recommended)

For mission-critical deliverables:
- Launch a brief "spot-check agent" to verify that P0/P1 fixes were actually applied.
- Check that cross-module contradictions are resolved.
- If the revision agent hit max iterations and failed, **manually inspect and finish the fixes**.

### Phase 5: Final Delivery (汇总交付)

1. **Update the project README** with:
   - Final directory structure
   - Version history (v1.0 = initial research, v1.1 = post-review revision)
   - Key conclusions summary
   - Known limitations / data quality caveats
2. **Deliver to user** with a clear summary of:
   - What was produced
   - What issues were found and fixed
   - What remains as "estimated / to-be-validated"

## Pitfalls & Lessons Learned

| Pitfall | Mitigation |
|---------|------------|
| **Revision agents hit iteration limits** | Plan for manual follow-up. The human operator should verify P0 fixes and apply any remaining patches. |
| **Cross-module contradictions survive** | The review agent for module A should always skim module B if they share brands/metrics. Explicitly assign "cross-module consistency" as a review task. |
| **Citation footnotes in text but missing in references** | The revision agent should update `references.md` alongside the body text. Check this in Phase 4. |
| **Math errors in financial models** | Review agents must actually recalculate (CAC from CPM/CTR/CVR, churn vs retention rates). Do not trust the original agent's arithmetic. |
| **Over-reliance on "industry common knowledge"** | Flag unsourced claims as Major issues. Replace with tier-1 sources or downgrade to "Agent estimate (based on X analogy)". |

## Directory Template

```
/project-root/
├── 00-project-overview/
│   └── README.md              # Master index + exec summary
├── 01-[topic-a]/
│   └── *.md                   # Research reports
├── 02-[topic-b]/
│   └── *.md                   # Research reports
├── 03-[topic-c]/
│   └── *.md                   # Research reports
├── 04-data-sources/
│   └── references.md          # Living bibliography + footnote registry
└── 05-review-reports/
    └── review-01-*.md         # Critique reports (permanent record)
```

For deliverables that are primarily a **concise answer with citations** (e.g., competitor policy teardowns), use the condensed output format in `references/research-output-confidence-template.md`.

## User Preference Integration

If the user says **"当前任务简单做"** or similar in a specific session, you may simplify the workflow (skip Phase 2–4), but you **must label the output as "未经交叉审查，仅供参考"**.

## Adaptation for Product Requirements Analysis (需求分析)

The cross-check pipeline adapts naturally to **product requirements analysis**, where the goal is to evaluate a feature request or product decision from multiple independent perspectives before committing to a direction. This adaptation is used by Steven (MayGrove/Sino-Well Product Director) as his core requirements analysis framework.

**Domain-specific agents** (instead of research sub-modules):

| Agent Role | Focus Area | Typical Questions |
|------------|-----------|------------------|
| **User Perspective** (用户视角) | Real user pain points, behaviors, and desires — sourced from Reddit, customer support logs, user interviews | What are users actually saying? What workarounds do they invent? What would they pay for? |
| **Tech Perspective** (技术视角) | Feasibility, BOM cost, architecture, performance ceilings, integration complexity | Can we build this? What does it cost? What breaks if we do? |
| **Business Perspective** (商业视角) | Strategic alignment, ROI, pricing risk, timing, organizational capacity | Does this move a needle this quarter? Is the market ready? Can we sell it? |
| **Competitor Perspective** (竞品视角) | Competitive landscape, what peers shipped and why they succeeded or failed | Who else did this? Did they ship it or kill it? Why? |

**Pipeline adaptations**:
- **Phase 1 (Parallel Research)**: Launch 4 independent agents — one per perspective. Each agent searches its domain independently (Reddit + support data for user, BOM tools for tech, GTM research for business, competitive teardowns for competitor). Agents do NOT share drafts before Phase 2.
- **Phase 2 (Cross-Review)**: A review agent reads all 4 perspective reports and identifies contradictions:
  - *User wants X, but tech says it costs 3x the budget*
  - *Business says launch in Q3, but competitor data shows 2 other products launching at the same time*
  - The most valuable output of cross-review is the **contradiction map** — these are the decisions the PM actually needs to make.
- **Phase 3 (Revision)**: Resolve contradictions. Options: split feature into phases, change pricing strategy, kill the feature, or gather more data.
- **Phase 5 (Final Delivery)**: A one-page decision summary with: what we know, what we don't know, the key contradiction, and the recommended decision path.

**Key difference from research**: The output is a **decision recommendation**, not a research report. The entire pipeline exists to surface the contradictions that the human PM needs to resolve.

**案例 (real usage)**: MayGrove ambient light requirements analysis (2026-07) — 4 agents + cross-review concluded that independent RGB mood lighting was a pseudo-need, while the real opportunity was dimmable/bedtime-mode grow lights. The decision went directly into product roadmap.

## Adaptation for Design System / Competitive Analysis Research

The same cross-check pipeline can be applied to design system research and competitive analysis, with these adaptations:

- **Phase 1 (Parallel Research)**: Launch agents to analyze different competitor sites simultaneously (e.g., Agent A → ChargePoint, Agent B → Tritium, Agent C → Wallbox). Each extracts: color palette, typography, navigation structure, CTA patterns, trust signals.
- **Phase 2 (Cross-Review)**: A review agent compares findings across all competitor analyses to identify:
  - **Data Accuracy**: Are extracted hex codes correct? (Verify with `browser_console`)
  - **Logical Consistency**: Do patterns hold across all competitors? (e.g., "All US EV charging sites use green/blue, no gold")
  - **Detail Richness**: Are navigation patterns, button styles, and spacing systems fully documented?
  - **Citation Authority**: Are colors from live computed styles, not assumed?
- **Phase 3 (Revision)**: Synthesize findings into actionable design decisions (e.g., "Replace gold with brand blue #047acc based on 0/5 US competitors using gold")
- **Output**: A design system reference document (see `frontend-theme-migration/references/us-ev-charging-b2b-design-patterns.md` for an example)

**Key difference from market research**: Design analysis relies heavily on `browser_navigate` + `browser_console` for live token extraction, not just web search. The review agent should verify that extracted colors/fonts were actually read from computed styles, not guessed.