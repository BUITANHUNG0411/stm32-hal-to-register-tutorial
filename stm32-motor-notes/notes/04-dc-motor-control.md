# 04 - Điều Khiển Động Cơ DC Bằng PWM Và L298N

## 1. Vai trò của L298N

STM32 không cấp dòng trực tiếp cho motor DC. Chân STM32 chỉ là tín hiệu điều khiển.

Trong project:

```text
PA8 PWM      -> L298N ENA
PB12         -> L298N IN1
PB13         -> L298N IN2
Motor        -> L298N OUT1/OUT2
Nguồn motor  -> 12 V ngoài
```

L298N nhận tín hiệu từ STM32, rồi đóng/ngắt nguồn motor theo tín hiệu đó.

## 2. PWM trên ENA làm gì?

Chân `ENA` của L298N cho phép hoặc chặn động cơ.

Nếu `PA8` tạo PWM đưa vào `ENA`, thì L298N sẽ cấp năng lượng cho motor theo từng xung:

```text
PA8 MỨC CAO  -> ENA bật -> motor được cấp năng lượng
PA8 MỨC THẤP -> ENA tắt -> motor tạm thời không được cấp năng lượng
```

Vì PWM đang ở `10 kHz`, mỗi chu kỳ chỉ dài `100 us`. Motor có quán tính cơ học, nên không dừng/quay lại theo từng xung nhỏ. Nó cảm nhận gần như công suất trung bình.

## 3. Duty thấp: motor yếu

Ví dụ `CCR1 = 25`:

```text
ENA
MỨC CAO   ┌────────────┐
          │            │
MỨC THẤP  ┘            └──────────────────────────────
          0us         25us                         100us

Motor nhận năng lượng gần 25% thời gian.
```

Kết quả thực tế: motor có xu hướng quay chậm/yếu hơn, nhưng còn phụ thuộc tải, ma sát, nguồn, và đặc tính motor.

## 4. Duty 50%: motor trung bình

Ví dụ `CCR1 = 50`:

```text
ENA
MỨC CAO   ┌────────────────────────┐
          │                        │
MỨC THẤP  ┘                        └──────────────────
          0us                     50us              100us

Motor nhận năng lượng gần 50% thời gian.
```

## 5. Duty cao: motor mạnh

Ví dụ `CCR1 = 75`:

```text
ENA
MỨC CAO   ┌────────────────────────────────────┐
          │                                    │
MỨC THẤP  ┘                                    └──────
          0us                                75us   100us

Motor nhận năng lượng gần 75% thời gian.
```

## 6. IN1 và IN2 quyết định hướng

PWM trên `ENA` quyết định motor được cấp năng lượng bao nhiêu. Còn `IN1/IN2` quyết định hướng dòng qua motor.

| IN1 | IN2 | Hành vi gần đúng |
| --- | --- | --- |
| 1 | 0 | Quay một chiều |
| 0 | 1 | Quay chiều ngược lại |
| 0 | 0 | Thả trôi/dừng, tùy module và cách nối |
| 1 | 1 | Phanh, nhưng còn tùy module/driver |

Khi mới học, hãy tạm nhớ:

```text
IN1/IN2 = chọn hướng
ENA PWM = chọn độ mạnh
```

## 7. Lưu ý an toàn

Không cấp nguồn motor từ Blue Pill.

Nên dùng:

```text
Nguồn 12 V ngoài -> L298N motor supply
STM32 GND -------+
L298N GND -------+  chung mass
```

STM32 và L298N phải chung GND để tín hiệu `PA8`, `PB12`, `PB13` có cùng mốc điện áp.

## 8. Tóm tắt

| Giá trị | Tác dụng với motor |
| --- | --- |
| `ARR` | Định chu kỳ/tần số PWM |
| `CCR1/Pulse` | Định duty, tức thời gian ENA được bật |
| Duty thấp | Motor yếu/chậm hơn |
| Duty cao | Motor mạnh/nhanh hơn |
| IN1/IN2 | Chọn hướng quay hoặc chế độ dừng |

## 9. Đủ để làm bản open-loop cơ bản chưa?

Có. Nếu tạm bỏ encoder, OLED, HC-SR04, và closed-loop control, kiến thức hiện tại đã đủ để làm bản:

