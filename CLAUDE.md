# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A content repository of generated paramedic training scenarios for the MATC (Madison Area Technical College) paramedic program. There is no build, test, or lint step — the repo only holds scenario artifacts. Scenarios are produced by the `scenario-generator` skill (importing new protocol documents lives in a separate `protocol_loader` project, not here).

## Layout

Two parallel scenario collections, each a flat directory of scenario folders:

- `sim-lab/` — sim-lab scenarios named `<Cat>-T<Tier>-<NN>` (e.g. `C-T1-01`, `R-T3-04`). Category letters: `B` Behavioral, `C` Cardiac, `M` Medical, `P` Pediatric, `R` Respiratory. `T1`/`T2`/`T3` is the difficulty tier (beginner → intermediate → advanced); `NN` is a two-digit sequence within that category+tier.
- `main-lab/` — main-lab scenarios named `<NNN>-<short-name-slug>` (e.g. `013-isolated-tib-fib-fracture`, `055-anterior-stemi`). `NNN` is a zero-padded sequential number across the whole collection — numbers only, no class codes or letters. The directory name, `meta.id` in `unified.json`, and `scenarioId` in `realiti.json` are always the same string, and `meta.name` is the matching `"<NNN> - <Short Name>"` (e.g. `"013 - Isolated Tib/Fib Fracture"`). A new scenario takes the next unused number.

Every scenario folder contains exactly four files:

- `unified.json` — the canonical source data for the scenario (see schema below). Treat this as the source of truth; the other three are rendered from it.
- `realiti.json` — import file for the REALITi patient-monitor simulator (`scenarioStory`, `patientInformation`, `labs`, `scenarioEvents`, `checklist`, `media`).
- `index.html` — standalone (~1.6 MB, self-contained, no external assets) interactive scenario player for facilitators.
- `printable.html` — printable instructor handout / facilitator script.

## `unified.json` schema

Top-level keys: `meta`, `patient`, `scene`, `phases`, `realiti`.

- `meta` — `id`, `name`, `version`, `difficulty`, `category`, `protocols` (list of protocol IDs the scenario exercises), `totalTimeSeconds`, `createdAt`, `tags`.
- `patient` — demographics, weight (kg + lbs), `chiefComplaint`, and `history` (HPI, past medical, meds, allergies, last oral intake, events).
- `scene` — `dispatch`, `location`, `time`, `safety`, `bystanders`, `visualCues`.
- `phases` — ordered list of scenario states; each phase has `id`, `name`, `description`, `isDefault`, `clinicalPresentation`, `monitorState` (vitals/waveforms shown on the monitor), `expectedActions` (protocol-referenced steps), and `transitions` (what moves the patient to another phase).
- `realiti` — REALITi-specific monitor config (`scenarioMonitorType`, `scenarioDefaultEnergy`, `scenarioDefaultPacerThreshold`).

## Working in this repo

- Don't hand-edit `index.html`, `printable.html`, or `realiti.json` — they are generated artifacts. To change a scenario, regenerate it with the `scenario-generator` skill so all four files stay in sync.
- Git history convention: one scenario directory added (or removed) per commit, with a message like `Add <id> scenario` / `Delete <id> directory`.
- Remote: `origin` → `https://github.com/matc-ems/scenarios.git`, default branch `main`.
