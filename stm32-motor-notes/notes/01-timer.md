# 01 - Timer: Bo Dem Thoi Gian

## 1. Timer la gi?

Timer trong STM32 la mot bo dem phan cung. No khong "nghi" nhu code trong vong lap, ma tu dem theo clock noi bo.

Ban co the hinh dung timer nhu mot nguoi dem so rat deu:

```text
0, 1, 2, 3, 4, ... roi quay lai 0
```

Moi lan so dem tang len 1, ta goi la mot **counter tick**.

Trong bai nay:

```text
System clock = 72 MHz
TIM1 clock   = 72 MHz
PSC          = 71
ARR          = 99
```

## 2. PSC la gi?

`PSC` la prescaler, tuc bo chia clock truoc khi dua vao counter.

Cong thuc:

```text
f_CNT = f_TIM / (PSC + 1)
```

Voi `f_TIM = 72 MHz` va `PSC = 71`:

```text
f_CNT = 72 MHz / (71 + 1)
f_CNT = 72 MHz / 72
f_CNT = 1 MHz
```

Nghia la counter tang len 1,000,000 lan moi giay.

## 3. T_tick la gi?

Neu counter tang voi tan so `1 MHz`, thi moi tick mat:

```text
T_tick = 1 / f_CNT
T_tick = 1 / 1,000,000
T_tick = 1 us
```

Vay timer dem nhu sau:

```text
Thoi gian:  0us   1us   2us   3us   4us   ...   99us
Counter:    0     1     2     3     4     ...    99
```

## 4. ARR la gi?

`ARR` la Auto-Reload Register. No quy dinh counter dem toi dau thi quay ve `0`.

Voi:

```text
ARR = 99
```

Timer dem:

```text
0, 1, 2, 3, ..., 98, 99, roi quay ve 0
```

Can nho: dem tu `0` den `99` la **100 gia tri**, khong phai 99 gia tri.

```text
So tick trong mot vong = ARR + 1 = 99 + 1 = 100 tick
```

## 5. Mot vong dem mat bao lau?

Moi tick mat `1 us`. Mot vong co `100 tick`.

```text
T_vong = 100 x 1 us = 100 us
```

Neu timer dang dung de tao PWM, mot vong dem nay chinh la mot **chu ky PWM**.

```text
0us                                                100us
|----------------------------------------------------|
0    1    2    3    ...                         98   99
```

## 6. Nham lan quan trong

Sai:

```text
ARR = 99 nen co 10,000 count trong mot chu ky PWM
```

Dung:

```text
ARR = 99 nen co 100 tick trong mot chu ky PWM
```

So `10,000` den tu tan so PWM:

```text
Mot chu ky PWM = 100 us
Trong 1 giay co 1 / 100 us = 10,000 chu ky
```

## 7. Tom tat

| Ten | Nghia | Gia tri trong project |
| --- | --- | --- |
| `f_TIM` | Clock dua vao timer | 72 MHz |
| `PSC` | Bo chia clock | 71 |
| `f_CNT` | Toc do counter tang | 1 MHz |
| `T_tick` | Thoi gian cua 1 tick | 1 us |
| `ARR` | Gia tri dem cao nhat | 99 |
| `ARR + 1` | So tick trong mot vong | 100 tick |
