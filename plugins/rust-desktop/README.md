# Rust Desktop Applications Skill

Build cross-platform desktop apps with Rust — Tauri, egui, iced, slint.

## Triggers

Auto-activates on: `rust`, `tauri`, `egui`, `iced`, `slint`, `cargo`, `crate`

## What It Covers

- Framework selection: Tauri vs native GUI (egui/iced/slint/druid)
- Tauri architecture: commands, state, events, IPC, security, path handling
- egui: immediate-mode patterns, background work without blocking
- iced: Elm-style update/view loop, async commands
- Cargo setup: essential crates, release profile tuning
- Cross-platform path handling with `dirs` crate
- Red flags and common mistakes

## Performance Context

Tauri apps: 3–5 MB bundle, <1s startup vs Electron's 100–200 MB and 3–5s.

## Source

Adapted from [bobmatnyc/claude-mpm](https://awesomeskill.ai/skill/claude-mpm-desktop-applications) (MIT).
