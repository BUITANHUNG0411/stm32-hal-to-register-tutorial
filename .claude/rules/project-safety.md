# Project Safety Rules

- Repository firmware tham chiếu duy nhất là `BUITANHUNG0411/stm32-motor-controller`.
- Không sửa hoặc đề xuất sửa các hàm init CubeMX: `HAL_Init`, `SystemClock_Config`, `MX_*_Init` và MSP init.
- Chỉ chuyển đổi logic sau init: `while(1)`, `app_motor_control`, `adc_reader`, `motor_pwm`, `motor_driver`.
- Không tự suy diễn pin. Pin motor là PB12/PB13; PWM là TIM1 Channel 1; ADC là ADC1.
- Không gọi là "đã test trên board" nếu chưa có bằng chứng thực tế.
- Trước khi sửa, đọc `PRD.md` và `docs/reference-register.md`.
- Khi phát hiện mâu thuẫn giữa tài liệu và source repo, dừng và báo mâu thuẫn trước khi viết code.
