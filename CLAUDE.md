# CLAUDE.md
# Vibe Coding Structure for Claude Code (August 2026)

## 1. Giới thiệu
Đây là file cấu trúc chính cho dự án `register-course`. Claude Code sẽ tự động đọc file này để biết cách hoạt động.

## 2. Mục đích dự án
Web hướng dẫn STM32 Register-level motor controller. Giữ nguyên phần init CubeMX, chuyển toàn bộ phần còn lại sang thanh ghi trực tiếp.

## 3. Quy tắc an toàn (không thể thay đổi)
- **Giữ nguyên toàn bộ phần init CubeMX** (SystemClock_Config, MX_GPIO_Init, MX_ADC1_Init, MX_TIM1_Init, MSP init).
- Không bao giờ thay đổi hoặc xóa code init.
- Luôn so sánh code Register với code HAL sau init.
- Mọi code sample phải bám sát repo tham chiếu: https://github.com/BUITANHUNG0411/stm32-motor-controller

## 4. Quy tắc code (dành cho người non-tech)
- Comment bằng tiếng Việt, dễ hiểu.
- Mỗi hàm chỉ làm 1 việc.
- Dùng `//` comment thay vì `/* */`.
- Không dùng pointer, dùng int trực tiếp.
- Always check if (adc > 4095) before use.
- PWM duty clamp 0-99.
- Motor direction phải đúng L298N (common ground).

## 5. Workflow (luôn làm theo)
1. Đọc bảng register trước khi edit code.
2. Viết code Register tương đương HAL sau init.
3. Luôn thêm comment so sánh.
4. Nếu Claude Code muốn edit init, trả lời ngay: "Không được, giữ nguyên CubeMX init".

## 6. Auto Memory
Auto memory bật. Mọi thay đổi của mình Claude sẽ ghi nhớ.

## 7. Tools & Commands
- `/context` — xem luật đang active
- `/memory` — xem auto memory
- `/run` — test code
- `/doctor` — kiểm tra cấu hình
- `/compact` — nén lịch sử

## 8. Reference Files
- `docs/reference-register.md` — bảng register
- `PRD.md` — mục tiêu dự án
- `src/register-motor.c` — code Register-level

## 9. Import other files
@docs/reference-register.md
@PRD.md
