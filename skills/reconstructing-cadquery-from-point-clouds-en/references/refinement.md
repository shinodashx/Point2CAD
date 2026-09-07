# General Numerical Reconstruction and Refinement

Use for initial feature selection and corrections after exceeded targets, systematic residuals, junction anomalies, or invalid geometry. Choose operations from input evidence, not part-category templates, fixed radii, fixed partitions, or fixed iteration counts.

## 1. Test feature hypotheses first

- Save source indices, partition conditions, counts, and spatial coverage for candidate regions; compare candidates on the same region. Separate face interiors, end faces, transitions, and other components during measurement; explain local exclusions by feature membership, but never remove them from full-cloud validation.
- Numerically compare simple, interpretable alternatives: plane/cylinder, line/arc, extrusion/revolution, uniform offset/independent inner and outer walls. Decide from residual patterns and consistency across sections, not an assumption that one circular exterior makes everything coaxial.
- When support permits, cross-check spatially separated held-out sections or regions; record uncertainty from short arcs, nearly parallel faces, ill-conditioned fits, or occlusion. Extra degrees of freedom lowering training residuals do not establish a more correct structure.
- Report fitted-parameter residuals, adopted nominal-parameter residuals, and final CAD distances separately. Quantify rounding effects; attribute errors to noise or source-mesh chord deviation only when residual patterns, section evidence, and discretization convergence support that explanation.

| Structures to distinguish | Numerical test | Operation choice |
|---|---|---|
| Planar interior and cylindrical exterior | Independently test normals, radial residuals, and axial consistency of both walls | Do not force Shell; use Extrude/Cut for the interior and Revolve for the exterior when supported |
| Cylindrical bend and toroidal transition | Test whether a fixed section is invariant along a line or rotates about an axis | Extrude/Sweep a fixed arc section, or Revolve a section |
| Straight section meeting a curve | Solve tangent points, radii, and tangent continuity; check whether flat stock fills the bend | End the flat section at tangency, then join with tangent Sketch/local Fillet |
| End plane and radial cap | Measure end-face normals independently of the exterior's angular span | Trim with the measured construction plane rather than a guessed revolve angle |
| Mirror/pattern and merely similar parts | Compare plane layers, centers, thicknesses, and regional residuals after transformation | Mirror/Pattern only with evidence; otherwise build separately |

## 2. Repeat the same loop for every revision

1. **Save a baseline.** Use a separate revision directory for the replayable sequence, parameter/hypothesis changes, evidence, validation, and geometry hashes. Record input/index hashes, coordinates, units, threshold, surface type, and tessellation. Label diagnostic thresholds when manufacturing tolerances are unknown; do not relax them across revisions to manufacture acceptance.
2. **Localize errors.** Inspect worst points, all exceeded-threshold points, and spatial clusters together; trace them to features and S steps. Distinguish structure, dimensions, clipping boundaries, missing contact faces, and discretization issues. Do not optimize only global RMS or a single worst point.
3. **Propose a falsifiable correction.** State the old hypothesis, contradictory evidence, candidate CAD operations, and regions expected to improve. Change one related feature group at a time; preserve verified dimensions. Do not invent holes, fillets, or hidden structure when evidence cannot distinguish candidates.
4. **Modify and validate bodies.** Correct feature types before tuning a few dimensions; update evidence and the operation plan, then replay the complete sequence. Check validity, solid count, positive volume, and local thickness for every component and every fused deliverable. Lower distances cannot excuse invalid geometry.
5. **Revalidate all points under identical conditions.** Compare all input points, corrected regions, previously satisfactory regions, and critical openings separately; label overlapping region masks. Record N, mean, RMS, median, P95, maximum, exceedance counts/fractions, worst source indices, and body checks. Compare STEP faces and finer meshes to distinguish geometric from discretization errors.
6. **Keep or reject the candidate.** Retain only valid, evidenced candidates that improve the objective. Explain local regressions and satisfy critical constraints; global improvement cannot cancel a critical-feature failure. Revert only this reconstruction's own candidate changes, never user/other work. Rerender actual exports and inspect numerically identified regions; produce a revision comparison table.

## 3. Geometry-kernel issues and visual misinterpretation

- Fix rounded coordinates, non-tangent sketches, slivers, and incorrect contacts before considering Boolean tolerance. Never hide structural errors with a fixed large tolerance. Choose tolerance using units, the smallest relevant feature, and accuracy targets; record before/after volume, openings, interference, and validity. Unverified repairs/fusions are not deliverables.
- Fillets can add or remove material; large rim errors do not by themselves justify adding a fillet. Verify tangent points, sections, adjacent faces, and actual numerical residuals. Do not promote failed hypotheses into final modeling rules.
- Transparency, projected overlap, floating scan points after hiding a component, and uncapped display clipping do not prove interference or broken geometry. Use opaque views and explicit legends alongside BRep intersection volumes, sections, and point-to-face distances.

## 4. Stopping and delivery criteria

- **Accepted:** global, regional, and critical-dimension targets are met; body/mesh/assembly checks pass; exports are revalidated and actual renders inspected. Low overall RMS or majority coverage does not establish accuracy of every detail.
- **Evidence-limited or stagnant:** after checking evidenced alternative structures and numerical errors, remaining parameters are not identifiable, or candidates only lower local training residuals while degrading other regions. Record hypotheses tried, revision metrics, remaining source indices, and missing constraints; retain the best valid approximation and request specific regional sections, dimensions, or denser points. Do not cite sparsity to skip still-localizable, repairable defects, or chase noise to reach an arbitrary iteration count.
- The final script is independent of analysis inputs; the source, sequence, and STEP/STL hashes recorded in the report must each match the corresponding actual file. After rebuilding, old validation is history, not the current result. Measurement scripts must recompute evidence, and validation commands must recompute all reported errors.
