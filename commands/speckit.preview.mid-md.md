---
description: Generate a mid-fidelity evidence-backed Markdown wireflow preview from Spec Kit artifacts.
---

## User Input

```text
$ARGUMENTS
```

You **MUST** evaluate the user input before proceeding. Treat it as preview focus, audience, device, flow, or review criteria.

If `$ARGUMENTS` requests a page, role, device, state, interaction, or business rule that is not supported by the loaded artifacts, do not invent it. Mark it as `输入未说明`, or use `推理补齐` only for the minimal traceable inference required to preserve preview coherence.

## Goal

Generate or update `specs/<feature>/preview/wireflow-mid.md`.

Mid fidelity is the baseline product, UX, and engineering review artifact. It represents page structure, source-defined labels, basic layout relationships, states, and evidence-backed behavior, excluding final visual design treatment.

## Command Responsibilities

- Resolve the active feature and load source artifacts.
- Apply evidence policy and fidelity-specific extraction rules.
- Load the output template from `.specify/extensions/preview/templates/preview/mid.md`; when running from this extension repository, use `templates/preview/mid.md`.
- Populate template slots with evidence-backed content only.
- Write only `specs/<feature>/preview/wireflow-mid.md`. Create `specs/<feature>/preview/` if it is missing; do not create any other files or directories.

Do not redefine template sections, table columns, or output structure in this command. The template is the source of truth for Markdown shape and required tables.

## Boundaries

- Do not modify source code, tests, app assets, package manifests, build configuration, feature specs, plans, tasks, or memory files.
- Do not create HTML, Figma files, images, screenshots, or production UI.
- Do not invent business rules, roles, fields, states, visual details, or interactions. Mark unsupported items as `输入未说明`; mark only minimal traceable inferences as `推理补齐`.
- If the template file cannot be read, stop with an error explaining that the preview extension template is missing.

## Context Loading

1. Verify the current directory is a Spec Kit project by checking for `.specify/`.
2. Identify the active feature:
   - Prefer `SPECIFY_FEATURE` when set.
   - Otherwise use the current Git branch name when it exactly matches a directory under `specs/`.
   - Otherwise inspect `specs/` and use it only when there is exactly one unambiguous candidate directory.
   - Do not choose by most recent timestamp when multiple feature directories exist.
   - If the feature cannot be identified, stop and ask the user to set `SPECIFY_FEATURE` or run from the feature branch.
3. Read these files when present:
   - `specs/<feature>/spec.md` (required)
   - `specs/<feature>/plan.md`
   - `specs/<feature>/research.md`
   - `specs/<feature>/data-model.md`
   - `specs/<feature>/contracts/`
   - `specs/<feature>/quickstart.md`
   - `specs/<feature>/intake/**/structured-ir.yaml`
   - `specs/<feature>/intake/**/ir-assertions.yaml`
   - `specs/<feature>/intake/**/ir-evidence-packet.md`
4. Read `.specify/memory/constitution.md` if present.
5. If `spec.md` is missing, stop with an error explaining that `/speckit.specify` must run first.
6. If `specs/<feature>/preview/wireflow-mid.md` already exists, read it before writing. Preserve user-authored review notes, decisions, and unresolved questions when they remain consistent with current source artifacts; label changed items as `UPDATED` and superseded items as `SUPERSEDED`.

## Evidence Policy

Use these coverage labels exactly: `已覆盖`, `部分覆盖`, `未覆盖`, `输入未说明`, `推理补齐`.

Every requirement, page, node, field, state, branch, and system response included in the preview must include a coverage label and provenance. Provenance must be either a source anchor or a `推理补齐` explanation.

Coverage evidence must identify the source with a precise anchor: file path, heading, section id, line number, table row, or short quoted source excerpt. Use `输入未说明` when no source supports the requested item. Use `推理补齐` only when a minimal inference connects two supported facts; include the reasoning bridge and keep it non-authoritative.

## Structured IR Adapter Input

