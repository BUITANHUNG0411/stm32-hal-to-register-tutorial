# Kế Hoạch Triển Khai Sổ Tay STM32 Timer PWM

> **Dành cho agentic workers:** KỸ NĂNG PHỤ BẮT BUỘC: dùng superpowers:subagent-driven-development (khuyến nghị) hoặc superpowers:executing-plans để triển khai kế hoạch này theo từng task. Các bước dùng cú pháp checkbox (`- [ ]`) để theo dõi.

**Mục tiêu:** Xây dựng một sổ tay kết hợp Markdown và web tĩnh để giải thích các khái niệm STM32 Timer/PWM đủ rõ cho việc điều chỉnh độ sáng LED và điều khiển động cơ DC.

**Kiến trúc:** Các file Markdown giữ vai trò ghi chú nguồn. Một file `index.html` độc lập cùng `styles.css` trình bày lại cùng ý tưởng dưới dạng trực quan, có section màu, bảng, và sơ đồ xung kiểu oscilloscope. Sổ tay là dạng tĩnh và có thể mở trực tiếp trong trình duyệt.

**Tech stack:** HTML, CSS, Markdown. Không dùng JavaScript và không có dependency bên ngoài.

## Ràng Buộc Toàn Cục

- Văn bản tiếng Việt có dấu.
- Ngữ cảnh project hiện tại: STM32F103C8T6 Blue Pill, system clock 72 MHz.
- Ví dụ timer: TIM1 PWM trên PA8, `PSC = 71`, `ARR = 99`, `f_CNT = 1 MHz`, `T_tick = 1 us`, `T_PWM = 100 us`, `f_PWM = 10 kHz`.
- Tập trung vào Timer, PWM, ARR, CCR/Pulse, duty cycle, hành vi LED, và hành vi động cơ DC qua L298N.
- Không đề cập ánh xạ ADC throttle, encoder mode, closed-loop control, OLED, hoặc HC-SR04 trong phiên bản 1.
- Không dùng framework nặng.
- Không cần JavaScript cho phiên bản 1.

---

### Công Việc 1: Nguồn Ghi Chú Markdown

**Tệp:**
- Tạo: `stm32-motor-notes/README.md`
- Tạo: `stm32-motor-notes/notes/01-timer.md`
- Tạo: `stm32-motor-notes/notes/02-pwm.md`
- Tạo: `stm32-motor-notes/notes/03-led-control.md`
- Tạo: `stm32-motor-notes/notes/04-dc-motor-control.md`

**Interfaces:**
- Đầu vào: design spec đã duyệt tại `docs/superpowers/specs/2026-07-31-stm32-timer-pwm-notebook-design.md`.
- Đầu ra: các file ghi chú Markdown được liên kết bởi `README.md`; trang web trong Công Việc 2 phản chiếu các ý tưởng này.

- [ ] **Bước 1: Tạo README của sổ tay**

Tạo `stm32-motor-notes/README.md` với tiêu đề, mục đích, thứ tự học, ngữ cảnh phần cứng, và liên kết tới bốn file note.

- [ ] **Bước 2: Tạo note Timer**

Tạo `stm32-motor-notes/notes/01-timer.md` giải thích timer, system clock, PSC, `f_CNT`, tick time, ARR, cách đếm bao gồm cả 0, và ví dụ chính xác `72 MHz -> 1 MHz -> 100 us`.

- [ ] **Bước 3: Tạo note PWM**

Tạo `stm32-motor-notes/notes/02-pwm.md` giải thích PWM, chu kỳ, tần số, duty cycle, CCR1/Pulse, và sơ đồ xung dạng text cho 0%, 25%, 50%, 75%, và gần 100%.

- [ ] **Bước 4: Tạo note LED**

Tạo `stm32-motor-notes/notes/03-led-control.md` giải thích việc điều chỉnh độ sáng LED bằng độ sáng trung bình và vì sao tần số PWM ảnh hưởng hiện tượng nhấp nháy nhìn thấy được.

- [ ] **Bước 5: Tạo note động cơ DC**

Tạo `stm32-motor-notes/notes/04-dc-motor-control.md` giải thích PA8 nối tới L298N ENA, duty là công suất motor trung bình gần đúng, logic hướng IN1/IN2, GND chung, và nguồn motor ngoài.

