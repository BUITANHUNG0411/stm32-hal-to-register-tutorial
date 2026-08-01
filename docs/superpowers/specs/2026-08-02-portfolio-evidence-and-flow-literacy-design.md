# STM32 Portfolio Evidence and Flow Literacy Design

## Goal

Update the Vietnamese STM32 learning notebook so it accurately separates the current open-loop project's documented manual hardware test from evidence that has not yet been saved in the repository, and introduce the three viewpoints needed before drawing portfolio diagrams: project tree, runtime/module call flow, and hardware signal flow.

## Audience and Use Modes

- The learner uses the dashboard to see the project's truthful scope and the next portfolio-ready evidence to collect.
- The learner studies the new lesson before drawing any diagram or writing a portfolio README/case study.
- The page remains a static Vietnamese technical workbook with `index.html` as the primary reading surface.

## Content Design

### Evidence-first project status

The dashboard will retain the existing source-backed open-loop facts: STM32F103C8T6, potentiometer input, ADC-to-PWM mapping, fixed forward direction at startup, and the README's manual hardware-test claim.

It will explicitly distinguish that claim from repository evidence. The notebook will identify the absent portfolio artefacts: wiring/board photograph, motor-test photo or video, PWM measurement capture, and a recorded direction/duty test table. It will not claim that any missing artefact exists. Existing hardware safety text and the fixed pin plan remain unchanged.

The current-task strip will become an evidence-collection checkpoint. It will direct the learner to capture and label evidence after safe testing, without changing the firmware workflow or requiring a new peripheral.

### Flow-literacy lesson

Add one learning section directly after `#firmware-modules`. It will contrast three non-interchangeable views using actual project paths, modules, functions, pins, and hardware:

| View | Question answered | Project anchor |
| --- | --- | --- |
| Project tree | Where does each file live? | `Core/Inc`, `Core/Src`, CMake source list |
| Runtime/module call flow | Which startup and update calls lead to which module? | `main.c`, `app_motor_control_update`, ADC/PWM modules |
| Hardware signal flow | How does the physical command/power-related signal path move? | potentiometer, PA0, ADC, PA8, L298N ENA, GA37; PB12/PB13 for direction |

This is explanatory content only. It will not add Mermaid, PlantUML, draw.io output, generated diagrams, firmware code, or new pin assignments. A checkpoint will state which of the three views is appropriate for a given portfolio question.

## Information Architecture

- Add one sidebar entry under Learn for the new lesson anchor, named for project tree and flows.
- Preserve the established anchors: `#dashboard`, `#current-task`, `#timer`, `#pwm`, `#adc`, `#l298n`, `#firmware-modules`, `#reference`, `#build-flash`, `#later`, and `#archive`.
- Preserve the existing documentation-first visual system. Reuse existing tables, lesson blocks, checkpoints, status labels, and callouts when possible. No JavaScript, dependencies, or overall visual redesign.

## Scope Boundaries

- Modify only the static notebook's content surface unless a minimal existing-style CSS selector is necessary for readability.
- Do not modify files in `stm32-motor-controller/`.
- Do not modify the fixed pin plan, canonical PWM values, or statements about encoder/OLED/HC-SR04 being later/reserved.
- Do not create diagrams or begin README/case-study writing in this update.

## Acceptance Criteria

- The page describes the current manual test as a README claim and lists repository evidence still absent, without overstating test proof.
- The dashboard/task area gives the learner a concrete evidence-collection next step.
- The new lesson distinguishes project tree, runtime/module call flow, and hardware signal flow with examples drawn from the actual repository.
- Sidebar navigation reaches the new lesson while all existing anchors remain.
- The notebook remains valid static HTML, contains no placeholder copy, and preserves the canonical pins, formulas, module names, and build/flash commands.
