# Glossary cho người mới

## HAL
Thư viện cấp cao của ST. Thay vì tự ghi từng thanh ghi, người học gọi hàm như `HAL_ADC_Start`.

## Register / thanh ghi
Một vùng nhớ đặc biệt bên trong MCU dùng để điều khiển hoặc đọc trạng thái phần cứng.

## RCC
Bộ điều khiển clock. Ngoại vi phải được cấp clock trước khi sử dụng.

## ADC
Bộ chuyển đổi điện áp analog thành số. Trong project, ADC đọc potentiometer thành giá trị `0..4095`.

## EOC
Cờ báo ADC đã hoàn thành conversion (End Of Conversion).

## PWM
Tín hiệu xung dùng để điều chỉnh công suất/tốc độ motor bằng duty cycle.

## Duty / pulse
Mức compare của PWM. Project dùng khoảng `0..99`.

## Timer
Ngoại vi tạo thời gian và PWM. Project dùng TIM1 Channel 1.

## GPIO
Chân vào/ra số. PB12 và PB13 điều khiển IN1/IN2 của L298N.

## L298N
Mạch cầu H dùng để điều khiển chiều và công suất cho motor DC.

## Open-loop
Điều khiển không có cảm biến phản hồi tốc độ. Project chưa dùng encoder.

## CubeMX init
Các hàm khởi tạo do STM32CubeMX sinh ra. Trong khóa học này, phần đó được giữ nguyên.

## Bản đồ HAL → Register
Bảng nối mỗi lời gọi HAL với các thanh ghi/bit trực tiếp thực hiện cùng nhiệm vụ.