- [ ] **Bước 6: Xác minh các file nguồn Markdown**

Chạy: `find stm32-motor-notes -maxdepth 3 -type f | sort`

Kỳ vọng: đầu ra liệt kê `README.md` và cả bốn file trong thư mục `notes/`.

---

### Công Việc 2: Sổ Tay Web Tĩnh Trực Quan

**Tệp:**
- Tạo: `stm32-motor-notes/index.html`
- Tạo: `stm32-motor-notes/styles.css`

**Interfaces:**
- Đầu vào: các note Markdown từ Công Việc 1.
- Đầu ra: một sổ tay trực quan dạng web tĩnh, mở được bằng trình duyệt, có panel sơ đồ xung và các section chủ đề rõ ràng.

- [ ] **Bước 1: Tạo cấu trúc HTML**

Tạo `stm32-motor-notes/index.html` với:

- Header đặt tên sổ tay.
- Link điều hướng tới các section Timer, PWM, LED, và motor.
- Formula card cho `f_CNT`, `T_PWM`, và `f_PWM`.
- Panel sơ đồ xung cho `CCR1 = 0`, `25`, `50`, `75`, và `99`.
- Bảng tóm tắt so sánh ARR, CCR1, duty, thời gian MỨC CAO, thời gian MỨC THẤP, và hành vi.
- Ghi chú an toàn về GND chung của L298N và nguồn motor ngoài 12 V.

- [ ] **Bước 2: Tạo CSS styling**

Tạo `stm32-motor-notes/styles.css` với:

- Phong cách trang giống sổ tay nền sáng.
- Các section màu rõ ràng, không bị thống trị bởi một họ màu duy nhất.
- Layout responsive cho desktop và mobile.
- Các hàng waveform có kích thước ổn định và nhãn dễ đọc.
- Nền tảng print-friendly thông qua HTML semantic bình thường.

- [ ] **Bước 3: Xác minh liên kết cục bộ**

Chạy: `rg 'href=' stm32-motor-notes/index.html`

Kỳ vọng: link bao gồm `styles.css`, section anchor, và các note Markdown.

- [ ] **Bước 4: Xác minh nội dung sơ đồ xung**

Chạy: `rg 'CCR1 =|Duty|MỨC CAO|MỨC THẤP|100 us|10 kHz' stm32-motor-notes/index.html stm32-motor-notes/styles.css`

Kỳ vọng: đầu ra hiển thị nhãn waveform, công thức, và text timing.

---

### Công Việc 3: Xác Minh Cuối

**Tệp:**
- Kiểm tra: `stm32-motor-notes/index.html`
- Kiểm tra: `stm32-motor-notes/styles.css`
- Kiểm tra: `stm32-motor-notes/notes/*.md`

**Interfaces:**
- Đầu vào: tất cả file từ Công Việc 1 và Công Việc 2.
- Đầu ra: sổ tay đã xác minh, sẵn sàng mở cục bộ.

- [ ] **Bước 1: Kiểm tra cây file**

Chạy: `find stm32-motor-notes -maxdepth 3 -type f | sort`

Kỳ vọng: Cây file bao gồm `README.md`, `index.html`, `styles.css`, và bốn note Markdown.

- [ ] **Bước 2: Kiểm tra marker chưa hoàn thiện**

Chạy: `rg 'TODO|TBD|lorem|placeholder' stm32-motor-notes docs/superpowers/specs/2026-07-31-stm32-timer-pwm-notebook-design.md`

Kỳ vọng: Không có match.

- [ ] **Bước 3: Kiểm tra tham chiếu trang**

Chạy: `rg 'notes/01-timer.md|notes/02-pwm.md|notes/03-led-control.md|notes/04-dc-motor-control.md|styles.css' stm32-motor-notes/index.html stm32-motor-notes/README.md`

Kỳ vọng: đầu ra xác nhận trang web và README liên kết tới các note và stylesheet.

- [ ] **Bước 4: Hướng dẫn mở**

Sổ tay tĩnh sẵn sàng khi `stm32-motor-notes/index.html` có thể mở trực tiếp trong trình duyệt.
