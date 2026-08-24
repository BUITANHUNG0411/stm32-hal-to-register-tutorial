# PRD.md

**Phiên bản:** 1.2 (ghi đè toàn bộ nội dung 1200 lines)  
**Ngày:** 2026-08-24  
**Mục đích:** Web hướng dẫn sinh viên STM32 Register từ A-Z (chỉ phần chuyển từ HAL sang Register)

## 1. Mục tiêu dự án
- Tạo web hướng dẫn A-Z để sinh viên học lập trình thanh ghi STM32 (Register) qua ví dụ thực tế motor controller.
- **Repository firmware tham chiếu bắt buộc:** [BUITANHUNG0411/stm32-motor-controller](https://github.com/BUITANHUNG0411/stm32-motor-controller)
- **Scope chính xác** (đã confirm):
  - Giữ nguyên toàn bộ phần **init** từ CubeMX (`HAL_Init`, `SystemClock_Config`, `MX_GPIO_Init`, `MX_ADC1_Init`, `MX_TIM1_Init`, và MSP init).
  - Chuyển toàn bộ phần còn lại sang Register:
    - `while(1)`
    - các driver tự viết (`adc_reader`, `motor_pwm`, `motor_driver`)
    - `app_motor_control`.

## 2. Repository firmware tham chiếu

### 2.1 Target phần cứng và chức năng
- MCU: **STM32F103C8T6**.
- Motor: **GA37 DC motor**.
- Driver: **L298N**.
- Input: potentiometer đọc bằng **ADC1**, giá trị 12-bit `0..4095`.
- Output tốc độ: **TIM1 Channel 1 PWM**, chu kỳ `0..99`, tần số mục tiêu 10 kHz.
- Điều khiển chiều/phanh/dừng: GPIOB `PB12` (`IN1`) và `PB13` (`IN2`).
- Kiểu điều khiển: **open-loop**; chưa có encoder, đo tốc độ hay closed-loop.

### 2.2 Luồng xử lý thực tế trong repo
```text
CubeMX/HAL init
  -> motor_pwm_start(&htim1)
  -> motor_driver_set_direction(MOTOR_DRIVER_FORWARD)
  -> while (1)
       -> app_motor_control_update(&hadc1, &htim1)
            -> adc_reader_read_raw(hadc)
            -> pulse = adc_raw * 99 / 4095
            -> motor_pwm_set_pulse(htim, pulse)
```

### 2.3 File nguồn cần bám sát
- `Core/Src/main.c`: init CubeMX, khởi động PWM, đặt chiều mặc định, vòng lặp chính.
- `Core/App/app_motor_control.c`: glue ADC → PWM.
- `Core/Drivers/adc_reader/adc_reader.c`: `HAL_ADC_Start`, `HAL_ADC_PollForConversion(..., 100)`, `HAL_ADC_GetValue`.
- `Core/Drivers/motor_pwm/motor_pwm.c`: `HAL_TIM_PWM_Start`, cập nhật compare và clamp tối đa `99`.
- `Core/Drivers/motor_driver/motor_driver.c`: ghi `PB12/PB13`; forward `1/0`, reverse `0/1`, brake `1/1`, stop `0/0`.
- `Core/Inc/main.h` và `stm32-motor-controller.ioc`: nguồn xác minh cấu hình/pin CubeMX.

### 2.4 Ranh giới chuyển đổi
Giữ nguyên các hàm init do CubeMX sinh ra. Chỉ giải thích/viết lại phần application sau init:
- ADC HAL API → trạng thái/data register của ADC1 (`CR2`, `SR`, `DR`) với clock đã được init.
- `HAL_TIM_PWM_Start` và `__HAL_TIM_SET_COMPARE` → TIM1 (`CR1`, `CCER`, `BDTR/MOE` nếu cần, `CCR1`).
- `HAL_GPIO_WritePin` → GPIOB `BSRR`/`BRR` hoặc `ODR` cho PB12/PB13.
- Logic mapping phải giữ nguyên: `pulse = adc_raw * 99 / 4095`; PWM pulse phải được clamp ở `99`.

### 2.5 Build firmware tham chiếu
```sh
cmake --preset Debug
cmake --build --preset Debug
```
Artifact: `build/Debug/stm32-motor-controller.elf`. Flash qua ST-Link tại `0x08000000`, giữ `BOOT0 = 0`, và tháo nguồn motor khi flash.

## 3. Nền tảng và công nghệ
- Frontend: HTML + Tailwind CSS + vanilla JS
- Hosting: Sẵn sàng deploy GitHub Pages / Vercel
- Code style: Tương đồng repo STM32-motor-controller

## 4. Cấu trúc web
```
register-course/
├── index.html
├── assets/style.css
├── js/copy-code.js
├── docs/reference-register.md
└── PRD.md
```

## 5. Nội dung chính (đã cập nhật scope)
1. Giới thiệu
2. Code HAL hiện tại (toàn bộ phần còn lại sau init)
3. Code Register mới (chỉ phần `while(1)` + driver tự viết)
4. So sánh (bảng + diff)
5. Cách test trên board

## 6. Cấu trúc Claude Code
- `CLAUDE.md`, `.claude/settings.local.json`, `.claude/rules/`, `.claude/skills/`, `.claude/agents/`, Memory.

## 7. Verification
- Mở `index.html` → navigation, copy-code và các code sample hoạt động.
- Đối chiếu mọi code sample với repository firmware tham chiếu, không tự suy diễn tên hàm/pin.
- Firmware reasoning phải chứng minh init CubeMX được giữ nguyên và logic ADC → PWM tương đương.

**Lưu ý:** Scope đã được xác nhận lại. Nếu muốn thêm encoder, closed-loop hoặc tính năng motor control mới, phải update PRD trước.

## 8. Implementation Plan

### 8.1 Context
Biến toàn bộ phần còn lại sau init CubeMX (`while(1)` + `app_motor_control.c` + các driver) sang Register-level programming để sinh viên thực hành trực tiếp với thanh ghi STM32F103C8T6.

### 8.2 Register Mapping
| HAL API | Register-level | Register/bit | Trình tự |
|---------|----------------|--------------|----------|
| `HAL_ADC_Start` / poll / get | Start conversion, poll EOC, read data | `ADC1->CR2`, `ADC1->SR`, `ADC1->DR` | Dùng clock/config đã init; set `SWSTART`, chờ `EOC`, đọc `DR` |
| `HAL_TIM_PWM_Start` | Enable timer/channel output | `TIM1->CR1`, `TIM1->CCER`, `TIM1->BDTR` | Cấu hình sau init; enable channel, `MOE` và `CEN` khi phù hợp |
| `__HAL_TIM_SET_COMPARE` | Set duty compare | `TIM1->CCR1` | Clamp `0..99`, ghi compare |
| `HAL_GPIO_WritePin` | Set/reset GPIO atomically | `GPIOB->BSRR` / `GPIOB->BRR` | PB12/PB13: forward `1/0`, reverse `0/1`, brake `1/1`, stop `0/0` |

### 8.3 Proposed Files
| File | Action | Nội dung |
|------|--------|----------|
| `index.html` | CREATE | Trang chính |
| `src/register-motor.c` | CREATE | Code Register-level minh họa |
| `docs/reference-register.md` | CREATE/UPDATE | Register reference và mapping bám repo |

### 8.4 Execution Order
1. Chuẩn hóa source facts từ repository tham chiếu.
2. Chuẩn hóa content model.
3. Tạo cấu trúc section.
4. Thêm code HAL examples đúng source.
5. Thêm Register examples và register reference.
6. Thêm interaction copy/navigation.
7. Kiểm tra nội dung.
8. Kiểm tra giao diện.
9. Review.
10. Final verification.

## 9. Error Handling and Safety
- ADC timeout: kiểm tra flag EOC và giới hạn thời gian chờ.
- Duty ngoài range: clamp `0..99`.
- Motor direction conflict: kiểm tra common ground L298N và trạng thái IN1/IN2.
- Không thay đổi phần init CubeMX nếu chưa có bằng chứng từ repo tham chiếu.

## 10. Acceptance Criteria
- [ ] Web chạy đúng.
- [ ] Repo tham chiếu được liên kết và mọi ví dụ firmware bám đúng source.
- [ ] Code Register tương đương logic HAL sau init.
- [ ] Navigation và copy code hoạt động.
- [ ] Hướng dẫn test trên board rõ ràng.
- [ ] Không còn unknown blocker.
