---
name: triage-inbox
description: Chạy một vòng lặp đầy đủ trên 00-inbox - đọc ghi chú thô, lấy ngữ cảnh, viết claim/source/entity, cho critic và linker review, rồi ghi log vào 40-runs. Dùng khi người dùng gõ /triage-inbox, nói "xử lý inbox", hoặc khi chạy theo lịch.
---

# Triage inbox

Một lần gọi = một lượt của vòng lặp: capture → context → claude → draft → review → commit.

## Các bước

1. **Mở run.** Tạo `40-runs/YYYY-MM-DDTHHMM-triage.md` (mẫu run trong skill `atomic-note`). Đọc `MEMORY.md`.
2. **Chọn việc.** Liệt kê file trong `00-inbox/`. Bỏ qua file đã có trong `derived_from` của một run trước (đã xử lý).
   Không còn gì thì ghi "inbox trống" vào run và dừng.
3. **Context.** Với mỗi ghi chú inbox, tìm trong `10-notes/` và `20-sources/` các ghi chú cùng chủ đề (grep từ khoá,
   tên riêng, tag). Chỉ đọc những file liên quan.
4. **Draft.** Theo skill `atomic-note`:
   - có link, tài liệu hoặc tệp đính kèm → tạo `source` trong `20-sources/`
   - chỉ là ý tưởng tự ghi của người dùng → không tạo `source`; claim đặt `source` trỏ về file inbox
   - mỗi luận điểm → một `claim` trong `10-notes/`, `status: draft`
   - người, công ty, dự án mới được một claim nhắc tới trong thân → `entity`, và thêm vào `mentions` của claim đó
   - câu hỏi người dùng tự đặt trong inbox → chép vào mục câu hỏi của file run, không tạo claim hay entity cho nó
   - ý trùng với claim có sẵn → thêm cạnh `supports`, không tạo bản trùng
   - ý trái với claim có sẵn → tạo claim mới với `contradicts`, không sửa claim cũ
5. **Review.** Gọi agent `critic` với danh sách file vừa tạo, sau đó gọi agent `linker`. Sửa theo góp ý. Tối đa 2 vòng sửa;
   sau 2 vòng vẫn chưa đạt thì giữ `status: draft` và ghi lý do vào run.
6. **Commit.** File đã qua review thì đổi `status: reviewed`. Ghi vào run: file đã đọc, file đã tạo, kết quả critic,
   câu hỏi cho người dùng. Điều đáng nhớ lâu dài thì thêm một dòng vào `MEMORY.md`.
7. **Báo cáo.** Trả lời người dùng 3–5 dòng: đã xử lý gì, tạo mấy ghi chú, có gì cần họ quyết định. Link tới file run.

## Không được

- Sửa hoặc xoá file trong `00-inbox/`.
- Xử lý lại ghi chú inbox đã nằm trong một run trước.
- Gửi gì ra ngoài vault (mail, chat) khi người dùng chưa đồng ý.
