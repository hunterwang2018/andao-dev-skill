# Andao Dev Skill

## What It Does

`andao-dev-skill` is a reusable skill package for this job:

> 软件开发的完成开发流程，严格遵循skill中定义的流程规范 Primary output: 一个重复使用的技能包 Keep this correction in scope: 正确.

## How To Use

1. Load the skill through `SKILL.md`.
2. Start with `reports/intent-dialogue.md` to tighten the real job, outputs, exclusions, and the standards you care about.
3. Open `reports/reference-scan.md` to capture external benchmarks and any user-supplied references worth learning from.
4. Review `reports/intent-confidence.md` to see whether the real job, inputs, outputs, and exclusions are clear enough yet.
5. Open `reports/reference-synthesis.md` to see the GitHub benchmarks plus curated official, research, and principle tracks in one place.
6. Follow the workflow steps in `SKILL.md`.
7. Open `reports/skill-interpretation.html` first for the first-class bilingual interpretation report: role, principle, scenarios, trigger, inputs, outputs, highlights, risks, assets, and upgrade directions. It defaults to Simplified Chinese and includes an English switch in the top right.
8. Check `reports/skill-overview.html` for the generated bilingual HTML skill audit report: overview, metrics, capability profile, principle, contract, quality, risk, assets, and iteration roadmap.
9. Open `reports/review-studio.html` for the one-page Review Studio 2.0 gate view.
10. Record source-line reviewer comments in `reports/review_annotations.md` when review needs follow-up.
11. Open `reports/review-viewer.html` for a compact visual review of the package.
12. Check `reports/output-risk-profile.md` to see likely output mistakes and self-repair checks.
13. Check `reports/artifact-design-profile.md` to see the intended artifact direction, layout patterns, visual quality gates, and anti-patterns.
14. Check `reports/prompt-quality-profile.md` to see the need model, RTF-to-skill mapping, complexity, and prompt-facing quality matrix.
15. Review `reports/skill-ir.json` for the platform-neutral Skill IR contract before platform-specific packaging.
16. Review `reports/compiled_targets.md` to see how Skill IR compiles into OpenAI, Claude, generic, and Agent Skills compatible target contracts.
17. Review `reports/iteration-directions.md` for the three most valuable next moves.
18. Review `reports/system-model.md` to understand the boundary, feedback loops, drift watch, failure map, and highest-leverage next changes.
19. Review `reports/adoption_drift_report.md` to see local-first metadata-only adoption and drift signals.
20. Review `reports/review_waivers.md` to see human reviewer risk approvals and expiry dates.

## Honest Boundaries

- This package starts from the current intent frame and should not pretend to cover unclear adjacent jobs.
- The first version should ask for clarification when the real input, output, or exclusion boundary is still fuzzy.
- New structure should be added only when it earns its keep through evidence, validation, or reviewer need.

## Package Map

- `SKILL.md`: trigger and workflow entrypoint
- `agents/interface.yaml`: portable interface metadata
- `manifest.json`: lifecycle and packaging metadata
- `reports/intent-dialogue.md`: front-loaded discovery questions for better boundary design and clearer human alignment
- `reports/intent-confidence.md`: current clarity score, open gaps, and the next follow-up questions worth asking
- `reports/github-benchmark-scan.md`: top public benchmark repositories, extracted patterns, and borrow or avoid notes
- `reports/reference-scan.md`: benchmark notes from public references, user references, and local constraints
- `reports/reference-synthesis.md`: a combined view of GitHub benchmarks plus curated world-class pattern tracks
- `reports/output-risk-profile.md`: predicted output failure modes and self-repair constraints for this skill
- `reports/artifact-design-profile.md`: artifact-specific design direction, layout patterns, visual quality gates, and anti-patterns
- `reports/prompt-quality-profile.md`: prompt-facing need model, RTF mapping, complexity, and quality matrix
- `reports/system-model.md`: systems-thinking model for boundary, feedback loops, drift, failure patterns, and leverage points
- `reports/skill-ir.json`: platform-neutral 2.0 Skill IR contract for trigger, workflow, resources, evals, risk, and governance
- `reports/compiled_targets.md`: target compiler report showing generated contracts, adapter modes, preserved semantics, warnings, and unsupported features
- `reports/skill-interpretation.html`: first-class bilingual interpretation report for role, principle, scenarios, trigger, inputs, outputs, quality evidence, risks, assets, highlights, and upgrade directions
- `reports/skill-overview.html`: white-background bilingual HTML skill audit report with sticky four-character Chinese navigation, a top-right language switch, metrics, SVG charts, contract boundary, quality review, risk governance, assets, and iteration roadmap
- `reports/review-studio.html`: Review Studio 2.0 gate page for intent, trigger, output eval, context, runtime conformance, trust, atlas, and release readiness
- `reports/review-viewer.html`: compact review page for architecture, usage, feedback, and next steps
- `reports/iteration-directions.md`: the top three next iteration directions
- `reports/adoption_drift_report.md`: local-first metadata-only telemetry summary for adoption, missed triggers, bad outputs, script errors, and review drift
- `reports/review_waivers.md`: human reviewer risk approval ledger for warning acceptance and expiry
- `reports/review_annotations.md`: source-line reviewer comments linked to Review Studio gates