Use `schemas/preview/mid-ir-adapter.schema.json` as the preview-owned adapter contract for mid-fidelity structured input. This schema defines the minimum payload needed to render an evidence-backed mid preview; it is not the upstream `spec-kit-intake` structured IR authority and must not be treated as a copy of the upstream IR schema.

When `specs/<feature>/intake/**/structured-ir.yaml`, `specs/<feature>/intake/**/ir-assertions.yaml`, or `specs/<feature>/intake/**/ir-evidence-packet.md` are present, attempt to map source-backed `source_artifacts`, `pages`, `pages[].roles`, `pages[].viewports`, `regions`, `fields`, `controls`, `states`, `relations`, `assertions`, `evidence_gaps`, and `visual_enhancement_refs` into the adapter contract. Ignore upstream `contract_type`; do not use it as the preview validation target. Validate the mapped adapter payload against `schemas/preview/mid-ir-adapter.schema.json` before treating it as IR-backed preview input. Require only that the mapped payload contains enough adapter-compatible structure and provenance to support this preview.

If the mapped payload passes adapter schema validation, prefer IR-backed pages, regions, fields, controls, states, relations, and assertions over looser extraction from prose artifacts. Each IR-backed item still requires a coverage label and provenance. If an item lacks `source_refs`, use it only when a minimal `推理补齐` explanation is available; otherwise mark it as `输入未说明` or record it as an evidence gap.

If structured IR files are missing, unreadable, blocked, cannot be mapped, cannot be validated, or fail validation against `schemas/preview/mid-ir-adapter.schema.json`, do not fail solely for that reason. Continue from `spec.md`, `plan.md`, `contracts/`, and other available artifacts, then record the IR limitation as an evidence gap with one of these stable codes: `IR_MISSING`, `IR_UNMAPPABLE`, or `IR_PARTIAL`.

Distinguish IR-backed structure from visual screenshot, HTML SSOT, design, or token enhancement refs. Visual enhancement refs may support layout review only; they do not create business rules, interactions, states, relations, assertions, coverage conclusions, or acceptance evidence by themselves.

## Mid-Fidelity Extraction Policy

Extract only:

- Source-defined page, field, button, message, and role labels.
- Page/state nodes and decision nodes.
- Node purpose, visible elements, user actions, system responses, states, and evidence anchors.
- IR-backed regions, controls, states, relation checks, and assertions when they satisfy `schemas/preview/mid-ir-adapter.schema.json`.
- Basic layout relationships such as header/sidebar/content/table/form/modal only when supported by artifacts or marked as `推理补齐`.
- Empty, loading, validation error, success, failure, disabled, and permission states when relevant and supported by evidence.
- Coverage evidence mapped from requirements to preview nodes, states, or review questions.

Use monochrome wireframe notation. Do not invent brand visual design, icons, shadows, images, final typography, animation, or production component names.

## Procedure

1. Summarize the feature goal, personas, primary scenarios, and constraints from loaded artifacts.
2. Select the preview focus from `$ARGUMENTS`; if absent, cover the highest-priority user story and primary alternate flow state.
3. Map optional structured IR inputs into `schemas/preview/mid-ir-adapter.schema.json` and validate the mapped adapter payload against that schema; if mapping or validation fails or is partial, keep the failure non-blocking and record `IR_UNMAPPABLE` or `IR_PARTIAL` as the evidence gap.
4. Extract evidence-backed pages, tasks, fields, controls, states, decisions, relation checks, assertions, and system responses.
5. Fill the mid-fidelity Markdown template slots for metadata, evidence summary, wireflow, node inventory, state inventory, branches, coverage, preserved review records, and unresolved review questions.
6. Write `specs/<feature>/preview/wireflow-mid.md`, preserving existing review content as described above.
7. Report output path, fidelity, input sources, structured IR mapping status, flows covered, inferred assumptions, unsupported items, evidence gap entries, and unresolved review questions.
