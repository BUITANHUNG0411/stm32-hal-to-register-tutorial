# 02 - PWM: Xung Bật Tắt Nhanh

## 1. PWM là gì?

PWM là viết tắt của **Pulse Width Modulation**, tức điều chế độ rộng xung.

Nó không tạo ra điện áp analog thật sự. Nó bật/tắt rất nhanh:

```text
MỨC CAO, MỨC THẤP, MỨC CAO, MỨC THẤP...
```

Nếu bật/tắt đủ nhanh, LED sẽ thấy độ sáng trung bình, còn motor sẽ nhận năng lượng trung bình.

## 2. Chu kỳ PWM

Với Timer 1 hiện tại:

```text
PSC = 71
ARR = 99
1 tick = 1 us
1 chu kỳ PWM = 100 tick = 100 us
f_PWM = 10 kHz
```

Một chu kỳ PWM là một lần timer đếm từ `0` đến `99`.

```text
0us                                                100us
|----------------------------------------------------|
0    1    2    3    ...                         98   99
```

## 3. CCR1/Pulse là gì?

`CCR1` là Capture/Compare Register kênh 1. Trong CubeMX/HAL, bạn hay thấy nó dưới tên **Pulse**.

Với PWM mode thông dụng, có thể hiểu đơn giản:

```text
Counter < CCR1   => chân PWM ở MỨC CAO
Counter >= CCR1  => chân PWM ở MỨC THẤP
```

Trong project:

```text
PA8 = TIM1_CH1
CCR1 = Pulse của TIM1 Channel 1
```

Vậy `CCR1` quyết định PA8 ở MỨC CAO bao lâu trong mỗi chu kỳ `100 us`.

## 4. Sơ đồ xung theo CCR1

### CCR1 = 0, duty 0%

```text
PA8
MỨC CAO
MỨC THẤP  ────────────────────────────────────────────
          0us                                      100us

Thời gian MỨC CAO  = 0 us
Thời gian MỨC THẤP = 100 us
```

### CCR1 = 25, duty gần 25%

```text
PA8
MỨC CAO   ┌────────────┐
          │            │
MỨC THẤP  ┘            └──────────────────────────────
          0us         25us                         100us

Thời gian MỨC CAO  = 25 us
Thời gian MỨC THẤP = 75 us
```

### CCR1 = 50, duty gần 50%

```text
PA8
MỨC CAO   ┌────────────────────────┐
          │                        │
MỨC THẤP  ┘                        └──────────────────
          0us                     50us              100us

Thời gian MỨC CAO  = 50 us
Thời gian MỨC THẤP = 50 us
```

### CCR1 = 75, duty gần 75%

```text
PA8
MỨC CAO   ┌────────────────────────────────────┐
          │                                    │
MỨC THẤP  ┘                                    └──────
          0us                                75us   100us

Thời gian MỨC CAO  = 75 us
Thời gian MỨC THẤP = 25 us
```

### CCR1 = 99, duty gần 99%

```text
PA8
MỨC CAO   ┌──────────────────────────────────────────┐
          │                                          │
MỨC THẤP  ┘                                          └
          0us                                      99us 100us

Thời gian MỨC CAO  gần 99 us
Thời gian MỨC THẤP gần 1 us
```

## 5. Duty cycle là gì?

Duty cycle là tỉ lệ thời gian MỨC CAO trong một chu kỳ PWM.

```text
Duty = thời gian MỨC CAO / toàn bộ chu kỳ
```

Với `ARR = 99`, một chu kỳ có `100 tick`, nên có thể ước tính:

```text
Duty gần đúng = CCR1 / 100
```

Ví dụ:

| CCR1 | Thời gian MỨC CAO | Thời gian MỨC THẤP | Duty gần đúng |
| --- | ---: | ---: | ---: |
| 0 | 0 us | 100 us | 0% |
| 25 | 25 us | 75 us | 25% |
| 50 | 50 us | 50 us | 50% |
| 75 | 75 us | 25 us | 75% |
| 99 | 99 us | 1 us | 99% |

## 6. ARR và CCR1 khác nhau thế nào?

| Thành phần | Quyết định cái gì? |
| --- | --- |
| `ARR` | Độ dài cả chu kỳ PWM |
| `CCR1` | MỨC CAO kéo dài bao lâu trong chu kỳ đó |

Nếu giữ `ARR = 99`, tần số PWM giữ ở `10 kHz`.

Khi thay đổi `CCR1`, ta không đổi tần số PWM. Ta chỉ đổi **duty cycle**.

## 7. Vì sao CubeMX đặt Pulse ban đầu bằng 0?

Trong CubeMX, `Pulse` là giá trị ban đầu của `CCR1` cho kênh PWM.

Với motor, nên đặt:

```text
Pulse = 0
```

Lý do: khi firmware vừa start PWM, motor chưa nên quay ngay. `Pulse = 0` tạo duty gần `0%`, tức trạng thái khởi động an toàn.

Sau đó trong code:

```text
Đọc ADC chiết áp -> map sang Pulse mới -> ghi vào CCR1
```

Vậy `Pulse = 0` chỉ là giá trị ban đầu, không phải giá trị cố định mãi mãi.

## 8. Tóm tắt

```text
ARR  = khung thời gian của cả chu kỳ
CCR1 = phần MỨC CAO nằm trong khung đó
PWM  = bật/tắt nhanh theo chu kỳ
Duty = thời gian MỨC CAO / toàn bộ chu kỳ
```
