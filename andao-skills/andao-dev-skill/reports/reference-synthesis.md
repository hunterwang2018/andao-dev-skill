# Reference Synthesis

Skill: `andao-dev-skill`
- Description: 软件开发的完成开发流程，严格遵循skill中定义的流程规范 Primary output: 一个重复使用的技能包 Keep this correction in scope: 正确.
- Intent confidence: `43/100` (`low`)

## Live GitHub Benchmarks

- No live GitHub benchmarks are attached yet.

## Curated World-Class Pattern Tracks

### Official skill anatomy and context discipline
- Type: `official`
- Evidence mode: `curated-pattern-track`
- Why relevant: This track matches: general fit.
- Borrow: Borrow progressive disclosure: keep the entrypoint lean and move depth into references or scripts.
- Avoid: Do not let packaging or platform concerns swallow the core job boundary.

### Hypothesis-test-learn loop
- Type: `research`
- Evidence mode: `curated-pattern-track`
- Why relevant: This track matches: general fit.
- Borrow: Borrow a small hypothesis-test-learn loop so the first revision is evidence-backed.
- Avoid: Do not create experimental overhead that exceeds the skill's real risk tier.

### Boundary-first design
- Type: `principles`
- Evidence mode: `curated-pattern-track`
- Why relevant: This track matches: scope.
- Borrow: Borrow the discipline of defining what the skill should not own before growing the package.
- Avoid: Do not expand execution assets until route boundaries stay clean.

## Borrow Now

- Borrow progressive disclosure: keep the entrypoint lean and move depth into references or scripts.
- Borrow a small hypothesis-test-learn loop so the first revision is evidence-backed.
- Borrow the discipline of defining what the skill should not own before growing the package.

## Avoid Now

- Do not let packaging or platform concerns swallow the core job boundary.
- Do not create experimental overhead that exceeds the skill's real risk tier.
- Do not expand execution assets until route boundaries stay clean.

## Pattern Gate

- Summary: 3 accepted, 0 deferred using threshold 3/4.
- Acceptance threshold: `3/4`
- Accepted patterns:
  - **Official skill anatomy and context discipline**: 3/4 (recurrence, generativity, boundary)
  - **Hypothesis-test-learn loop**: 3/4 (recurrence, generativity, boundary)
  - **Boundary-first design**: 4/4 (recurrence, generativity, distinctiveness, boundary)

## Default Recommendation

- Summary: Start by borrowing this pattern: Borrow progressive disclosure: keep the entrypoint lean and move depth into references or scripts. Avoid this for the first pass: Do not let packaging or platform concerns swallow the core job boundary.
- Why: Intent still has gaps, so the system should surface the recommendation and ask for correction before deepening the package.
- User decision required: `True`

## Visibility Mode

- Mode: `explicit`
- Reasons: intent_uncertain
- User note: Surface the recommendation because intent is still settling or there is a real design conflict that needs a user call.
- Reviewer note: Keep the full benchmark and synthesis evidence visible for authors and reviewers.

## Conflict Check

- No material design conflict detected. Keep the synthesis silent for the user.

## Quality Lift Thesis

- Use GitHub repositories for concrete package and workflow patterns.
- Use curated official or commercial tracks for entrypoint and operator ergonomics.
- Use research tracks to justify the smallest evaluation loop that still catches regressions.
- Use principle tracks to keep the package small, boundary-aware, and outcome-driven.

## Decision Prompt

Use the recommendation by default. Only surface the underlying benchmark tradeoffs when intent is uncertain or a real design conflict needs a deliberate call.
