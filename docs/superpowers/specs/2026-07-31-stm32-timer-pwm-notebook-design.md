# Thiết Kế Sổ Tay STM32 Timer PWM

Ngày: 2026-07-31

## Mục Tiêu

Tạo một sổ tay học tập nhỏ cho sinh viên ngành Kỹ thuật Ô tô đang học các khái niệm cơ bản về STM32 timer và PWM. Sổ tay phải giải thích đủ rõ để hiểu việc điều chỉnh độ sáng LED và điều khiển tốc độ động cơ DC trong ngữ cảnh dự án STM32F103C8T6 hiện tại.

Phiên bản đầu tiên chỉ tập trung vào Timer, PWM, ARR, CCR/Pulse, duty cycle, hành vi của LED, và hành vi của động cơ DC qua L298N. Encoder, ánh xạ ADC throttle, điều khiển tốc độ vòng kín, OLED, và HC-SR04 nằm ngoài phạm vi của phiên bản đầu tiên này.

## Định Dạng

Dùng định dạng kết hợp:

- Các file Markdown là nguồn ghi chú chính, dễ chỉnh sửa và mở rộng sau mỗi buổi học.
- Một trang web tĩnh cơ bản là bản sổ tay trực quan, tối ưu cho phần tóm tắt nhiều màu và sơ đồ xung rõ ràng.

Cấu trúc đề xuất:

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

## Nội Dung

Phiên bản đầu tiên của sổ tay bao gồm:

1. Nền tảng Timer
   - System clock ở 72 MHz.
   - Prescaler `PSC = 71`.
   - Tần số counter tick `f_CNT = 1 MHz`.
   - Một tick bằng `1 us`.
   - `ARR = 99` nghĩa là counter đếm `0..99`, nên một chu kỳ có 100 tick.

2. Nền tảng PWM
   - PWM là bật/tắt nhanh giữa MỨC CAO và MỨC THẤP.
   - Chu kỳ PWM và tần số PWM.
   - Với `ARR = 99`, một chu kỳ PWM là `100 us`.
   - Tần số PWM là `10 kHz`.
   - `CCR1` / `Pulse` điều khiển PA8 ở MỨC CAO trong bao lâu ở mỗi chu kỳ PWM.

3. Sơ đồ xung
   - Sơ đồ ngang rõ ràng cho duty 0%, 25%, 50%, 75%, và gần 100%.
   - Mỗi sơ đồ thể hiện thời gian từ `0 us` đến `100 us`.
   - Mỗi sơ đồ liên hệ giá trị `CCR1` với thời gian MỨC CAO và thời gian MỨC THẤP.

4. Điều khiển LED
   - Duty thấp làm LED mờ.
   - Duty cao làm LED sáng.
   - Mắt người cảm nhận độ sáng trung bình vì PWM đủ nhanh.

5. Điều khiển động cơ DC
   - PA8 PWM nối tới L298N ENA.
   - Duty PWM điều khiển công suất trung bình gần đúng của motor.
   - IN1/IN2 chọn hướng hoặc trạng thái dừng/phanh.
   - Nguồn motor phải đến từ nguồn ngoài 12 V.
   - STM32 và L298N phải chung GND.

6. Các nhầm lẫn thường gặp
- `ARR = 99` không có nghĩa là 10,000 lần đếm trong một chu kỳ PWM.
   - 10,000 là số chu kỳ PWM mỗi giây ở tần số 10 kHz.
   - `f_CNT` là tốc độ tick của counter.
   - `f_PWM` là tốc độ lặp lại của cả chu kỳ PWM.
   - `CCR1` thay đổi duty cycle, không thay đổi tần số PWM.

## Thiết Kế Web

Trang web nên có cảm giác như một sổ tay kỹ thuật nhiều màu, không phải landing page. Trang phải mở thẳng vào nội dung học tập.

Đặc điểm thiết kế:

- Văn bản tiếng Việt có dấu.
- Nền sáng với các phần chủ đề có màu rõ ràng.
- Công thức và bảng gọn, dễ quét mắt.
- Panel sơ đồ xung giống oscilloscope đơn giản.
- Không cần framework nặng.
- Không cần JavaScript cho phiên bản 1, trừ khi một tương tác nhỏ thật sự giúp dễ hiểu hơn.

## Tiêu Chí Thành Công

Sổ tay được xem là đạt khi người học có thể trả lời:

- Timer counter làm gì?
- Vì sao `PSC = 71` tạo ra counter tick `1 MHz` từ clock `72 MHz`?
- Vì sao `ARR = 99` tạo ra 100 tick trong một chu kỳ PWM?
- Vì sao chu kỳ PWM là `100 us` và tần số là `10 kHz`?
- `CCR1` điều khiển cái gì?
- Duty cycle ảnh hưởng độ sáng LED như thế nào?
- Duty PWM ảnh hưởng công suất gần đúng của động cơ DC qua L298N như thế nào?

## Xác Minh

Xác minh các file tồn tại và có thể mở cục bộ. Với trang tĩnh, kiểm tra HTML/CSS đủ để xác nhận các tham chiếu đúng và các sơ đồ xung có mặt.
