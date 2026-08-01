# Sổ Tay STM32 Timer PWM Điều Khiển Động Cơ

Đây là quyển ghi chú nhỏ để học Timer và PWM trên STM32F103C8T6 Blue Pill. Mục tiêu của bản này là nắm đủ chắc để hiểu:

- PWM làm LED mờ/sáng như thế nào.
- PWM điều khiển công suất gần đúng của động cơ DC qua L298N như thế nào.
- `PSC`, `ARR`, `CCR1/Pulse`, `f_CNT`, `f_PWM` khác nhau ra sao.

Bản web trực quan nằm ở: [index.html](index.html)

## Trạng Thái Học Hiện Tại

Kiến thức hiện tại đã đủ để làm bản open-loop cơ bản:

```text
Chiết áp -> ADC PA0
ADC value -> code map sang Pulse/CCR1
TIM1 PWM PA8 -> L298N ENA
GPIO PB12/PB13 -> L298N IN1/IN2
L298N -> motor GA37
```

Tạm bỏ qua encoder, OLED, HC-SR04, và closed-loop control. Trước khi code HAL, phần còn thiếu nhẹ là `GPIO Output` cho `IN1/IN2` và flow tổng thể: start PWM, đọc ADC, map ADC sang Pulse, ghi Pulse vào `CCR1`, rồi set GPIO hướng.

## Quy Trình HAL Cần Nắm Trước Khi Code

Biết C là đủ để hiểu logic, nhưng STM32 HAL dùng các hàm thư viện như `HAL_...` để điều khiển peripheral. Trước khi dùng ADC/PWM/GPIO, peripheral phải được cấu hình và init.

Luồng nhìn từ xa:

```text
CubeMX config -> MX init -> HAL start -> read ADC -> map value -> write CCR1
```

Vai trò từng phần:

| Phần | Vai trò |
| --- | --- |
| CubeMX config | Chọn chân, mode, timer, ADC channel, clock |
| `MX_..._Init` | Hàm init do CubeMX sinh ra để cấu hình peripheral |
| `HAL_..._Start` | Bật peripheral bắt đầu chạy, ví dụ PWM hoặc ADC |
| Vòng lặp chính | Đọc ADC, tính Pulse, ghi Pulse vào `CCR1`, set GPIO hướng |

## Ngữ Cảnh Phần Cứng

| Thành phần | Cấu hình hiện tại |
| --- | --- |
| MCU | STM32F103C8T6 Blue Pill |
| Clock hệ thống | 72 MHz |
| PWM động cơ | PA8 = TIM1_CH1 |
| Mạch driver | L298N, chân ENA nhận PWM từ PA8 |
| Hướng motor | PB12 = IN1, PB13 = IN2 |
| Nguồn motor | Nguồn ngoài 12 V / 10 A |
| Lưu ý | STM32 và L298N phải chung GND |

## CubeMX Pinout Đã Confirm

Pinout hiện tại đã đúng cho bản open-loop:

| Chức năng | Cấu hình CubeMX |
| --- | --- |
| Chiết áp | `PA0 = ADC1_IN0` |
| PWM motor | `PA8 = TIM1_CH1` |
| L298N IN1 | `PB12 = GPIO_Output` |
| L298N IN2 | `PB13 = GPIO_Output` |
| Encoder C1 | `PA6 = TIM3_CH1`, để học sau |
| Encoder C2 | `PA7 = TIM3_CH2`, để học sau |

## CubeMX Clock: Từ 8 MHz Lên 72 MHz

Nếu Clock Configuration đang hiện `SYSCLK = 8 MHz`, CubeMX đang dùng `HSI` nội `8 MHz`.
Với Blue Pill muốn chạy project ở `72 MHz`, cấu hình thường dùng là:

```text
RCC HSE = Crystal/Ceramic Resonator
HSE input = 8 MHz
PLL source = HSE
PLLMul = x9
System Clock Mux = PLLCLK
SYSCLK = 72 MHz
```

Prescaler nên giữ:

```text
AHB  = /1  -> HCLK = 72 MHz
APB1 = /2  -> PCLK1 = 36 MHz
APB2 = /1  -> PCLK2 = 72 MHz
ADC  = /6  -> ADC clock = 12 MHz
```

Lưu ý: nếu chưa bật `HSE` trong `System Core > RCC`, CubeMX sẽ không cho cấu hình `72 MHz` theo cách này.

Clock Configuration hiện tại đã confirm đúng:

```text
SYSCLK = 72 MHz
AHB    = /1  -> HCLK = 72 MHz
APB1   = /2  -> PCLK1 = 36 MHz
APB2   = /1  -> PCLK2 = 72 MHz
ADC    = /6  -> ADC clock = 12 MHz
```

Chưa cần đào sâu các bus clock ở giai đoạn này. Tạm nhớ: đây là bộ chia an toàn/phù hợp cho STM32F103 chạy `72 MHz`; đặc biệt APB1 không được vượt `36 MHz`.

## Thứ Tự Học Dễ Hiểu Nhất

1. [Timer: bộ đếm thời gian](notes/01-timer.md)
2. [PWM: xung bật/tắt nhanh](notes/02-pwm.md)
3. [Điều khiển LED bằng PWM](notes/03-led-control.md)
4. [Điều khiển động cơ DC bằng PWM và L298N](notes/04-dc-motor-control.md)

## Công Thức Chính Trong Project

```text
f_CNT = f_TIM / (PSC + 1)
T_tick = 1 / f_CNT
T_PWM = (ARR + 1) x T_tick
f_PWM = 1 / T_PWM
Duty gần đúng = CCR1 / (ARR + 1)
```

Với cấu hình hiện tại:

```text
f_TIM = 72 MHz
PSC = 71
ARR = 99

f_CNT = 72 MHz / 72 = 1 MHz
T_tick = 1 us
T_PWM = 100 x 1 us = 100 us
f_PWM = 10 kHz
```

Trong CubeMX, đặt `Pulse = 0` cho TIM1 Channel 1 là giá trị khởi động an toàn. `Pulse` chính là giá trị ban đầu của `CCR1`; đặt `0` để motor chưa quay ngay khi PWM vừa start. Sau đó code đọc ADC chiết áp và cập nhật `CCR1` theo giá trị ga thật.

## Điều Cần Nhớ

`ARR = 99` không có nghĩa là 10,000 count trong một chu kỳ PWM. Timer đếm từ `0` đến `99`, tổng cộng `100` tick. Còn `10,000` là số chu kỳ PWM lặp lại trong 1 giây, tức `10 kHz`.
