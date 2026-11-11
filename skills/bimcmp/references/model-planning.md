# BIMCMP model planning

Use this planning method before multi-category Revit work. It adapts the useful structure of implementation planning to physical model work: name the evidence and dependencies, make each phase small and verifiable, and close the loop against the source drawings. The result may be a concise in-chat plan or a task-specific Markdown note when the user requests a saved artifact.

## Plan header

- **Goal:** one sentence describing the model outcome and requested categories.
- **Source set:** file names, sheet/view names, revisions, units, and any reference model.
- **Revit target:** verified document and unit system.
- **Coordinate assumptions:** project origin, X/Y mapping, project north/shared coordinates only when evidenced.
- **Scope:** requested categories; explicitly exclude unrequested categories.
- **Open decisions:** conflicting, missing, or low-confidence evidence that can change geometry.

## Build a reference schedule

Capture only values that drive geometry or type selection. For each, record value/unit, reference name, source sheet/view, whether it is explicit or derived, confidence, and any conflict.

| Reference | Required evidence |
|---|---|
| Datums | level names/elevations; grid labels/positions; project zero; coordinate mapping |
| Geometry | overall dimensions; dimension chains; reference face/centerline; offsets; angles; boundary closures |
| Types | category, mark/type, total thickness, build-up, material/function, nominal size |
| Openings | mark, host, width/height, offsets to datums/faces, hinge/hand/swing, sill/head/base offsets |
| Vertical coordination | slab tops/bottoms; wall base/top; head/sill; ceiling/roof elevations, slopes, clearances |
| Structure/services | support lines, bearing, framing direction, shafts, risers, equipment and service zones |
| Documentation | required plans, sections, elevations, schedules, tags, dimensions, sheet references |

Use written dimensions first. Derive secondary locations arithmetically and show the dimension chain. Scale a raster only with a known calibration dimension; label the result approximate and lower confidence. When dimensions fail to close, record the delta and do not distribute the error invisibly.

## Sequence category phases by dependency

Include only categories in scope. For each phase, write: **inputs/evidence; elements and Revit MCP operations; expected result; read-back checks; view checks; unresolved decisions.** A phase is complete only when those checks pass or remaining issues are explicitly carried forward.

| Phase | Typical categories | Depends on | Main checks before next phase |
|---|---|---|---|
| 0. Existing conditions | linked models/CAD, survey points, project info, phases, existing elements | drawing/model inventory | intended document, units, link placement, existing-vs-new scope, coordinates |
| 1. Datums and control | levels, grids, reference planes | phase 0 | elevations and axis locations match references; dimensions close |
| 2. Primary structure | foundations, columns, structural walls, beams, framing, structural slabs | phases 0–1 | bearing/alignment, levels/offsets, spans/direction, support relationships |
| 3. Floor plates and envelope | floors/slabs, exterior walls, curtain walls, roofs | phases 1–2 as applicable | boundary closure, wall faces/thickness, joins, slopes, base/top constraints |
| 4. Hosted openings | doors, windows, wall openings | host walls/floors and datums | host ID, jamb offsets, size, level/head/sill, orientation/swing |
| 5. Interior and circulation | partitions, stairs, ramps, railings, ceilings, shafts | phases 1–4 as applicable | face offsets, vertical clearances, landings, room boundaries, openings |
| 6. Spaces and finishes | rooms, areas, MEP spaces, finish assignments | enclosing geometry and phases | enclosure, level/phase, room-bounding settings, finish extents |
| 7. Building services | ducts, pipes, cable trays, conduit, fixtures, equipment, terminals | structure, openings, ceiling/space coordination | systems, connectors, slopes/sizes, clearances, access, penetrations |
| 8. Documentation | views, sections, elevations, dimensions, tags, schedules, sheets | stable model geometry | view coverage, datum references, legibility, schedules agree with model |

This is a default dependency graph, not a mandatory list. For example, a small architectural plan can omit structural or MEP phases. If a project-specific dependency changes the order, name it in the plan. Do not model a dependent category until its host/control geometry is stable enough to verify.

## Make each phase checkable

Write short actions with observable completion conditions. Good examples:

- “Read the Level 1 and Level 2 annotations; record elevations and source views; confirm their difference matches the section.”
- “Create grids A–D from the dimension schedule; query their coordinates; compare outer grid spacing with the overall plan dimension.”
- “Place door D07 in host wall W12 from the specified jamb offset; query host, location, type and level; compare swing and both jambs in plan, then head height in section.”

Avoid vague actions such as “model the plan,” “fix doors,” or “check everything.” Split a phase when independent parts need different sources or validations. Keep dependent operations together only when one coherent transaction is safer and reversible.

## MCP execution and approval boundary

1. Inspect live MCP schemas and supported categories; verify the active target before writes.
2. Prefer typed MCP commands for creation and edits. Group related operations atomically only when the batch supports them and rollback behavior is clear. Record created IDs and returned locations/types/hosts.
3. If a typed command cannot express a required action, use the Revit API only through the MCP, scoped to explicit element IDs and intended values.
4. Computer use may only approve a Revit-native approval dialog for an action the user has authorized. Read the prompt; stop on scope changes, destructive choices, unresolved warnings, or ambiguous effects. Do not model through the UI.
5. After each phase, query actual element state and inspect required views. Do not repeat a timed-out mutation blindly; inspect its operation state first.

## Plan self-review before editing

- Every requested category appears in a phase, and unrequested categories are excluded.
- Every category has its required host, datum, or upstream geometry identified.
- Each critical value traces to a named source or is explicitly marked derived/approximate.
- Wall dimension faces and thickness offsets are accounted for.
- Every opening has an explicit location and orientation check, including plan plus vertical evidence.
- Each phase has a read-back and view-based pass condition.
- Conflicts and unresolved choices are listed, not hidden in coordinates or family defaults.
- The plan is proportionate: enough detail to guide safe modeling, without duplicating the whole drawing set.
