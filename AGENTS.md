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
- Preserve the current reference-workbook layout in `index.html`: compact sidebar groups, a small dashboard, numbered learning sections, dense reference tables, and a responsive quick-lookup flow. Do not turn it back into a landing page with a large marketing hero.
- The current information architecture is: `Dashboard` (`#dashboard`, `#current-task`), `Learn` (`#timer`, `#pwm`, `#adc`, `#l298n`, `#firmware-modules`), `Reference` (`#reference`, `#build-flash`), and `Later/Archive` (`#later`, `#archive`). Preserve these anchors when editing content.
- The page supports two modes at once: sequential learning through the Learn group and quick lookup through Reference desk tables/formulas/commands. New content should state which mode it serves.
- Prefer documentation surfaces over presentation cards: compact tables, equations, status labels, callouts, checklists, and code blocks. Keep shadows and decorative spacing restrained.
- Keep `/notes/*.md` as source/archive links only. The learner should not need to open Markdown to understand the lesson.
- When teaching HAL/C firmware workflow and the learner answers correctly, immediately add the confirmed knowledge to `stm32-motor-notes/index.html`.

The HTML/CSS visual contract:

- `index.html` remains static and does not require JavaScript or external dependencies.
- `styles.css` must keep the fixed desktop sidebar, compact mobile navigation, readable main column, technical tables, and horizontally scrollable code/table content on small screens.
- Keep `index.html` as the primary readable document; `/notes/*.md` are source/archive links only.

## Firmware Architecture Rule

The learner explicitly does not want all application code placed in `main.c`. Teach and implement the STM32 HAL project using small C modules, each with a `.h` public interface and `.c` implementation.

Recommended module split for the first open-loop motor project:

| Module | Responsibility |
| --- | --- |
| `adc_reader.h/.c` | Read `ADC1_IN0` on `PA0` and return raw ADC values `0..4095`. It must not know about PWM `ARR` or motor duty. |
| `motor_pwm.h/.c` | Manage `TIM1_CH1`: start PWM on `TIM_CHANNEL_1`, clamp/write `Pulse/CCR1` in the valid range, currently `0..99`. |
| `motor_driver.h/.c` | Manage L298N direction pins `PB12/PB13`: forward, reverse, coast/stop, brake if used. |
| `app_motor_control.h/.c` | Coordinate the open-loop application: read ADC, map raw ADC to Pulse using the current ARR/PWM range, set direction, update PWM. |

`main.c` should stay thin:

- Call CubeMX/HAL generated initialization: `HAL_Init`, `SystemClock_Config`, `MX_GPIO_Init`, `MX_ADC1_Init`, `MX_TIM1_Init`.
- Call small module init/start functions after generated init, such as starting PWM.
- In `while (1)`, call one application update function rather than placing all ADC/PWM/GPIO logic inline.
- Put user includes and calls inside CubeMX `USER CODE BEGIN ...` / `USER CODE END ...` regions so regeneration does not delete them. For example, include `adc_reader.h` inside `USER CODE BEGIN Includes`.
- Put repeated loop logic inside `USER CODE BEGIN 3`, not between `USER CODE END WHILE` and `USER CODE BEGIN 3`.

When explaining file creation, prefer the STM32 layout:

```text
Core/Inc/<module>.h
Core/Src/<module>.c
```

For CMake-generated projects, remind the learner that adding a new `.c` file may require ensuring it is included in the generated build target, depending on the generated `CMakeLists.txt` pattern.

Current `motor_driver` review notes:

- `motor_driver.h` uses `GPIO_PinState` for GPIO values and maps the fixed pins as `GPIOB`, `GPIO_PIN_12`, and `GPIO_PIN_13`. These names come from STM32 HAL/CMSIS and require the correct device define, currently `STM32F103xB`; do not redefine them manually.
- Prefer clear names such as `MOTOR_DRIVER_GPIO_PORT`, `MOTOR_DRIVER_IN1_PIN`, and `MOTOR_DRIVER_IN2_PIN`.
- `motor_driver_set_port(in1_state, in2_state)` should be the low-level helper that calls `HAL_GPIO_WritePin`; `motor_driver_set_direction(direction)` should select a direction and call that helper, not call itself recursively.
- Every `switch` case must terminate with `break` or `return` unless deliberate fall-through is being taught.
- Keep the L298N mapping explicit: forward `(SET, RESET)`, reverse `(RESET, SET)`, coast/stop `(RESET, RESET)`, brake `(SET, SET)`. Do not silently swap `STOP` and `BRAKE`.
- In this CMake project, custom sources such as `adc_reader.c`, `motor_pwm.c`, and `motor_driver.c` must be added to `MX_Application_Src` in `cmake/stm32cubemx/CMakeLists.txt`; headers are not added as build sources.
- The current `motor_driver.c` logic is considered correct at the basic level when it maps `FORWARD` to `(1,0)`, `REVERSE` to `(0,1)`, `BRAKE` to `(1,1)`, and `STOP` to `(0,0)`, with a terminating `break` in every `switch` case. Prefer `GPIO_PIN_SET` / `GPIO_PIN_RESET` over bare `1` / `0` when teaching the clearer HAL form.
- Next teaching step after the module review: guide the learner to include `motor_driver.h` in a CubeMX `USER CODE BEGIN Includes` block and call `motor_driver_set_direction(MOTOR_DRIVER_STOP)` in `USER CODE BEGIN 2`, then build before integrating ADC and PWM again. Do not write the learner's code for this step unless explicitly requested.
- Current build/flash workflow: use `cmake --preset Debug`, then `cmake --build --preset Debug`; the expected ELF is `build/Debug/stm32-motor-controller.elf`. Convert it to BIN with `arm-none-eabi-objcopy -O binary ...elf ...bin`, then flash via ST-Link at `0x08000000`. Keep `BOOT0 = 0`, disconnect motor power during flashing, and never power the motor from the Blue Pill.
- After flashing, test in layers: with `MOTOR_DRIVER_STOP`, verify PB12/PB13 are LOW before connecting motor power; then set `MOTOR_DRIVER_FORWARD`, start with the potentiometer at minimum, share STM32/L298N GND, power the motor from the external supply, and increase throttle slowly. PA8 PWM duty is best checked with an oscilloscope or logic analyzer.
- LED testing on PB12/PB13 is optional, only a visual check of GPIO HIGH/LOW; it is not required before progressing.
- Current project progression toward the open-loop potentiometer -> L298N -> motor demo: build/flash, verify ADC raw `0..4095`, verify ADC-to-Pulse mapping `0..99`, verify direction GPIO, connect common GND and external motor power, test one direction at low duty, then extract the loop into `app_motor_control.c`. Do not introduce encoder feedback until this open-loop path is stable.

Confirmed HAL/channel knowledge:

- `PA8 = TIM1_CH1`, so HAL must use `TIM_CHANNEL_1` for the motor PWM channel.
- `MX_TIM1_Init()` configures TIM1 but does not make PWM output run by itself.
- PWM output starts only after starting PWM for `TIM1` + `TIM_CHANNEL_1`.
- With CubeMX `Pulse = 0`, PWM starts at duty near `0%`, so the motor should not rotate until code updates `CCR1/Pulse`.
- CubeMX-generated handles such as `hadc1` should be reused by modules. For the first beginner-friendly pass, prefer explicit calls like `adc_reader_read(&hadc1)` rather than hiding the handle too early.
- For software-triggered ADC reads, teach the HAL order as: `HAL_ADC_Start(hadc)`, then `HAL_ADC_PollForConversion(hadc, timeout)`, then `HAL_ADC_GetValue(hadc)`.

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
