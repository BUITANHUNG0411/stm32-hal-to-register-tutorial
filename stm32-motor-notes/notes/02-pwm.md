# 02 - PWM: Xung Bat Tat Nhanh

## 1. PWM la gi?

PWM la viet tat cua **Pulse Width Modulation**, tuc dieu che do rong xung.

No khong tao ra dien ap analog that su. No bat/tat rat nhanh:

```text
HIGH, LOW, HIGH, LOW, HIGH, LOW...
```

Neu bat/tat du nhanh, LED se thay do sang trung binh, con motor se nhan nang luong trung binh.

## 2. Chu ky PWM

Voi Timer 1 hien tai:

```text
PSC = 71
ARR = 99
1 tick = 1 us
1 chu ky PWM = 100 tick = 100 us
f_PWM = 10 kHz
```

Mot chu ky PWM la mot lan timer dem tu `0` den `99`.

```text
0us                                                100us
|----------------------------------------------------|
0    1    2    3    ...                         98   99
```

## 3. CCR1/Pulse la gi?

`CCR1` la Capture/Compare Register kenh 1. Trong CubeMX/HAL, ban hay thay no duoi ten **Pulse**.

Voi PWM mode thong dung, co the hieu don gian:

```text
Counter < CCR1   => chan PWM HIGH
Counter >= CCR1  => chan PWM LOW
```

Trong project:

```text
PA8 = TIM1_CH1
CCR1 = Pulse cua TIM1 Channel 1
```

Vay `CCR1` quyet dinh PA8 HIGH bao lau trong moi chu ky `100 us`.

## 4. So do xung theo CCR1

### CCR1 = 0, duty 0%

```text
PA8
HIGH
LOW   ────────────────────────────────────────────────
      0us                                            100us

HIGH time = 0 us
LOW time  = 100 us
```

### CCR1 = 25, duty gan 25%

```text
PA8
HIGH  ┌────────────┐
      │            │
LOW   ┘            └──────────────────────────────────
      0us         25us                               100us

HIGH time = 25 us
LOW time  = 75 us
```

### CCR1 = 50, duty gan 50%

```text
PA8
HIGH  ┌────────────────────────┐
      │                        │
LOW   ┘                        └──────────────────────
      0us                     50us                  100us

HIGH time = 50 us
LOW time  = 50 us
```

### CCR1 = 75, duty gan 75%

```text
PA8
HIGH  ┌────────────────────────────────────┐
      │                                    │
LOW   ┘                                    └──────────
      0us                                75us       100us

HIGH time = 75 us
LOW time  = 25 us
```

### CCR1 = 99, duty gan 99%

```text
PA8
HIGH  ┌──────────────────────────────────────────────┐
      │                                              │
LOW   ┘                                              └
      0us                                          99us 100us

HIGH time gan 99 us
LOW time  gan 1 us
```

## 5. Duty cycle la gi?

Duty cycle la ti le thoi gian HIGH trong mot chu ky PWM.

```text
Duty = HIGH time / total period
```

Voi `ARR = 99`, mot chu ky co `100 tick`, nen co the uoc tinh:

```text
Duty gan dung = CCR1 / 100
```

Vi du:

| CCR1 | HIGH time | LOW time | Duty gan dung |
| --- | ---: | ---: | ---: |
| 0 | 0 us | 100 us | 0% |
| 25 | 25 us | 75 us | 25% |
| 50 | 50 us | 50 us | 50% |
| 75 | 75 us | 25 us | 75% |
| 99 | 99 us | 1 us | 99% |

## 6. ARR va CCR1 khac nhau the nao?

| Thanh phan | Quyet dinh cai gi? |
| --- | --- |
| `ARR` | Do dai ca chu ky PWM |
| `CCR1` | HIGH bao lau trong chu ky do |

Neu giu `ARR = 99`, tan so PWM giu o `10 kHz`.

Khi thay doi `CCR1`, ta khong doi tan so PWM. Ta chi doi **duty cycle**.

## 7. Tom tat

```text
ARR  = khung thoi gian ca chu ky
CCR1 = phan HIGH nam trong khung do
PWM  = bat/tat nhanh theo chu ky
Duty = HIGH time / total period
```
