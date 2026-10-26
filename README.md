# Claude + Obsidian vault

Bộ não thứ hai: **Obsidian để đọc và ghi chú, Claude để xử lý và liên kết ghi chú**. Mọi thứ Claude biết đều nằm trong
các file Markdown ở thư mục này, nên tắt máy hay mở phiên chat mới cũng không mất gì.

Dựng theo infographic "Claude + Obsidian – Loop Engineering" (xem [[2026-10-26-kns-claude-obsidian]]).

## Cài đặt (một lần, khoảng 10 phút)

1. Cài **Obsidian** (obsidian.md) → *Open folder as vault* → chọn thư mục này.
2. Cài **Claude desktop** (claude.ai/download) → tab *Code* → chọn thư mục này làm thư mục làm việc.
   Dùng `claude` trong Terminal cũng được.
3. Nếu lấy từ GitHub: `git clone <link-repo>` rồi làm bước 1 và 2 với thư mục vừa clone.

Hai ứng dụng mở cùng một thư mục: Obsidian để xem, Claude để làm.

## Dùng hằng ngày: 4 việc

| Việc | Làm thế nào |
|---|---|
| **1. Ghi nhanh** | Trong Obsidian, tạo ghi chú mới trong `00-inbox/`. Viết thô, không cần định dạng: ý tưởng, link bài báo, nội dung họp. |
| **2. Cho Claude xử lý** | Trong Claude, gõ `/triage-inbox`. Claude tách inbox thành các ghi chú gọn, gắn nguồn, nối với ghi chú cũ, nhờ agent phản biện kiểm tra, rồi báo lại. |
| **3. Hỏi** | Hỏi thẳng: *"Ta đã biết gì về chi phí AI?"*, *"Có ghi chú nào mâu thuẫn nhau không?"*. Claude trả lời từ vault, kèm link về ghi chú gốc. |
| **4. Lấy báo cáo** | *"Tóm tắt những gì ghi trong tuần này"* → agent `summarizer` viết báo cáo vào `30-artifacts/`. |

Trong Obsidian, bấm **Graph view** (biểu tượng ở thanh bên trái) để xem các ghi chú nối với nhau thế nào.

**Thử ngay:** trong `00-inbox/` có sẵn một ý tưởng mẫu. Gõ `/triage-inbox` để xem vòng lặp chạy.

## Các thư mục

| Thư mục | Chứa gì | Ai ghi |
|---|---|---|
| `00-inbox/` | ghi chú thô | **bạn** (Claude chỉ đọc) |
| `10-notes/` | ghi chú gọn, mỗi ghi chú một ý | Claude |
| `20-sources/` | tài liệu gốc, link, file đính kèm | bạn và Claude |
| `30-artifacts/` | báo cáo, tóm tắt | Claude |
| `40-runs/` | nhật ký mỗi lần Claude xử lý | Claude |
| `MEMORY.md` | những điều Claude cần nhớ lâu dài | Claude, bạn sửa được |
| `.claude/` | quy tắc, kỹ năng, agent của Claude | chỉ sửa khi muốn đổi cách Claude làm việc |

Claude không bao giờ xoá hay viết đè ghi chú. Ý mới thay ý cũ thì ghi chú cũ được đánh dấu `superseded`, nội dung vẫn còn,
nên lúc nào cũng xem lại được ai nghĩ gì, khi nào.

## Bên trong `.claude/`

- `CLAUDE.md`: luật của vault, Claude đọc mỗi phiên.
- `skills/atomic-note`: định dạng chuẩn của một ghi chú.
- `skills/triage-inbox`: vòng lặp xử lý inbox, gọi bằng `/triage-inbox`.
- `agents/critic`: phản biện, kiểm tra ghi chú có nguồn, không bịa, không trùng.
- `agents/linker`: nối ghi chú mới với ghi chú cũ, sửa link gãy.
- `agents/summarizer`: viết báo cáo tổng hợp.

## Bước tiếp theo (khi đã quen)

- **Tự chạy mỗi sáng:** nhờ Claude *"tạo lịch chạy /triage-inbox lúc 7h sáng các ngày làm việc trong thư mục này"*.
- **Kéo nguồn tự động:** nhờ Claude lấy biên bản họp, tài liệu từ Lark và lưu vào `20-sources/`.
- **Thử nghiệm an toàn:** dùng git, mỗi thử nghiệm một nhánh riêng; hỏng thì bỏ nhánh, vault chính không ảnh hưởng.

## Năm quy tắc

1. Bắt đầu đơn giản: một vòng lặp, một agent.
2. Đo trước khi nâng cấp: cải thiện dưới 5% thì không đáng làm phức tạp thêm.
3. Việc càng rủi ro càng phải kiểm soát chặt: gửi mail, nhắn tin luôn phải hỏi người dùng.
4. Đếm token, không đếm số agent.
5. Chỉ dùng cấu trúc phức tạp khi nó bù lại được chi phí.
