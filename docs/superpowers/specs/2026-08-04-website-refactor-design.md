# Website Refactor — STM32 Motor Workbook

## Tóm tắt

Website ghi chú hiện tại (`stm32-motor-notes/index.html`) đang chứa hai track học
khác nhau trong cùng một trang (Motor Control và CyberDash/Joystick), gây khó tìm
kiếm khi dùng cho mục đích tra cứu nhanh.

Mục tiêu: Tách thành 3 file, tối ưu trải nghiệm tra cứu nhanh.

## Quyết định thiết kế

- Mục đích chính của người dùng: **tra cứu nhanh** (không phải đọc tuần tự)
- Cấu trúc: 3 trang, 1 CSS dùng chung
- Không thay đổi phong cách CSS hiện tại (light theme, sidebar layout)

## Cấu trúc file sau refactor

```
stm32-motor-notes/
├── index.html       ← Trang chủ (MỚI)
├── motor.html       ← Motor Control track (đổi tên từ index.html cũ)
├── cyberdash.html   ← CyberDash track (MỚI)
└── styles.css       ← Giữ nguyên + bổ sung class mới cho landing
```

## Thiết kế từng trang

### index.html — Trang chủ

**Sidebar:** Brand + 2 link: "Motor Control" và "CyberDash"

**Main content:**
1. `<header>` — Title "STM32 Motor Workbook" + kicker + lede
2. Hardware dashboard — 4 metric box: 72 MHz / 10 kHz / STM32F103C8T6 / GA37
3. Project status strip — 3 badge: ĐÃ CÓ Timer/PWM/ADC | ĐÃ CÓ HAL modules | HỌC SAU Encoder
4. 2 track card lớn — Motor Control (blue) | CyberDash (purple), click vào chuyển trang

### motor.html — Motor Control track

Lấy từ `index.html` hiện tại, thay đổi:
- Xóa section `#joystick-events` (số 07)
- Xóa section `#uart-basics` (số 08)
- Chuyển nội dung section `#project-views` (06A) vào trong reference section (R1)
  thay vì đứng riêng giữa luồng học
- Xóa mục `06A Tree & flows` khỏi sidebar
- Sidebar chỉ còn: Dashboard, Timer, PWM, ADC, L298N, HAL modules | Reference: Pin & công thức, Build & flash, Học sau, MD archive
- Thêm link "← Trang chủ" ở đầu sidebar

### cyberdash.html — CyberDash track

Trang mới, cùng layout sidebar:
- Sidebar: Brand + link "← Trang chủ" + 2 section: "01 Joystick events" | "02 UART basics"
- Section 01 — Joystick events: tách thành 5 sub-section có h3 riêng:
  1. Vì sao không gửi raw ADC?
  2. Dead zone & ngưỡng
  3. Gạt chéo & nút SW / Debounce
  4. LED test trước UART
  5. Hiệu chuẩn hai trục
- Section 02 — UART basics: giữ nguyên nội dung
- Thêm section Reference nhỏ: pin UART (TX/RX), baud 115200 8N1

## CSS bổ sung (vào styles.css)

- `.track-cards` — grid 2 cột cho 2 card track
- `.track-card` — card lớn, hover effect, con trỏ pointer
- `.track-card.motor` — accent blue
- `.track-card.cyberdash` — accent purple

## Nội dung không thay đổi

- `notes/01-timer.md`, `notes/02-pwm.md`, `notes/03-led-control.md`, `notes/04-dc-motor-control.md`
- `styles.css` — chỉ bổ sung, không xóa class cũ
- `AGENTS.md` — cập nhật phần Project Shape để phản ánh cấu trúc 3 file mới
