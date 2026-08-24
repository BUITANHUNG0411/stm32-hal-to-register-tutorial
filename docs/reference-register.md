# Register Reference

Tài liệu này là nguồn tra cứu nhanh khi viết phần Register-level cho project `register-course`.

## Nguồn sự thật

Firmware tham chiếu bắt buộc: [BUITANHUNG0411/stm32-motor-controller](https://github.com/BUITANHUNG0411/stm32-motor-controller)

Không tự đổi pin, tần số, kiểu dữ liệu hoặc trình tự khởi tạo nếu chưa xác minh từ repository và datasheet STM32F103C8T6.

## Phần cứng

| Thành phần | Giá trị |
|---|---|
| MCU | STM32F103C8T6 |
| Motor | GA37 DC motor |
| Driver | L298N |
| Input | Potentiometer → ADC1, 12-bit (`0..4095`) |
| PWM | TIM1 Channel 1, ARR/chu kỳ logic `99`, mục tiêu 10 kHz |
| IN1 | GPIOB PB12 |
| IN2 | GPIOB PB13 |
| Điều khiển | Open-loop; không có encoder hoặc feedback |

## Luồng firmware

```text
CubeMX/HAL init
  -> start PWM
  -> đặt hướng mặc định FORWARD
  -> while (1)
       -> đọc ADC
       -> map ADC 0..4095 sang pulse 0..99
       -> ghi pulse vào PWM
```

## Mapping HAL → Register

| HAL trong repo | Register-level tương đương | Ý nghĩa |
|---|---|---|
| `HAL_ADC_Start` | `ADC1->CR2` | Bắt đầu conversion (`SWSTART`, tùy cấu hình trigger) |
| `HAL_ADC_PollForConversion` | `ADC1->SR` | Chờ cờ `EOC` |
| `HAL_ADC_GetValue` | `ADC1->DR` | Đọc giá trị conversion |
| `HAL_TIM_PWM_Start` | `TIM1->CCER`, `TIM1->BDTR`, `TIM1->CR1` | Bật output channel, main output và counter khi cần |
| `__HAL_TIM_SET_COMPARE` | `TIM1->CCR1` | Đặt duty PWM |
| `HAL_GPIO_WritePin` | `GPIOB->BSRR` hoặc `GPIOB->BRR` | Set/reset PB12 và PB13 |

## Logic motor

| Direction | PB12 / IN1 | PB13 / IN2 |
|---|---:|---:|
| `FORWARD` | 1 | 0 |
| `REVERSE` | 0 | 1 |
| `BRAKE` | 1 | 1 |
| `STOP` | 0 | 0 |

## Invariants phải giữ

- Giữ nguyên mọi hàm init CubeMX.
- `pulse = adc_raw * 99 / 4095`.
- Clamp pulse trong `0..99` trước khi ghi `TIM1->CCR1`.
- Kiểm tra timeout khi chờ ADC `EOC`.
- Không gọi trạng thái motor ngoài bảng trên.
- Không tuyên bố đã test phần cứng nếu chưa có log hoặc xác nhận thực tế.

## Cách xác minh

1. Đọc `Core/Src/main.c`, `Core/App/app_motor_control.c` và ba driver trong repository tham chiếu.
2. Đối chiếu pin/config với `Core/Inc/main.h` và `stm32-motor-controller.ioc`.
3. Đối chiếu bit register với Reference Manual STM32F1 trước khi đưa code vào tutorial.
4. Ghi rõ phần nào là fact từ repo, phần nào là suy luận register-level.
