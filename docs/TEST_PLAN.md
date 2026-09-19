# Test Plan

> Define evidence required to trust each milestone. Replace placeholders with the repository's real commands after discovery.

## Verification philosophy

Use the narrowest useful check during implementation, then broaden at milestone boundaries. Never claim success from code inspection alone when executable verification is available.

## Canonical commands

Populate from existing repository tooling; do not invent equivalents.

| Purpose | Command | Expected result |
|---|---|---|
| Focused tests | `TBD` | Relevant tests pass |
| Full tests | `TBD` | Suite passes |
| Lint / format | `TBD` | No blocking findings |
| Type check | `TBD` | No errors |
| Build / package | `TBD` | Successful artifact/build |
| Smoke test | `TBD` | Critical user path succeeds |
| Security / dependency check | `TBD` | Project-defined gate passes |

## Acceptance coverage

| MVP criterion | Test level | Automated? | Evidence / command |
|---|---|---:|---|
| TBD | TBD | TBD | TBD |

## Milestone gate

A milestone may be marked verified only when:

- [ ] task-local tests passed during implementation;
- [ ] fresh spec review has no blocking findings;
- [ ] fresh quality review has no blocking findings;
- [ ] all relevant canonical checks above pass, or each unavailable check has a documented reason and substitute evidence;
- [ ] critical user path has been exercised;
- [ ] `PROJECT_STATE.md` records the exact verification evidence.

## Release gate

- [ ] Full required test suite passes.
- [ ] Lint/format/type/build gates pass as applicable.
- [ ] Migrations/schema changes are validated and rollback/compatibility considered when applicable.
- [ ] Critical smoke test passes in the closest practical environment to release.
- [ ] No unresolved critical/high-severity review finding remains.
- [ ] MVP acceptance criteria are all checked with evidence.

## Regression policy

For a bug fix, prefer a test that demonstrates the failure before the fix and passes after it. Do not weaken meaningful assertions to obtain a green build.
