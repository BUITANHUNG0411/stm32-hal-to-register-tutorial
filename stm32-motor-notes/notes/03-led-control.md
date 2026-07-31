# 03 - Dieu Khien LED Bang PWM

## 1. LED neu bat/tat cham

Neu ban bat/tat LED cham, mat nguoi se thay LED nhap nhay:

```text
Sang ... tat ... sang ... tat ...
```

Dieu nay khong phai dimming tot, vi nguoi nhin thay flicker.

## 2. LED neu bat/tat nhanh bang PWM

Neu PWM nhanh, vi du `10 kHz`, mot chu ky chi dai:

```text
100 us
```

Mat nguoi khong kip thay tung xung bat/tat. No chi cam nhan do sang trung binh.

## 3. Duty thap: LED mo

Vi du `CCR1 = 25`:

```text
HIGH 25 us, LOW 75 us
Duty 25%
```

So do:

```text
LED pin
HIGH  ┌────────────┐
      │            │
LOW   ┘            └──────────────────────────────────
      0us         25us                               100us
```

LED duoc cap dien chi 25% thoi gian, nen nhin mo.

## 4. Duty trung binh: LED sang vua

Vi du `CCR1 = 50`:

```text
HIGH 50 us, LOW 50 us
Duty 50%
```

So do:

```text
LED pin
HIGH  ┌────────────────────────┐
      │                        │
LOW   ┘                        └──────────────────────
      0us                     50us                  100us
```

LED duoc cap dien nua thoi gian, nen nhin sang vua.

## 5. Duty cao: LED sang manh

Vi du `CCR1 = 75`:

```text
HIGH 75 us, LOW 25 us
Duty 75%
```

So do:

```text
LED pin
HIGH  ┌────────────────────────────────────┐
      │                                    │
LOW   ┘                                    └──────────
      0us                                75us       100us
```

LED duoc cap dien phan lon thoi gian, nen nhin sang hon.

## 6. Dieu can nho khi dieu khien LED

PWM khong lam chan LED co dien ap analog lien tuc 25%, 50%, hay 75%.

Thuc te chan van chi co hai trang thai:

```text
HIGH hoac LOW
```

Nhung vi bat/tat nhanh, mat nguoi thay gia tri trung binh.

## 7. Ket noi voi Timer

Neu LED duoc noi vao mot chan PWM, thi:

| Gia tri | Tac dung |
| --- | --- |
| `ARR` | Chon PWM lap lai nhanh hay cham |
| `CCR1/Pulse` | Chon LED sang bao nhieu |
| `f_PWM` | Neu qua thap co the thay nhap nhay |
| Duty | Ty le thoi gian LED duoc bat |
