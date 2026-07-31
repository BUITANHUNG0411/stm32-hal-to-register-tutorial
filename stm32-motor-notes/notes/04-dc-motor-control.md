# 04 - Dieu Khien Dong Co DC Bang PWM Va L298N

## 1. Vai tro cua L298N

STM32 khong cap dong truc tiep cho motor DC. Chan STM32 chi la tin hieu dieu khien.

Trong project:

```text
PA8 PWM  -> L298N ENA
PB12     -> L298N IN1
PB13     -> L298N IN2
Motor    -> L298N OUT1/OUT2
Nguon motor -> 12 V ngoai
```

L298N nhan tin hieu tu STM32, roi dong/ngat nguon motor theo tin hieu do.

## 2. PWM tren ENA lam gi?

Chan `ENA` cua L298N cho phep hoac chan dong co.

Neu `PA8` tao PWM dua vao `ENA`, thi L298N se cap nang luong cho motor theo tung xung:

```text
PA8 HIGH -> ENA bat -> motor duoc cap nang luong
PA8 LOW  -> ENA tat -> motor tam thoi khong duoc cap nang luong
```

Vi PWM dang o `10 kHz`, moi chu ky chi dai `100 us`. Motor co quan tinh co hoc, nen khong dung/quay lai theo tung xung nho. No cam nhan gan nhu cong suat trung binh.

## 3. Duty thap: motor yeu

Vi du `CCR1 = 25`:

```text
ENA
HIGH  ┌────────────┐
      │            │
LOW   ┘            └──────────────────────────────────
      0us         25us                               100us

Motor nhan nang luong gan 25% thoi gian.
```

Ket qua thuc te: motor co xu huong quay cham/yeu hon, nhung con phu thuoc tai, ma sat, nguon, va dac tinh motor.

## 4. Duty 50%: motor trung binh

Vi du `CCR1 = 50`:

```text
ENA
HIGH  ┌────────────────────────┐
      │                        │
LOW   ┘                        └──────────────────────
      0us                     50us                  100us

Motor nhan nang luong gan 50% thoi gian.
```

## 5. Duty cao: motor manh

Vi du `CCR1 = 75`:

```text
ENA
HIGH  ┌────────────────────────────────────┐
      │                                    │
LOW   ┘                                    └──────────
      0us                                75us       100us

Motor nhan nang luong gan 75% thoi gian.
```

## 6. IN1 va IN2 quyet dinh huong

PWM tren `ENA` quyet dinh motor duoc cap nang luong bao nhieu. Con `IN1/IN2` quyet dinh huong dong qua motor.

| IN1 | IN2 | Hanh vi gan dung |
| --- | --- | --- |
| 1 | 0 | Quay mot chieu |
| 0 | 1 | Quay chieu nguoc lai |
| 0 | 0 | Coast/stop, tuy module va cach noi |
| 1 | 1 | Brake, nhung tuy module/driver co caveat |

Khi moi hoc, hay tam nho:

```text
IN1/IN2 = chon huong
ENA PWM = chon do manh
```

## 7. Luu y an toan

Khong cap nguon motor tu Blue Pill.

Nen dung:

```text
Nguon 12 V ngoai -> L298N motor supply
STM32 GND -------+
L298N GND -------+  chung mass
```

STM32 va L298N phai chung GND de tin hieu `PA8`, `PB12`, `PB13` co cung moc dien ap.

## 8. Tom tat

| Gia tri | Tac dung voi motor |
| --- | --- |
| `ARR` | Dinh chu ky/tan so PWM |
| `CCR1/Pulse` | Dinh duty, tuc thoi gian ENA duoc bat |
| Duty thap | Motor yeu/cham hon |
| Duty cao | Motor manh/nhanh hon |
| IN1/IN2 | Chon huong quay hoac che do dung |
