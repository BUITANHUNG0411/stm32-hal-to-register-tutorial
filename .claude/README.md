# Claude Code Project Setup

## Cách dùng cho người non-tech

1. Mở Claude Code tại thư mục `register-course`.
2. Nói mục tiêu bằng kết quả mong muốn, ví dụ: "Tạo section giải thích ADC HAL sang ADC register cho người mới".
3. Claude phải đọc `PRD.md` và `docs/reference-register.md` trước khi sửa.
4. Nếu cần xác minh firmware, yêu cầu dùng agent `register-explorer`.
5. Sau thay đổi, yêu cầu kiểm tra bằng `/run` hoặc mở `index.html`.

## File nào dùng cho việc gì?

- `CLAUDE.md`: luật chung luôn áp dụng.
- `.claude/rules/`: luật theo chủ đề.
- `.claude/skills/register-tutorial/SKILL.md`: quy trình chuyên môn gọi khi làm tutorial.
- `.claude/agents/register-explorer.md`: researcher read-only.
- `docs/`: kiến thức chi tiết, không nhồi vào instruction.
- Auto memory: learnings cá nhân của Claude Code; không dùng làm nguồn sự thật firmware.

## Kiểm tra instruction

- `/context`: xem file nào đã được load.
- `/memory`: xem và chỉnh auto memory.
- `/doctor`: kiểm tra cấu hình Claude Code.
