# STM32 Reference Workbook Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild the static STM32 notebook into a compact documentation workbook that supports sequential learning and fast technical lookup.

**Architecture:** Keep one static `index.html` as the readable content source, with stable anchors for lessons and new reference sections. Replace presentation-heavy styling with a documentation layout: compact sidebar, main lesson column, and responsive quick-facts rail. Keep Markdown files as archives and update `AGENTS.md` with the new information architecture.

**Tech Stack:** Semantic HTML, CSS, static local files, no JavaScript or external dependencies.

## Global Constraints

- Preserve Vietnamese technical content and the canonical STM32 values: `72 MHz`, `PSC=71`, `f_CNT=1 MHz`, `T_tick=1 us`, `ARR=99`, `10 kHz`, ADC `0..4095`, Pulse `0..99`.
- Preserve the fixed pin plan and existing stable anchors where practical.
- Keep `index.html` as the primary readable surface; Markdown remains archive/source material.
- Do not modify STM32 firmware files.
- Keep the page usable on desktop and mobile without clipped tables or code.
- Do not add JavaScript, search, persistence, or external dependencies.

---

### Task 1: Rebuild the workbook information architecture

**Files:**
- Modify: `stm32-motor-notes/index.html`

**Interfaces:**
- Produces stable anchors: `#dashboard`, `#timer`, `#pwm`, `#adc`, `#l298n`, `#firmware-modules`, `#reference`, `#build-flash`, `#later`, `#archive`.
- Produces content groups: Dashboard, Learn, Reference, Later/Archive.

- [x] Replace the oversized header with a compact documentation header containing the project name, current status, and one-line purpose.
- [x] Restructure the sidebar into grouped links for Dashboard, Learn, Reference, and Later/Archive.
- [x] Add a Dashboard section with project state, signal flow, pin snapshot, current learning task, and progress labels.
- [x] Keep the existing Timer, PWM, ADC, L298N, and Firmware Modules content under their stable anchors, removing duplicate paragraphs when encountered.
- [x] Add Reference sections for pin map, formula sheet, HAL call flow, project file map, and Build/Flash commands.
- [x] Add a Later section that clearly marks encoder, speed measurement, filtering, closed-loop, OLED, and HC-SR04 as future topics.
- [x] Keep Markdown archive links in a final Archive section.

### Task 2: Rebuild documentation-first styling

**Files:**
- Modify: `stm32-motor-notes/styles.css`

**Interfaces:**
- Consumes the semantic groups and classes from Task 1.
- Produces responsive desktop/mobile layout with compact documentation surfaces.

- [x] Remove hero-like scale, excessive card shadows, and repeated marketing-style spacing.
- [x] Create a fixed desktop sidebar, readable main content column, and restrained quick-facts rail.
- [x] Make the sidebar stack or become a compact top navigation below the mobile breakpoint.
- [x] Style tables, formulas, code blocks, warnings, status labels, and checkpoints as technical reference elements.
- [x] Ensure code and tables scroll horizontally inside their own containers instead of expanding the page.
- [x] Use the existing light palette with restrained blue/green/amber accents and accessible contrast.
- [x] Add print-friendly behavior so the main notes remain readable when printed or saved as PDF.

### Task 3: Align agent instructions with the workbook

**Files:**
- Modify: `AGENTS.md`

**Interfaces:**
- Records the final information architecture and maintenance expectations for future agents.

- [x] Replace the old landing-page/workbook wording with the final Dashboard/Learn/Reference/Later structure.
- [x] State that new concepts must update the relevant lesson and reference section in `index.html` in the same turn.
- [x] State that future UI edits must preserve stable anchors, responsive behavior, and the compact documentation style.
- [x] Keep the existing firmware architecture, pin plan, teaching sequence, and build/flash facts intact.

### Task 4: Verify the static notebook

**Files:**
- Test: `stm32-motor-notes/index.html`
- Test: `stm32-motor-notes/styles.css`

- [x] Run `git diff --check` for edited documentation files.
- [x] Verify all sidebar anchors resolve with `rg 'href=' stm32-motor-notes/index.html`.
- [x] Verify canonical formulas, pins, module names, and build commands remain present with targeted `rg` checks.
- [x] Scan for `TODO`, `TBD`, `lorem`, or placeholder content.
- [x] Validate the HTML with `xmllint` and inspect responsive CSS/overflow rules statically; no browser tool was available in this session.
