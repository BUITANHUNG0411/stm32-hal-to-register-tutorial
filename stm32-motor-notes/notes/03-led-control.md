# 03 - Điều Khiển LED Bằng PWM

## 1. LED nếu bật/tắt chậm

Nếu bạn bật/tắt LED chậm, mắt người sẽ thấy LED nhấp nháy:

```text
Sáng ... tắt ... sáng ... tắt ...
```

Điều này không phải cách điều chỉnh độ sáng tốt, vì người nhìn thấy hiện tượng nhấp nháy.

## 2. LED nếu bật/tắt nhanh bằng PWM

Nếu PWM nhanh, ví dụ `10 kHz`, một chu kỳ chỉ dài:

```text
100 us
```

Mắt người không kịp thấy từng xung bật/tắt. Nó chỉ cảm nhận độ sáng trung bình.

## 3. Duty thấp: LED mờ

Ví dụ `CCR1 = 25`:

```text
MỨC CAO 25 us, MỨC THẤP 75 us
Duty 25%
```

Sơ đồ:

```text
Chân LED
MỨC CAO   ┌────────────┐
          │            │
MỨC THẤP  ┘            └──────────────────────────────
          0us         25us                         100us
```

LED được cấp điện chỉ 25% thời gian, nên nhìn mờ.

## 4. Duty trung bình: LED sáng vừa

Ví dụ `CCR1 = 50`:

```text
MỨC CAO 50 us, MỨC THẤP 50 us
Duty 50%
```

Sơ đồ:

```text
Chân LED
MỨC CAO   ┌────────────────────────┐
          │                        │
MỨC THẤP  ┘                        └──────────────────
          0us                     50us              100us
```

LED được cấp điện nửa thời gian, nên nhìn sáng vừa.

## 5. Duty cao: LED sáng mạnh

Ví dụ `CCR1 = 75`:

```text
MỨC CAO 75 us, MỨC THẤP 25 us
Duty 75%
```

Sơ đồ:

```text
Chân LED
MỨC CAO   ┌────────────────────────────────────┐
          │                                    │
MỨC THẤP  ┘                                    └──────
          0us                                75us   100us
```

LED được cấp điện phần lớn thời gian, nên nhìn sáng hơn.

## 6. Điều cần nhớ khi điều khiển LED

PWM không làm chân LED có điện áp analog liên tục 25%, 50%, hay 75%.

Thực tế chân vẫn chỉ có hai trạng thái:

```text
MỨC CAO hoặc MỨC THẤP
```

Nhưng vì bật/tắt nhanh, mắt người thấy giá trị trung bình.

## 7. Kết nối với Timer

Nếu LED được nối vào một chân PWM, thì:

| Giá trị | Tác dụng |
| --- | --- |
| `ARR` | Chọn PWM lặp lại nhanh hay chậm |
| `CCR1/Pulse` | Chọn LED sáng bao nhiêu |
| `f_PWM` | Nếu quá thấp có thể thấy nhấp nháy |
| Duty | Tỉ lệ thời gian LED được bật |
