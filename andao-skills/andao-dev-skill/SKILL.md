---
name: andao-dev-skill
description: "软件开发的完成开发流程，严格遵循skill中定义的流程规范 Primary output: 一个重复使用的技能包 Keep this correction in scope: 正确."
---

# Andao Dev Skill

## Workflow

1. Understand the request.
2. Execute the main task.
3. Validate the result.

## Output Quality Guardrails

- Before final output, apply the likely failure modes in `reports/output-risk-profile.md` when that report is present.
- Before rendering reports, tutorials, review pages, dashboards, or visual artifacts, apply the artifact direction and visual quality gates in `reports/artifact-design-profile.md` when that report is present.
- When prompt behavior, role design, dialogue quality, or output contracts matter, apply `reports/prompt-quality-profile.md` when that report is present.
- Before adding more structure, apply the boundary, feedback-loop, drift, and leverage-point checks in `reports/system-model.md` when that report is present.
- Repair generic headings, cluttered notes, fragile visual assumptions, weak tables, and missing verification cues before handing work back.
- Map role, task, and format into skill behavior rather than copying a large prompt template into `SKILL.md`.
- Let the artifact's content choose the visual system; do not copy a fixed palette or report style from another skill without a clear reason.
- If output-specific evidence is missing, state the gap instead of inventing screenshots, citations, data, or examples.

## Honest Boundaries

- Use this skill for the recurring job described in the trigger, not for one-off adjacent requests.
- Treat missing inputs, unclear outputs, or conflicting constraints as reasons to ask one focused clarification.
- Do not add new references, scripts, evals, or governance unless they improve reliability more than they add weight.
