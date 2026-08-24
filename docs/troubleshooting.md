# Troubleshooting

## Trang web không mở

Mở trực tiếp `index.html` trong trình duyệt. Nếu trình duyệt chặn một tính năng do `file://`, chạy một static server đơn giản trong thư mục project.

## Code sample không khớp firmware tham chiếu

Dừng việc viết tiếp. Kiểm tra lại repository:

- `Core/Src/main.c`
- `Core/App/app_motor_control.c`
- `Core/Drivers/adc_reader/adc_reader.c`
- `Core/Drivers/motor_pwm/motor_pwm.c`
- `Core/Drivers/motor_driver/motor_driver.c`

Mọi khác biệt phải được đánh dấu là chủ ý hoặc sửa lại cho khớp.

## ADC luôn bằng 0 hoặc không kết thúc

Kiểm tra clock/config đã được CubeMX init, channel ADC, cờ `EOC` và timeout. Không chờ vô hạn.

## Motor không quay

Kiểm tra nguồn motor riêng, common ground giữa STM32 và L298N, dây PWM, và trạng thái IN1/IN2. Tháo nguồn motor trước khi flash.

## Motor quay sai chiều

Đối chiếu PB12/PB13 với bảng direction trong `docs/reference-register.md`. Không tự đảo pin trong tutorial.

## PWM không có output

Kiểm tra đúng TIM1 Channel 1, `CCR1`, enable channel, main output (`BDTR/MOE` nếu cần) và counter (`CR1/CEN`).

## Duty vượt giới hạn

Giá trị phải được clamp trong `0..99`; mapping chuẩn là `adc_raw * 99 / 4095`.

## Claude làm sai quy tắc

1. Chạy `/context` để kiểm tra instruction đã được load.
2. Nhắc Claude đọc `CLAUDE.md` và rule liên quan.
3. Nêu lỗi cụ thể và yêu cầu cập nhật rule nếu lỗi sẽ lặp lại.
4. Dùng `/memory` để kiểm tra auto memory, nhưng không dùng memory thay cho rule dự án.
