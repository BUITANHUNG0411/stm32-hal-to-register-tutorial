---
name: register-tutorial
description: Tạo hoặc review nội dung tutorial HAL-to-Register cho STM32 motor controller, luôn bám repository tham chiếu và giữ nguyên CubeMX init.
---

# Register Tutorial Workflow

Dùng workflow này khi tạo code sample, section tutorial, bảng mapping hoặc review nội dung firmware.

## Bắt buộc đọc

1. `PRD.md`
2. `docs/reference-register.md`
3. File nguồn liên quan trong repository tham chiếu
4. Rule phù hợp trong `.claude/rules/`

## Các bước

1. Xác định phần đang xử lý là init hay application. Nếu là init, không thay đổi scope.
2. Ghi lại fact từ repo: file, hàm, pin, giới hạn và luồng gọi.
3. Viết mapping HAL → register với đúng thứ tự thao tác.
4. Viết code Register tối thiểu tương đương logic sau init.
5. Kiểm tra invariants: ADC `0..4095`, pulse `0..99`, PB12/PB13, timeout EOC.
6. Giải thích bằng ngôn ngữ cho người mới.
7. Ghi rõ phần chưa thể xác minh trên board.

## Output format

- `Source facts`
- `Register mapping`
- `Code sample`
- `Beginner explanation`
- `Safety notes`
- `Verification status`

## Không được làm

- Không sửa CubeMX init.
- Không tự đổi pin hoặc thêm encoder/closed-loop.
- Không khẳng định hardware pass khi chỉ kiểm tra bằng suy luận hoặc static inspection.
