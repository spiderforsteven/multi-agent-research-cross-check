# Multi-Agent Research Cross-Check

> Drop a research topic. Get back a cross-reviewed report.

**5-phase pipeline: parallel research → independent cross-review → revision → optional re-verification → final delivery.**

**English** · [中文](./README.zh-CN.md)

Use your agent as a research team. Four independent researchers dig in parallel, none of them aware of what the others wrote. Then a separate reviewer reads every report and scores it across data accuracy, logical consistency, detail richness, and citation authority. Finally, a revision agent fixes every P0–P1 issue in priority order.

Runtime-agnostic — installs on Hermes, Claude Code, Codex, or Cursor. Tool-name translation per runtime lives in the **Runtime Adaptation** section of `SKILL.md`.

## Say this to your agent

```
"Research the competitive landscape for smart planters, using the cross-check workflow."
"Analyze this feature request — give me a contradiction map and a recommendation."
"Do a pricing-strategy analysis of a SaaS competitor, with cross-review."
"Evaluate this new feature request from four separate perspectives."
```

## What it does

| Capability | Deliverable | Typical effort |
|---|---|---|
| Industry / competitive research | Deep report with confidence labels and cited sources | 3–8 agent turns |
| Product requirements analysis | Four-perspective contradiction map + recommendation | 4–6 turns |
| Design-system competitive analysis | Competitor design-pattern summary + design rationale | 3–5 turns |
| Market / business intelligence | Structured conclusion table (High / Medium / Low / Speculative) | 4–8 turns |

## The pipeline

```
┌────────────────────────────────────────────────────────────┐
│                    Topic Decomposition                     │
│            (split into 2-4 independent modules)            │
└────────────────────────────────────────────────────────────┘

                               │
         ┌─────────────────────┼─────────────────────┐
 ┌───────────────┐     ┌───────────────┐     ┌───────────────┐
 │    Agent A    │     │    Agent B    │     │    Agent C    │      <- parallel research, no shared drafts
 │   Module 1    │     │   Module 2    │     │   Module 3    │      <- each agent must cite its sources
 └───────────────┘     └───────────────┘     └───────────────┘
         │                     │                     │
         └─────────────────────┼─────────────────────┘
                               │

           ┌──────────────────────────────────────┐
           │       Independent Cross-Review       │                 <- the key differentiator
           │   separate reviewer, 4 dimensions    │
           └──────────────────────────────────────┘

                               │

           ┌──────────────────────────────────────┐
           │               Revision               │                 <- surgical edits only
           │       fix P0 -> P1 -> P2 -> P3       │
           └──────────────────────────────────────┘

                               │

           ┌──────────────────────────────────────┐
           │      Re-Verification (optional)      │                 <- recommended for critical work
           │  spot-check that P0/P1 fixes landed  │
           └──────────────────────────────────────┘

                               │

           ┌──────────────────────────────────────┐
           │            Final Delivery            │                 <- state what is still unverified
           │  README + version history + limits   │
           └──────────────────────────────────────┘
```

## What makes it different

### Four-dimension cross-review

Ordinary research goes straight from "collect information" to "write the report". This skill inserts an independent review gate in between:

| Dimension | What the reviewer checks | Why it matters |
|---|---|---|
| **1. Data accuracy** | Does every key figure have a source? Do multiple sources agree? Are the market-size / CAGR figures internally consistent? | Stops the agent inventing numbers |
| **2. Logical consistency** | Do conclusions across modules contradict each other? Are the assumptions closed-loop? Does the financial model actually compute (CAC / LTV / churn)? | Catches cross-module breaks |
| **3. Detail richness** | Are major players or segments missing? Is the technical analysis complete? Are edge cases covered? | Prevents shallow analysis |
| **4. Citation authority** | Are sources tier-1 (filings, official docs, industry reports) or second-hand? Do estimates show their derivation? | Prevents citing unreliable sources |

### Confidence labelling

Every conclusion carries a confidence level, so a reader knows which ones to trust:

| Level | Meaning | Typical source |
|---|---|---|
| **High** | Stated explicitly by an official source | Official help centre, SEC filing |
| **Medium** | Supported by an official source but missing detail | Official announcement, press release |
| **Low** | Third-party, user-reported, or indirect only | Forums, user reviews, third-party tests |
| **Speculative** | No direct evidence, or sources conflict | Inference — must be labelled as such |

### Requirements-analysis adaptation

Beyond standard research, the pipeline fits product requirements analysis. Four independent perspective agents:

| Agent role | Focus | Typical sources |
|---|---|---|
| **User perspective** | Real pain points, behaviour, desires | Reddit, support tickets, user interviews |
| **Tech perspective** | Feasibility, BOM cost, architecture | Technical docs, cost models |
| **Business perspective** | Strategic alignment, ROI, pricing | GTM research, internal data |
| **Competitor perspective** | What peers shipped, and why it worked or failed | Competitor teardowns, industry reports |

The core output of cross-review here is **not a report but a contradiction map** — the points where users want something the tech says costs 3× the budget, or where business plans a Q3 launch while competitor data shows two other products launching in Q3. Those contradictions are the decisions the PM actually has to make.

## Anti-AI-slop rules

| 🚫 Symptom | Fix |
|---|---|
| Conclusions with no source | Every key conclusion needs URL + exact quote + access date |
| Estimates with no derivation | State the input data, the formula, and the result |
| Cross-module contradictions ignored | The reviewer must compare the same metric across all modules |
| "Industry common knowledge" used instead of data | Label Speculative, or downgrade to Medium-low |
| Financial figures given as bare numbers | The reviewer must recompute the formula |

**Pre-delivery checklist:**

1. Does every conclusion carry a confidence level (High / Medium / Low / Speculative)?
2. Are all P0 / Critical issues fixed *and verified*?
3. Are cross-module contradictions listed and resolved?
4. Are gaps honestly labelled "no direct source"?
5. Does the README state known limitations and data-quality caveats?

AI slop is the kind of report that reads plausibly until you check a number — all filler, no verifiable anchor.

Guarding against it is not an aesthetic preference. It is what lets you know which parts of a research result you can act on, and which you cannot.

## Limitations

- **Not a substitute for primary research.** Output is analysis of public information; it does not replace user interviews, field work, or experimental data.
- **Cross-review quality depends on the underlying model.** Complex financial models still warrant human review.
- **Overkill for single-fact lookups.** If you only need one fact ("what is company X's valuation"), do not run this pipeline.
- **Multiple-module contradictions can be missed.** With more than four sub-modules, the reviewer's cross-module consistency check may be incomplete.

## Repository structure

```
multi-agent-research-cross-check/
  ├── SKILL.md                      # Skill definition (includes the Runtime Adaptation mapping)
  ├── README.md                     # This file (English)
  ├── README.zh-CN.md               # Chinese README
  └── references/
       └── research-output-confidence-template.md
```

## About

This skill addresses a structural problem with single-agent research: one agent searching alone tends to produce conclusions that sound reasonable but do not survive follow-up questions. Inserting an independent cross-review gate into a standard research flow makes the agents check each other, which raises output quality.

The core design principle: **quality comes not from a smarter agent, but from agents holding each other accountable.**
