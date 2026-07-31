# So Tay STM32 Timer PWM Motor Control

Day la quyen note nho de hoc Timer va PWM tren STM32F103C8T6 Blue Pill. Muc tieu cua ban nay la nam du chac de hieu:

- PWM lam LED mo/sang nhu the nao.
- PWM dieu khien cong suat gan dung cua dong co DC qua L298N nhu the nao.
- `PSC`, `ARR`, `CCR1/Pulse`, `f_CNT`, `f_PWM` khac nhau ra sao.

Ban web truc quan nam o: [index.html](index.html)

## Ngu canh phan cung

| Thanh phan | Cau hinh hien tai |
| --- | --- |
| MCU | STM32F103C8T6 Blue Pill |
| System clock | 72 MHz |
| PWM motor | PA8 = TIM1_CH1 |
| Driver | L298N, chan ENA nhan PWM tu PA8 |
| Huong motor | PB12 = IN1, PB13 = IN2 |
| Motor supply | Ngoai 12 V / 10 A |
| Luu y | STM32 va L298N phai chung GND |

## Thu tu hoc de de hieu nhat

1. [Timer: bo dem thoi gian](notes/01-timer.md)
2. [PWM: xung bat/tat nhanh](notes/02-pwm.md)
3. [Dieu khien LED bang PWM](notes/03-led-control.md)
4. [Dieu khien dong co DC bang PWM va L298N](notes/04-dc-motor-control.md)

## Cong thuc chinh trong project

```text
f_CNT = f_TIM / (PSC + 1)
T_tick = 1 / f_CNT
T_PWM = (ARR + 1) x T_tick
f_PWM = 1 / T_PWM
Duty gan dung = CCR1 / (ARR + 1)
```

Voi cau hinh hien tai:

```text
f_TIM = 72 MHz
PSC = 71
ARR = 99

f_CNT = 72 MHz / 72 = 1 MHz
T_tick = 1 us
T_PWM = 100 x 1 us = 100 us
f_PWM = 10 kHz
```

## Dieu can nho

`ARR = 99` khong co nghia la 10,000 count trong mot chu ky PWM. Timer dem tu `0` den `99`, tong cong `100` tick. Con `10,000` la so chu ky PWM lap lai trong 1 giay, tuc `10 kHz`.
