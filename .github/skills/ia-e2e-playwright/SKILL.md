---
name: ia-e2e-playwright
description: "Use when: BoxLang + TestBox + Playwright E2E work in this repo, including tests/specs/E2E failures, BaseSpec.bx browser setup, CommandBox runner URLs, Maven Playwright dependencies, device emulation, browser incompatibilities, query parameter handling, and host-unavailable cleanup issues."
---

# IA E2E Playwright Skill

## Purpose
Provide a repository-specific workflow for debugging, extending, and stabilizing BoxLang E2E tests that run through TestBox and Playwright Java.

## Use When
- A test fails under `tests/specs/E2E/*` or `tests/specs/*`.
- You need to modify shared E2E behavior in `tests/resources/BaseSpec.bx`.
- Browser behavior differs between `chromium`, `firefox`, and `webkit`.
- Device emulation is required (iPhone/iPad/Android presets).
- Test navigation fails because the host is unavailable.
- You need to validate command flow (`box install`, `mvn install`, runner URL execution).
- You need to tune reliability, cleanup, and failure diagnostics without broad refactors.

## Project Map
- Main E2E specs: `tests/specs/E2E/`
- Shared E2E helper: `tests/resources/BaseSpec.bx`
- Test runner endpoints: `tests/runner.bxm`, `tests/runner.cfm`
- Dependency source for Playwright Java libs: `tests/pom.xml` and `tests/lib/`
- App and server config: `Application.bx`, `server.json`, `box.json`
- Embedded TestBox framework source: `testbox/`

## Inputs
- Failing spec path and failing test name (if available).
- Stack trace and failing browser/engine.
- Current `baseURL`, active server port, and runner URL used.
- Relevant helper files (usually `tests/resources/BaseSpec.bx`).

## Workflow
1. Read the failing spec and `tests/resources/BaseSpec.bx` first.
2. Confirm run path and environment assumptions:
	- Server running and reachable (`server.json` port or overridden host).
	- Playwright Java libs present (`tests/lib/` populated from Maven).
3. Locate browser lifecycle flow:
	- `createUI()` launch/context creation.
	- `goTo()` navigation behavior.
	- `close()` cleanup guarantees.
4. Validate browser/device compatibility rules:
	- Firefox option support differs from Chromium/WebKit.
	- Device emulation options may require per-engine conditionals.
5. Diagnose URL and navigation behavior:
	- Relative vs absolute paths.
	- Query parameter handling when requested by a suite.
	- Error handling when host is unavailable.
6. Patch minimally in shared helpers or target spec.
7. Validate with problems check and, when requested, test execution.
8. Return concise root cause, change summary, and next step.

## Standard Command Sequence
1. Install project deps: `box install`
2. Install Playwright Java deps: `cd tests && mvn install`
3. Start app: `box server start`
4. Run tests:
	- Browser runner: `http://localhost:4099/tests/runner.bxm`
	- CLI: `box testbox run`

## Known Pitfalls
- Host unavailable can leave browser resources open unless navigation failures close immediately.
- Firefox rejects some mobile/device context options that work in Chromium/WebKit.
- Failing to keep helper changes minimal can break many suites.
- Description and trigger phrasing must stay specific so this skill is discoverable.

## Guardrails
- Prefer minimal changes and preserve existing APIs.
- Avoid broad refactors during debugging fixes.
- If adding browser-specific behavior, isolate it with clear conditions.
- Ensure cleanup still runs when navigation throws exceptions.
- Keep commands and paths aligned with this repository structure.
- Validate edited files before concluding.

## Expected Output
- Root cause and affected scope.
- Exact file edits with paths.
- Validation status (problems/tests if executed).
- Suggested next hardening step only when useful.
