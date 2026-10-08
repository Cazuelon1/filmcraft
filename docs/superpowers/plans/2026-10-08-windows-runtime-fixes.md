# Windows runtime fixes implementation plan

> **For agentic workers:** Use systematic debugging and test-driven development for each task; verify the integrated result before completion.

**Goal:** Correct the Windows failures observed in the workspace test run without changing the existing themes or disabling coverage.

**Architecture:** Keep filesystem paths native when writing files. Normalize both sides when matching cross-platform folder remaps. Correct test fixtures that retain unnecessary file handles or compare path spellings instead of path components.

**Tech Stack:** Rust, Cargo, egui_kittest, wgpu.

**Spec:** The user's request to correct the Windows failures, with AGENTS.md as the governing safety rules.

## Global constraints

- No new unsafe code, dependencies, assets, or panicking production paths.
- No commits or changes to the existing theme feature.
- Preserve cross-platform remap matching and platform-specific shortcut semantics.

## Review focus

- Windows prefixes supplied with backslashes must match stored paths with either separator.
- Verbatim Windows output directories must not retain forward slashes.
- Directory outputs with either trailing separator must keep sequence-specific names.
- Tests must release finished media sessions before moving their parent folders.
- GPU tests must exercise all assertions and complete without concurrent device contention.

## Tasks

- [x] Add and observe failing tests in `relink_tests.rs` for backslash prefixes and in `export_tools.rs` for mixed-separator verbatim directories; preserve the existing queue naming regression.
- [x] Fix `relink::apply_remap` and export directory handling; rerun the regressions and engine integration tests.
- [x] Correct path comparisons in AAF, proxy, image-sequence, MXF and relink tests; close finished fixture sessions before directory moves.
- [x] Make the shortcut test compare native conflicts against its baseline, retaining its intentional-conflict and undo checks.
- [x] Diagnose and correct GPU test contention, retaining real rendering assertions. Fix the exposed FX source interpolation error with an exact pixel-centre regression; retain the existing precision thresholds.
- [x] Run formatting, Clippy, the workspace suite, layering and asset checks; document any remaining limitation.

## Verification

- Both path regressions failed before their production fixes.
- The empty-effect source identity regression failed at odd dimensions before the shader fix.
- Engine: 381 passed, 3 ignored; export: 43 passed; GPU: 21 passed under normal harness concurrency.
- The workspace suite completed with exit code 0, including both previously failing media UI tests and all GPU tests.
- Format, diff, Clippy (`-D warnings`), layering and asset checks passed. Independent code review found no actionable defects.
