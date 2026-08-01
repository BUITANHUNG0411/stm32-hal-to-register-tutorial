# 01 - Timer: Bộ Đếm Thời Gian

## 1. Timer là gì?

Timer trong STM32 là một bộ đếm phần cứng. Nó không "nghĩ" như code trong vòng lặp, mà tự đếm theo clock nội bộ.

Bạn có thể hình dung timer như một người đếm số rất đều:

```text
0, 1, 2, 3, 4, ... rồi quay lại 0
```

Mỗi lần số đếm tăng lên 1, ta gọi là một **counter tick**.

Trong bài này:

```text
System clock = 72 MHz
TIM1 clock   = 72 MHz
PSC          = 71
ARR          = 99
```

## 2. PSC là gì?

`PSC` là prescaler, tức bộ chia clock trước khi đưa vào counter.

Công thức:

```text
f_CNT = f_TIM / (PSC + 1)
```

Với `f_TIM = 72 MHz` và `PSC = 71`:

```text
f_CNT = 72 MHz / (71 + 1)
f_CNT = 72 MHz / 72
f_CNT = 1 MHz
```

Nghĩa là counter tăng lên 1,000,000 lần mỗi giây.

## 3. T_tick là gì?

Nếu counter tăng với tần số `1 MHz`, thì mỗi tick mất:

```text
T_tick = 1 / f_CNT
T_tick = 1 / 1,000,000
T_tick = 1 us
```

Vậy timer đếm như sau:

```text
Thời gian:  0us   1us   2us   3us   4us   ...   99us
Counter:    0     1     2     3     4     ...    99
```

## 4. ARR là gì?

`ARR` là Auto-Reload Register. Nó quy định counter đếm tới đâu thì quay về `0`.

Với:

```text
ARR = 99
```

Timer đếm:

```text
0, 1, 2, 3, ..., 98, 99, rồi quay về 0
```

Cần nhớ: đếm từ `0` đến `99` là **100 giá trị**, không phải 99 giá trị.

```text
Số tick trong một vòng = ARR + 1 = 99 + 1 = 100 tick
```

## 5. Một vòng đếm mất bao lâu?

Mỗi tick mất `1 us`. Một vòng có `100 tick`.

```text
T_vòng = 100 x 1 us = 100 us
```

Nếu timer đang dùng để tạo PWM, một vòng đếm này chính là một **chu kỳ PWM**.

```text
0us                                                100us
|----------------------------------------------------|
0    1    2    3    ...                         98   99
```

## 6. Nhầm lẫn quan trọng

Sai:

```text
ARR = 99 nên có 10,000 count trong một chu kỳ PWM
```

Đúng:

```text
ARR = 99 nên có 100 tick trong một chu kỳ PWM
```

Số `10,000` đến từ tần số PWM:

```text
Một chu kỳ PWM = 100 us
Trong 1 giây có 1 / 100 us = 10,000 chu kỳ
```

## 7. Tóm tắt

| Tên | Nghĩa | Giá trị trong project |
| --- | --- | --- |
| `f_TIM` | Clock đưa vào timer | 72 MHz |
| `PSC` | Bộ chia clock | 71 |
| `f_CNT` | Tốc độ counter tăng | 1 MHz |
| `T_tick` | Thời gian của 1 tick | 1 us |
| `ARR` | Giá trị đếm cao nhất | 99 |
| `ARR + 1` | Số tick trong một vòng | 100 tick |
