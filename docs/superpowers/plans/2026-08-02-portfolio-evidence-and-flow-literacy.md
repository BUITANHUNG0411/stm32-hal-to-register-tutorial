# STM32 Portfolio Evidence and Flow Literacy Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make the static STM32 workbook accurately present project evidence status and teach the distinctions between the repository tree, runtime/module calls, and hardware signal flow.

**Architecture:** Update only `stm32-motor-notes/index.html`. Reuse its dashboard, task strip, lesson block, table, checkpoint, and sidebar patterns; do not add JavaScript, diagram syntax, external assets, or firmware content.

**Tech Stack:** Static semantic HTML and existing local CSS.

## Global Constraints

- Keep `stm32-motor-notes/index.html` as the primary Vietnamese learning surface.
- Preserve the fixed pin plan and canonical values: `72 MHz`, `PSC = 71`, `f_CNT = 1 MHz`, `T_tick = 1 us`, `ARR = 99`, `10 kHz`, ADC `0..4095`, and Pulse `0..99`.
- Do not modify `stm32-motor-controller/`, do not create diagrams, and do not write a portfolio README/case study.
- Describe the manual hardware test as documented in the firmware README; distinguish it from evidence files that are absent from the repository.
- Keep existing anchors and use no JavaScript or external dependency.

---

### Task 1: Make dashboard status evidence-first

**Files:**
- Modify: `stm32-motor-notes/index.html`, dashboard quick-grid and `#current-task` sections.
- Test: `stm32-motor-notes/index.html` static-content checks.

**Interfaces:**
- Consumes: current project facts in `stm32-motor-controller/README.md` and the existing dashboard status labels.
- Produces: a truthful manual-test status and an evidence-collection checkpoint for portfolio preparation.

- [ ] **Step 1: Confirm the current source of truth**

Read `stm32-motor-controller/README.md` and confirm that it states a manual hardware test for potentiometer-controlled open-loop speed, while the repository has no stored wiring image, motor-test media, PWM capture, or formal test-result table.

- [ ] **Step 2: Update the dashboard status copy**

Replace the generic in-progress motor-test item with wording that says the open-loop path has a documented manual hardware-test claim and that portfolio artefacts are still to be collected. Keep the encoder item explicitly in the later state.

- [ ] **Step 3: Update the current-task strip**

Replace the execution-oriented test prompt with an evidence collection list: wiring/board photo; motor response at low, middle, and high throttle; PWM capture if an oscilloscope or logic analyser is available; and a direction/duty observation table. Retain the existing common-GND, external-motor-power, and no-Blue-Pill-motor-power warning.

- [ ] **Step 4: Run focused content checks**

Run `rg -n 'manual|evidence|wiring|PWM|encoder|Chung GND|Blue Pill' stm32-motor-notes/index.html`.

Expected: evidence-first status is present, safety wording remains, and encoder remains a future topic.

- [ ] **Step 5: Commit**

Run `git add stm32-motor-notes/index.html` followed by `git commit -m "docs: clarify motor test evidence status"`.

### Task 2: Add the project-tree and flow-literacy lesson

**Files:**
- Modify: `stm32-motor-notes/index.html`, sidebar Learn links and content immediately after `#firmware-modules`.
- Test: `stm32-motor-notes/index.html` anchor and content checks.

**Interfaces:**
- Consumes: actual paths `Core/Inc`, `Core/Src`, `cmake/stm32cubemx/CMakeLists.txt`; current modules; and the open-loop call/signal facts.
- Produces: one new learning anchor and an explanatory comparison that prepares the learner to draw diagrams later.

- [ ] **Step 1: Add the Learn navigation entry**

Add a sidebar link under Learn that targets a new, uniquely named flow-literacy anchor. Do not rename or remove any existing anchor.

- [ ] **Step 2: Add one compact comparison lesson**

Insert the lesson after `#firmware-modules`. Its table must distinguish: project tree answers where files live; runtime/module flow answers which startup/update calls lead to which modules; hardware signal flow answers how the potentiometer command reaches PA0/ADC, becomes PA8 PWM at L298N ENA, and works with PB12/PB13 direction signals. Use actual project names, not invented folders or functions.

- [ ] **Step 3: Add a learner checkpoint**

Add one concise checkpoint explaining that the three views may describe the same project but answer different questions. State that diagram creation and README/case-study writing occur only after the learner can identify the correct view.

- [ ] **Step 4: Run focused content and anchor checks**

Run `rg -n 'href=|project tree|runtime|hardware signal|Core/Inc|Core/Src|app_motor_control_update|PA8|PB12' stm32-motor-notes/index.html`.

Expected: one sidebar link and one lesson contain all three viewpoints; existing navigation anchors remain.

- [ ] **Step 5: Commit**

Run `git add stm32-motor-notes/index.html` followed by `git commit -m "docs: teach project tree and control flows"`.

### Task 3: Validate the static notebook and scope

**Files:**
- Modify: none.
- Test: `stm32-motor-notes/index.html`, `stm32-motor-notes/styles.css`, and repository status.

**Interfaces:**
- Consumes: the completed HTML content from Tasks 1 and 2.
- Produces: verification evidence that the notebook remains structurally sound and within scope.

- [ ] **Step 1: Check edited-file whitespace and HTML structure**

Run `git diff --check HEAD~2..HEAD -- stm32-motor-notes/index.html` and `xmllint --html --noout stm32-motor-notes/index.html`.

Expected: no diff-whitespace errors; `xmllint` exits successfully or reports only HTML5-specific parser warnings that do not identify malformed nesting.

- [ ] **Step 2: Check canonical technical facts and prohibited scope**

Run `rg -n '72 MHz|PSC = 71|1 MHz|1 us|ARR = 99|10 kHz|0..4095|0..99|PA0|PA8|PB12|PB13' stm32-motor-notes/index.html`, then `rg -n 'TODO|TBD|lorem|placeholder|mermaid|plantuml' stm32-motor-notes/index.html`, then `git diff --name-only HEAD~2..HEAD`.

Expected: all canonical facts remain; placeholder/diagram-syntax search has no matches; the changed implementation file is only the notebook HTML.

- [ ] **Step 3: Inspect final git state and report evidence**

Run `git status --short` and `git log -3 --oneline`.

Expected: report any intentionally uncommitted user files separately; do not claim a clean worktree unless command output proves it.
