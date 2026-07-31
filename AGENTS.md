# Agent Notes For STM32 Motor Notebook

## Project Shape

This repository currently contains a static Vietnamese learning notebook at `stm32-motor-notes/`.

Primary files:

- `stm32-motor-notes/README.md`: entry point, hardware context, learning order, and core formulas.
- `stm32-motor-notes/index.html`: main readable notebook. Treat this as the primary learning surface and keep it updated after each new concept is taught.
- `stm32-motor-notes/styles.css`: styling for the static notebook. No JavaScript is currently used.
- `stm32-motor-notes/notes/01-timer.md`: secondary source/archive for timer counter, PSC, `f_CNT`, tick time, ARR, inclusive counting.
- `stm32-motor-notes/notes/02-pwm.md`: secondary source/archive for PWM period/frequency, `CCR1/Pulse`, duty cycle, waveform examples.
- `stm32-motor-notes/notes/03-led-control.md`: secondary source/archive for PWM as perceived LED brightness.
- `stm32-motor-notes/notes/04-dc-motor-control.md`: secondary source/archive for L298N ENA PWM, IN1/IN2 direction, motor power and common GND.
- `docs/superpowers/specs/2026-07-31-stm32-timer-pwm-notebook-design.md`: original design spec for the notebook.
- `docs/superpowers/plans/2026-07-31-stm32-timer-pwm-notebook.md`: implementation plan used to create the first notebook version.

## Content Maintenance Rule

The learner finds raw Markdown hard to read. Whenever an agent teaches a new project concept, update `stm32-motor-notes/index.html` so the same knowledge is available in the visual web notebook.

Rules:

- Keep `index.html` as the source the learner should open first.
- Do not leave important new knowledge only in chat or only in `/notes/*.md`.
- `/notes/*.md` may remain as lightweight source/archive files, but the web page must contain the readable version.
- Prefer short lesson sections, compact tables, equations, and concrete hardware examples over long prose.
- Keep the learning order incremental; do not add future topics before the learner reaches them unless labeling them clearly as "later".
- If adding a new concept such as ADC, encoder mode, speed measurement, filtering, or closed-loop control, add or update the corresponding section in `index.html` in the same turn.
- Preserve the current workbook layout in `index.html`: sidebar table of contents, numbered lesson pages, compact technical sections. Do not turn it back into a landing page with a large marketing hero.
- Keep `/notes/*.md` as source/archive links only. The learner should not need to open Markdown to understand the lesson.

## Learning Context

Teach in Vietnamese, patiently and incrementally. The learner is an Automotive Engineering student preparing for an Automotive Embedded internship and is still new to STM32 timers, PWM, ADC, GPIO, and encoders.

Project hardware:

- MCU: STM32F103C8T6 Blue Pill, system clock 72 MHz.
- Motor: GA37 DC motor with 6-wire quadrature Hall encoder: M1, M2, VCC, GND, C1, C2.
- Driver: L298N module, motor supply from external 12 V / 10 A source.
- Input: 3-pin potentiometer as throttle.

Fixed pin plan:

| Function | STM32 pin | Role |
| --- | --- | --- |
| Potentiometer wiper | PA0 | ADC1_IN0, 12-bit ADC, 0-4095 |
| Motor PWM / L298N ENA | PA8 | TIM1_CH1 |
| L298N IN1 | PB12 | GPIO output |
| L298N IN2 | PB13 | GPIO output |
| Encoder C1 | PA6 | TIM3_CH1, encoder mode |
| Encoder C2 | PA7 | TIM3_CH2, encoder mode |
| OLED SCL reserved | PB6 | I2C1_SCL |
| OLED SDA reserved | PB7 | I2C1_SDA |
| HC-SR04 Trig reserved | PB0 | GPIO/timer |
| HC-SR04 Echo reserved | PB1 | GPIO/timer |

Do not change this pin plan without first explaining the conflict and asking the user.

## Current Timer/PWM Assumptions

TIM1 provides motor PWM on PA8.

```text
f_TIM1 = 72 MHz
PSC = 71
f_CNT = 72 MHz / (71 + 1) = 1 MHz
T_tick = 1 us
ARR = 99
PWM period = 100 ticks = 100 us
f_PWM = 10 kHz
```

