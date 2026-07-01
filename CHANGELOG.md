# Changelog

## v1.2.0

- Adds a preview-owned `schemas/preview/mid-ir-adapter.schema.json` ingest contract for evidence-backed mid-fidelity structured IR adaptation.
- Updates `speckit.preview.mid-md` and `speckit.preview.mid-html` to optionally consume `structured-ir.yaml`, `ir-assertions.yaml`, and `ir-evidence-packet.md` when they can be mapped to the preview adapter contract.
- Keeps structured IR mapping non-blocking: missing, partial, or unmappable IR falls back to existing Spec Kit artifacts and is reported as an evidence gap.
- Updates package validation so declared schema files are driven by `schemas/preview/contract.json` instead of a hard-coded schema file list.

## v1.1.0

- Adds six preview commands: `speckit.preview.low-md`, `speckit.preview.low-html`, `speckit.preview.mid-md`, `speckit.preview.mid-html`, `speckit.preview.high-md`, and `speckit.preview.high-html`.
- Adds separate Markdown and HTML output designs for low, mid, and high fidelity preview artifacts.
- Adds fixed preview templates under `templates/preview/` so command prompts no longer define output structure inline.
- Adds schema-backed validation contracts under `schemas/preview/` for command/template structure checks.
- Adds consistent active feature detection and update-preservation guidance across preview commands.
- Strengthens package validation for command/template responsibility separation, output boundaries, and fidelity-specific contracts.

## v1.0.0

- Initial release.
- Adds `speckit.preview.html` for generating self-contained interactive HTML prototypes from Spec Kit feature artifacts.