```text
Chiết áp -> ADC PA0
ADC value -> code map sang Pulse/CCR1
TIM1 PWM PA8 -> L298N ENA
GPIO PB12/PB13 -> L298N IN1/IN2
L298N -> motor GA37
```

Cần học thêm ngay trước khi code HAL:

| Mảng | Dùng để làm gì |
| --- | --- |
| `GPIO Output` | Set `PB12/PB13` để chọn hướng hoặc dừng motor |
| HAL flow | Start PWM, đọc ADC, map ADC sang Pulse, ghi Pulse vào `CCR1`, set GPIO hướng |

Tạm nhớ:

```text
ADC đọc ga
PWM điều khiển độ mạnh
GPIO điều khiển hướng
L298N cấp dòng cho motor
```

## 10. Quy trình HAL nhìn từ xa

Biết C là đủ để hiểu logic, nhưng STM32 HAL dùng các hàm thư viện dạng `HAL_...` để thao tác với peripheral. Trước khi dùng PWM hoặc ADC, peripheral phải được cấu hình và init.

Luồng tổng quát:

```text
CubeMX config -> MX init -> HAL start -> read ADC -> map value -> write CCR1
```

| Bước | Ý nghĩa |
| --- | --- |
| CubeMX config | Chọn chân và mode: `PA0 = ADC1_IN0`, `PA8 = TIM1_CH1`, `PB12/PB13 = GPIO Output` |
| `MX_..._Init` | Hàm init do CubeMX sinh ra để cấu hình ADC, timer PWM, GPIO |
| `HAL_..._Start` | Bật peripheral bắt đầu chạy |
| Vòng lặp chính | Đọc ADC, map sang Pulse, ghi vào `CCR1`, set hướng motor |

Tư duy quan trọng:

```text
Init = chuẩn bị phần cứng
Start = cho peripheral chạy
Loop = đọc input và cập nhật output
```

## 11. CubeMX pinout đã confirm

Pinout hiện tại trong CubeMX đã đúng cho bản open-loop:

| Chức năng | Cấu hình CubeMX |
| --- | --- |
| Chiết áp | `PA0 = ADC1_IN0` |
| PWM motor | `PA8 = TIM1_CH1` |
| L298N IN1 | `PB12 = GPIO_Output` |
| L298N IN2 | `PB13 = GPIO_Output` |
| Encoder C1 | `PA6 = TIM3_CH1`, để học sau |
| Encoder C2 | `PA7 = TIM3_CH2`, để học sau |

Bước tiếp theo không phải thêm chân mới, mà là kiểm tra cấu hình parameter:

```text
Clock = 72 MHz
TIM1 PSC = 71
TIM1 ARR = 99
TIM1 Pulse = 0
ADC1 channel = IN0
PB12/PB13 output mode = push-pull
```

## 12. Nếu CubeMX đang hiện SYSCLK = 8 MHz

Khi `SYSCLK = 8 MHz`, thường là CubeMX đang dùng `HSI` nội. Với Blue Pill muốn đạt `72 MHz`, cần bật clock ngoài `HSE` trước:

```text
System Core > RCC
HSE = Crystal/Ceramic Resonator
```

Sau đó trong Clock Configuration:

```text
HSE input = 8 MHz
PLL source = HSE
PLLMul = x9
System Clock Mux = PLLCLK
SYSCLK = 72 MHz
```

Prescaler nên dùng:

```text
AHB  = /1
APB1 = /2
APB2 = /1
ADC  = /6
```

Lý do `APB1 = /2`: bus APB1 của STM32F103 chỉ tối đa `36 MHz`.

## 13. Clock configuration đã confirm đúng

Ảnh Clock Configuration hiện tại đã đúng cho project:

```text
SYSCLK = 72 MHz
AHB    = /1  -> HCLK = 72 MHz
APB1   = /2  -> PCLK1 = 36 MHz
APB2   = /1  -> PCLK2 = 72 MHz
ADC    = /6  -> ADC clock = 12 MHz
```

Chưa cần đào sâu bus clock ngay. Tạm nhớ: đây là bộ chia an toàn/phù hợp cho STM32F103 chạy `72 MHz`; đặc biệt APB1 không được vượt `36 MHz`.
