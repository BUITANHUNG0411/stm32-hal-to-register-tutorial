# STM32 Notebook Reference Workbook Design

## Goal

Restructure `stm32-motor-notes/index.html` from a long presentation-style landing page into a Vietnamese technical workbook that supports both sequential learning and fast lookup while coding.

## Audience and Use Modes

- The learner reads lessons in order when learning a new STM32 concept.
- The learner jumps directly to pins, formulas, HAL flow, files, or build commands while implementing.
- The page remains static HTML/CSS with no required JavaScript or external dependency.

## Information Architecture

The sidebar becomes a compact documentation index with four groups:

1. Dashboard: project status, signal flow, current task, and essential facts.
2. Learn: Timer, PWM, ADC, L298N, and HAL/module workflow in the current teaching order.
3. Reference: pin map, formula sheet, HAL call flow, project file map, and build/flash commands.
4. Later/Archive: encoder, closed-loop topics, and Markdown source/archive links.

The main content uses short technical sections rather than a hero. Each learning topic follows the same pattern: concept, project example, formula/table, firmware relation, and one checkpoint. Reference sections prioritize dense tables, code identifiers, and copyable commands.

## Layout

- Desktop: fixed narrow sidebar, readable main column, and a restrained right-side `Quick facts` rail for the current page or global project facts.
- Mobile: sidebar becomes a compact top navigation; the quick-facts rail stacks below the main content.
- Use full-width documentation bands and small framed blocks only for repeated facts, warnings, formulas, and commands.
- Reduce decorative shadows, oversized headings, and repeated card shells. Keep the existing light technical palette and accessible contrast.
- Preserve stable anchors so existing links such as `#timer`, `#pwm`, `#adc`, `#l298n`, and `#firmware-modules` continue to work where practical.

## Content Rules

- Dashboard states the current project truth: open-loop potentiometer -> ADC -> PWM -> L298N -> GA37 motor.
- The canonical numbers remain `72 MHz`, `PSC=71`, `f_CNT=1 MHz`, `T_tick=1 us`, `ARR=99`, `10 kHz`, ADC `0..4095`, and Pulse `0..99`.
- Firmware reference documents the current modules: `adc_reader`, `motor_pwm`, `motor_driver`, and `app_motor_control`, including each public responsibility.
- Build/flash reference documents CMake Presets, ELF/BIN conversion, ST-Link, and the `0x08000000` flash address.
- Progress labels distinguish `Đã hiểu`, `Đang làm`, and `Học sau`; do not imply encoder or closed-loop work is complete.
- `AGENTS.md` records this information architecture, maintenance rules, and the current STM32 project state so future agents continue the same structure.

## Out of Scope

- No JavaScript search, filtering, persistence, or progress tracking.
- No new hardware peripherals or pin changes.
- No changes to STM32 firmware source files in this notebook redesign.
- Markdown files remain lightweight archive/source files; the HTML remains the primary readable surface.

## Acceptance Criteria

- Opening `index.html` immediately shows a compact dashboard rather than a marketing-style hero.
- A learner can reach any core concept or reference area from the sidebar without scrolling through all lessons.
- A learner can see the project pin map, formulas, module responsibilities, and build/flash flow without opening Markdown.
- Desktop and mobile layouts remain readable, with no clipped code, tables, or navigation labels.
- `AGENTS.md` explicitly tells future agents to preserve the workbook/reference structure and update the HTML when new knowledge is taught.
