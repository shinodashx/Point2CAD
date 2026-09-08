# Point2CAD

**XYZ-first point-cloud analysis → editable CadQuery feature sequences.**

[![简体中文](https://img.shields.io/badge/语言-简体中文-blue)](README.zh-CN.md)

Reconstruct NPY, PLY and pasted XYZ through explicit CAD operations. The full workflow uses XYZ measurements and Python analysis to determine features and dimensions, with visualization as support. Three analysis modes each include separate English and Simplified Chinese editions.

## Contents

- [Skill directory](#skill-directory)
- [Project layout](#project-layout)
- [Setup](#setup)
- [Point cloud → CadQuery](#point-cloud-to-cadquery)
- [Validation and limitations](#validation)
- [References](#references)
- [简体中文](README.zh-CN.md)

<a id="skill-directory"></a>

## Skill directory

| Mode | Point-cloud analysis | English | 简体中文 |
|---|---|---|---|
| Full (hybrid) | XYZ/Python measurements lead; views assist | [Skill](skills/reconstructing-cadquery-from-point-clouds-en/SKILL.md) | [Skill](skills/reconstructing-cadquery-from-point-clouds-zh/SKILL.md) |
| Numerical-only | XYZ/Python measurements only | [Skill](skills/reconstructing-cadquery-numerically-en/SKILL.md) | [Skill](skills/reconstructing-cadquery-numerically/SKILL.md) |
| Visual-only | Actual rendered point-cloud views only | [Skill](skills/reconstructing-cadquery-visually-en/SKILL.md) | [Skill](skills/reconstructing-cadquery-visually/SKILL.md) |

All modes share feature planning, sequential modeling, junction checks, CAD validity, assembly checks and exports. Numerical-only retains CAD-only visual inspection. Visual-only uses programs for decoding, integrity checks and display; numerical cloud accuracy is **not evaluated**.

<a id="project-layout"></a>

## Project layout

```text
Point2CAD/
├── README.md
├── README.zh-CN.md
├── requirements.txt
└── skills/
    ├── reconstructing-cadquery-from-point-clouds-en/
    ├── reconstructing-cadquery-from-point-clouds-zh/
    ├── reconstructing-cadquery-numerically-en/
    ├── reconstructing-cadquery-numerically/
    ├── reconstructing-cadquery-visually-en/
    └── reconstructing-cadquery-visually/
```

Each edition contains `SKILL.md`, `agents/openai.yaml`, `references/checklist.md`, `references/refinement.md` and `scripts/`. Scripts are identical across language editions of the same mode; each directory is self-contained.

<a id="setup"></a>

## Setup

Select one mode and language per task. Copy its **complete directory** into the target project's `.agents/skills/`, checking for an existing installation first; then refresh your agent's skill discovery.

```bash
git clone https://github.com/shinodashx/Point2CAD.git
cd Point2CAD
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
for skill in skills/*; do
  python "$skill/scripts/test_workflow.py" || exit 1
done
```

Tested with Python 3.11 and CadQuery 2.8.0. The six suites contain seven tests each, covering input formats, coordinate preservation and mode-specific measurement or rendering behavior.

<a id="point-cloud-to-cadquery"></a>

## Point cloud → CadQuery

**Read XYZ → analyze numerically → record evidence → plan features → build the sequence → validate and refine.**

Example prompt for the full workflow:

> Use reconstructing-cadquery-from-point-clouds-en to reconstruct `input.ply`. Determine features and dimensions primarily from XYZ values and Python analysis; use views as support. Deliver a standalone CadQuery sequence, STEP and an iterative validation report.

```bash
SKILL_DIR=skills/reconstructing-cadquery-from-point-clouds-en
python "$SKILL_DIR/scripts/inspect_cloud.py" input.ply --out analysis
# NPY and saved XYZ text use the same command with a different filename.
# Extra columns: --xyz-columns 0 1 2; confirmed units: --unit mm --unit-status confirmed
```

The reader preserves coordinates, order and duplicates, producing `input_report.json`, `points.npy`, `source_indices.npy` and supporting `projections.png`. PLY contributes vertex XYZ. Use Python distributions, layers, numerical sections and axial–radial measurements to establish feature evidence.

Record measured/adopted dimensions in `feature_evidence.csv` and dependencies, datums, constraints and checks in `feature_plan.md`. Build standalone `sequence_cq.py` with named parameters, intermediate bodies and explicit S01, S02… operations. Choose Sketch, Extrude, Revolve, Sweep, Loft, Shell, Hole, Boolean, Fillet, Chamfer, patterns, mirrors, construction planes and transforms as features require; maintain the retained-body list for body removal. The sequence constructs all geometry independently of the input cloud.

After modeling and exporting, set `TOLERANCE` to the agreed threshold in task units and run:

```bash
python "$SKILL_DIR/scripts/verify_surface.py" \
  --points analysis/points.npy --source-indices analysis/source_indices.npy \
  --mesh result/component_surfaces.stl --out validation \
  --threshold "$TOLERANCE" --step result/components.step --exact-worst 20
```

The `result/` files must already exist; output directories should be new or empty. The verifier produces `point_errors.csv` and `verification.json`. Follow the [checklist](skills/reconstructing-cadquery-from-point-clouds-en/references/checklist.md) and [refinement loop](skills/reconstructing-cadquery-from-point-clouds-en/references/refinement.md) for regional analysis, local repair and complete revalidation. Single-mode editions use their own evidence workflow and bundled tools.

Deliver input provenance, evidence, plan, replayable sequence, STEP/requested STL, reproducible checks, revision comparisons and inspected renders. Bind reports to final file hashes.

<a id="validation"></a>

## Validation and limitations

- Replay independently, validate BRep and intended features, reimport STEP, and check meshes and assembly clearances. Validate fillets through local topology, tangent points and wall thickness.
- Full and numerical-only modes report full-cloud/regional point-to-triangle-face errors; refine tessellation and cross-check selected STEP faces. Reverse surface sampling diagnoses coverage with visibility and occlusion accounted for.
- Fix the responsible sequence step, regenerate exports, and recheck corrected and previously passing regions. Full-mode image concerns are resolved numerically; visual-only compares matched views and separately checks CAD quality.
- State units, acceptance targets, uncertain geometry and remaining approximations. Sparse or occluded data may leave dimensions unresolved; tool tests establish tool behavior, while reconstruction accuracy requires per-model validation.
- Keep faithful reconstruction and functional redesign separate. Add motion when requested and report the scope of pose/interference checks separately from engineering-performance evaluation.

<a id="references"></a>

## References

Workflow organization draws on [text-to-cad](https://github.com/earthtojake/text-to-cad)'s modeling brief, layered validation and local repair ideas, independently adapted to XYZ evidence and CadQuery. Skill packaging follows [Codex Skills](https://developers.openai.com/codex/skills/); bilingual documentation follows the structure of [CADSeqenceReverse](https://github.com/shinodashx/CADSeqenceReverse).
