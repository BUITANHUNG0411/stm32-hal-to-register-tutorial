# STM32 Timer PWM Notebook Design

Date: 2026-07-31

## Goal

Create a small learning notebook for an Automotive Engineering student learning STM32 timer and PWM basics. The notebook must explain enough to understand LED dimming and DC motor speed control using the current STM32F103C8T6 project context.

The first version focuses only on Timer, PWM, ARR, CCR/Pulse, duty cycle, LED behavior, and DC motor behavior through L298N. Encoder, ADC throttle mapping, closed-loop speed control, OLED, and HC-SR04 are out of scope for this first version.

## Format

Use a combined format:

- Markdown files are the source notes, easy to edit and extend after each lesson.
- A basic static web page is the visual notebook, optimized for colorful summaries and clear waveform diagrams.

Proposed structure:

```text
stm32-motor-notes/
├── README.md
├── index.html
├── styles.css
└── notes/
    ├── 01-timer.md
    ├── 02-pwm.md
    ├── 03-led-control.md
    └── 04-dc-motor-control.md
```

## Content

The first notebook version covers:

1. Timer fundamentals
   - System clock at 72 MHz.
   - Prescaler `PSC = 71`.
   - Counter tick frequency `f_CNT = 1 MHz`.
   - One tick equals `1 us`.
   - `ARR = 99` means the counter counts `0..99`, so one period has 100 ticks.

2. PWM fundamentals
   - PWM as fast HIGH/LOW switching.
   - PWM period and frequency.
   - With `ARR = 99`, one PWM period is `100 us`.
   - PWM frequency is `10 kHz`.
   - `CCR1` / `Pulse` controls how long PA8 stays HIGH in each PWM period.

3. Waveform diagrams
   - Clear horizontal HIGH/LOW diagrams for 0%, 25%, 50%, 75%, and near-100% duty.
   - Each diagram shows time from `0 us` to `100 us`.
   - Each diagram connects `CCR1` value to HIGH time and LOW time.

4. LED control
   - Low duty makes the LED dim.
   - High duty makes the LED bright.
   - Human eyes perceive the average brightness because PWM is fast.

5. DC motor control
   - PA8 PWM connects to L298N ENA.
   - PWM duty controls approximate average motor power.
   - IN1/IN2 choose direction or stop/brake behavior.
   - Motor supply must come from the external 12 V source.
   - STM32 and L298N must share GND.

6. Common confusions
   - `ARR = 99` does not mean 10,000 counts per PWM period.
   - 10,000 is the number of PWM cycles per second at 10 kHz.
   - `f_CNT` is the counter tick speed.
   - `f_PWM` is the full PWM-cycle repetition speed.
   - `CCR1` changes duty cycle, not PWM frequency.

## Web Design

The web page should feel like a colorful technical notebook, not a landing page. It should open directly into the learning content.

Design characteristics:

- Vietnamese text.
- Light background with clear colored topic sections.
- Compact formulas and tables.
- Oscilloscope-like waveform panels.
- No heavy framework needed.
- No JavaScript required for version 1 unless a small interaction clearly improves understanding.

## Success Criteria

The notebook is successful when the learner can answer:

- What does a timer counter do?
- Why does `PSC = 71` produce a `1 MHz` counter tick from `72 MHz`?
- Why does `ARR = 99` create 100 ticks per PWM period?
- Why is the PWM period `100 us` and frequency `10 kHz`?
- What does `CCR1` control?
- How does duty cycle affect LED brightness?
- How does PWM duty affect approximate DC motor power through L298N?

## Validation

Verify the files exist and can be opened locally. For the static page, inspect the HTML/CSS enough to confirm the page references are correct and the waveform diagrams are present.
