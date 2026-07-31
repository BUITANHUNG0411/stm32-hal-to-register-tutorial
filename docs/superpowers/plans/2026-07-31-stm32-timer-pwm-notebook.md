# STM32 Timer PWM Notebook Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a combined Markdown and static web notebook that explains STM32 Timer/PWM concepts clearly enough for LED dimming and DC motor control.

**Architecture:** Markdown files hold the source learning notes. A standalone `index.html` plus `styles.css` presents the same ideas visually with colored sections, tables, and oscilloscope-style waveform diagrams. The notebook is static and can be opened directly in a browser.

**Tech Stack:** HTML, CSS, Markdown. No JavaScript and no external dependencies.

## Global Constraints

- Vietnamese text.
- Current project context: STM32F103C8T6 Blue Pill, system clock 72 MHz.
- Timer example: TIM1 PWM on PA8, `PSC = 71`, `ARR = 99`, `f_CNT = 1 MHz`, `T_tick = 1 us`, `T_PWM = 100 us`, `f_PWM = 10 kHz`.
- Keep focus on Timer, PWM, ARR, CCR/Pulse, duty cycle, LED behavior, and DC motor behavior through L298N.
- Do not cover ADC throttle mapping, encoder mode, closed-loop control, OLED, or HC-SR04 in version 1.
- No heavy framework.
- No JavaScript required for version 1.

---

### Task 1: Notebook Markdown Source

**Files:**
- Create: `stm32-motor-notes/README.md`
- Create: `stm32-motor-notes/notes/01-timer.md`
- Create: `stm32-motor-notes/notes/02-pwm.md`
- Create: `stm32-motor-notes/notes/03-led-control.md`
- Create: `stm32-motor-notes/notes/04-dc-motor-control.md`

**Interfaces:**
- Consumes: Approved design spec at `docs/superpowers/specs/2026-07-31-stm32-timer-pwm-notebook-design.md`.
- Produces: Markdown note files linked by `README.md`; the web page in Task 2 mirrors these ideas.

- [ ] **Step 1: Create the notebook README**

Create `stm32-motor-notes/README.md` with a title, purpose, study order, hardware context, and links to the four note files.

- [ ] **Step 2: Create Timer note**

Create `stm32-motor-notes/notes/01-timer.md` explaining timer, system clock, PSC, `f_CNT`, tick time, ARR, inclusive counting, and the exact `72 MHz -> 1 MHz -> 100 us` example.

- [ ] **Step 3: Create PWM note**

Create `stm32-motor-notes/notes/02-pwm.md` explaining PWM, period, frequency, duty cycle, CCR1/Pulse, and text waveform diagrams for 0%, 25%, 50%, 75%, and near-100%.

- [ ] **Step 4: Create LED note**

Create `stm32-motor-notes/notes/03-led-control.md` explaining LED dimming by average brightness and why PWM frequency matters for visible flicker.

- [ ] **Step 5: Create DC motor note**

Create `stm32-motor-notes/notes/04-dc-motor-control.md` explaining PA8 to L298N ENA, duty as approximate average motor power, IN1/IN2 direction logic, shared GND, and external motor supply.

- [ ] **Step 6: Validate Markdown source files**

Run: `find stm32-motor-notes -maxdepth 3 -type f | sort`

Expected: Output lists `README.md` and all four files under `notes/`.

---

### Task 2: Static Visual Web Notebook

**Files:**
- Create: `stm32-motor-notes/index.html`
- Create: `stm32-motor-notes/styles.css`

**Interfaces:**
- Consumes: Markdown source notes from Task 1.
- Produces: A static, browser-openable visual notebook with clear waveform panels and topic sections.

- [ ] **Step 1: Create HTML structure**

Create `stm32-motor-notes/index.html` with:

- Header naming the notebook.
- Navigation links to Timer, PWM, LED, and motor sections.
- Formula cards for `f_CNT`, `T_PWM`, and `f_PWM`.
- Waveform panels for `CCR1 = 0`, `25`, `50`, `75`, and `99`.
- Summary table comparing ARR, CCR1, duty, HIGH time, LOW time, and behavior.
- Safety note for L298N shared GND and external 12 V motor supply.

- [ ] **Step 2: Create CSS styling**

Create `stm32-motor-notes/styles.css` with:

- Light notebook-like page styling.
- Clear colored sections that are not dominated by a single hue.
- Responsive layout for desktop and mobile.
- Waveform rows with stable dimensions and readable labels.
- Print-friendly basics through normal semantic HTML.

- [ ] **Step 3: Validate local links**

Run: `rg 'href=' stm32-motor-notes/index.html`

Expected: Links include `styles.css`, section anchors, and the Markdown notes.

- [ ] **Step 4: Validate waveform content**

Run: `rg 'CCR1 =|Duty|HIGH|LOW|100 us|10 kHz' stm32-motor-notes/index.html stm32-motor-notes/styles.css`

Expected: Output shows waveform labels, formulas, and timing text.

---

### Task 3: Final Verification

**Files:**
- Inspect: `stm32-motor-notes/index.html`
- Inspect: `stm32-motor-notes/styles.css`
- Inspect: `stm32-motor-notes/notes/*.md`

**Interfaces:**
- Consumes: All files from Tasks 1 and 2.
- Produces: Verified notebook ready to open locally.

- [ ] **Step 1: Check file tree**

Run: `find stm32-motor-notes -maxdepth 3 -type f | sort`

Expected: The tree includes `README.md`, `index.html`, `styles.css`, and the four Markdown notes.

- [ ] **Step 2: Check for unfinished markers**

Run: `rg 'TODO|TBD|lorem|placeholder' stm32-motor-notes docs/superpowers/specs/2026-07-31-stm32-timer-pwm-notebook-design.md`

Expected: No matches.

- [ ] **Step 3: Check page references**

Run: `rg 'notes/01-timer.md|notes/02-pwm.md|notes/03-led-control.md|notes/04-dc-motor-control.md|styles.css' stm32-motor-notes/index.html stm32-motor-notes/README.md`

Expected: Output confirms the web page and README link to the notes and stylesheet.

- [ ] **Step 4: Open instruction**

The static notebook is ready when `stm32-motor-notes/index.html` can be opened directly in a browser.