Use these names consistently:

- System Clock: MCU master clock, currently 72 MHz.
- `f_TIM`: clock reaching the timer peripheral.
- `PSC`: divider for the timer clock.
- `f_CNT`: actual rate at which the timer counter increments. Avoid calling this simply `f_TIM`.
- `ARR`: counter maximum/reload value; it defines how many counter ticks make one PWM period.
- `f_PWM`: number of complete PWM periods per second.
- `CCR1/Pulse`: value that decides the HIGH duration inside each PWM period, therefore duty cycle.

Important correction to preserve:

```text
ARR = 99 means 100 counter ticks per PWM cycle because the counter counts 0..99.
10,000 is the number of PWM cycles per second, not the number of timer counts in one PWM cycle.
```

Also preserve these corrections:

- `ARR` is a count limit, not a time value. The PWM period time is `(ARR + 1) x T_tick`.
- When the counter reaches `ARR`, the timer overflows/updates and starts the next counting cycle from `0`.
- PWM is related to `ARR`: `ARR` defines the PWM period/frequency, while `CCR1/Pulse` defines the HIGH time inside that period.
- Duty is a ratio, not a time. Use `Duty = CCR1 / (ARR + 1)` or equivalently `Duty = HIGH_time / T_PWM`.
- `Pulse` is the CubeMX/HAL name for the compare value loaded into `CCR1` for TIM1 channel 1. It is measured in timer counts/ticks, and becomes time only after multiplying by `T_tick`.

The main project numbers are intentionally clean and should remain the canonical configuration. If the learner cannot see the difference between concepts, add a secondary contrast example instead of replacing the project configuration. Good contrast example:

```text
f_TIM = 72 MHz
PSC = 7199
f_CNT = 10 kHz
T_tick = 100 us
ARR = 9
T_PWM = (9 + 1) x 100 us = 1 ms
f_PWM = 1 kHz
CCR1 = 3 -> HIGH time = 3 x 100 us = 300 us -> Duty = 3 / 10 = 30%
```

## Teaching Sequence

Do not jump through the whole project at once. Move only after the learner understands the current item.

1. Firmly distinguish `f_CNT`, `T_PWM`, and `f_PWM`.
2. Explain `CCR1/Pulse` with `ARR = 99`, such as `CCR1 = 50` meaning about 50 us HIGH and about 50% duty.
3. Derive throttle mapping from ADC range `0..4095` to PWM Pulse range `0..99`.
4. Explain L298N direction logic:
   - `IN1 = 1`, `IN2 = 0`: one direction.
   - `IN1 = 0`, `IN2 = 1`: reverse direction.
   - `IN1 = 0`, `IN2 = 0`: coast/stop behavior.
   - `IN1 = 1`, `IN2 = 1`: brake behavior, with module/driver caveats.
5. Later only: TIM3 encoder mode, measured speed, filtering, closed-loop control, OLED, and HC-SR04.

## Teaching Style

- Use Socratic, incremental teaching.
- Ask one meaningful question and wait for the learner's response.
- Do not dump a whole lesson, project plan, or multiple questions at once.
- Do not output raw code until the learner explicitly asks to implement.
- Focus on physical behavior, CubeMX/HAL roles, register concepts, and math.
- Correct wrong answers plainly but kindly, identify the exact confusion, then ask a smaller follow-up.
- Relate abstract concepts back to the real motor control hardware.
- Keep PWM at 10 kHz unless explaining why it should change.
- Mention safety facts when relevant: STM32 and L298N must share GND; motor power comes from the external supply; do not power the motor from the Blue Pill.

## Verification Commands

For this static notebook, useful checks are:

```text
find stm32-motor-notes -maxdepth 3 -type f | sort
rg 'href=' stm32-motor-notes/index.html
rg 'CCR1 =|Duty|MUC CAO|MỨC CAO|MỨC THẤP|100 us|10 kHz' stm32-motor-notes
rg 'TODO|TBD|lorem|placeholder' stm32-motor-notes docs/superpowers
```
