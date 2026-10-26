# Quy tắc của vault

Thư mục này vừa là vault Obsidian vừa là project Claude Code. **Vault là trạng thái của vòng lặp**: mọi thứ agent biết
đều nằm trong file `.md` ở đây, không nằm trong lịch sử chat. Muốn nhớ gì thì ghi vào vault.

## Vòng lặp (một lượt = một lần sửa đổi)

capture → context → claude → draft → review → commit

1. **Capture**: người dùng thả ý tưởng thô vào `00-inbox/`.
2. **Context**: đọc ghi chú inbox, tìm ghi chú liên quan qua link, tag và tên trong `10-notes/`, `20-sources/`.
3. **Claude**: suy luận từ vault, không dựa vào trí nhớ hội thoại.
4. **Draft**: viết bản nháp với `status: draft` trong một git worktree riêng, không sửa trực tiếp vault đang dùng.
5. **Review**: agent `critic` duyệt toàn bộ diff của lượt chạy, agent `linker` kiểm tra link.
6. **Commit**: chỉ ghi thêm. Đổi trạng thái sang `reviewed`, ghi một file log vào `40-runs/`, rồi merge `--ff-only`
   nhánh của lượt chạy vào vault. Critic chưa đạt thì không merge.

Các bước chi tiết nằm trong skill `triage-inbox`.

## Agent được phép chạm vào đâu

| Thư mục | Nội dung | Quyền của agent |
|---|---|---|
| `00-inbox/` | ghi chú thô của người dùng | chỉ đọc, không sửa, không xoá |
| `10-notes/` | ghi chú nhỏ, mỗi ghi chú một luận điểm | tạo mới; sửa frontmatter `status`, `superseded_by` |
| `20-sources/` | nguồn đã cắt về, kèm link gốc | tạo mới; không sửa nội dung trích |
| `30-artifacts/` | sản phẩm agent làm ra (báo cáo, tóm tắt), có ngày | tạo mới |
| `40-runs/` | mỗi lần chạy vòng lặp một file | tạo mới |
| `MEMORY.md` | mỗi dòng một điều cần nhớ lâu dài | thêm dòng |
| `.claude/` | quy tắc, skill, agent | chỉ sửa khi người dùng yêu cầu |

Tiền tố số (00, 10, 20…) giữ thứ tự ổn định giữa các lần chạy. Không tạo thư mục cấp một mới.

## Quy tắc ghi

- **Không viết đè, không xoá.** Ý mới mâu thuẫn hoặc thay thế ý cũ thì tạo ghi chú mới với `supersedes: ["[[ghi-chu-cu]]"]`,
  rồi đặt `status: superseded` cho ghi chú cũ. Nội dung ghi chú cũ giữ nguyên.
- **Frontmatter là đồ thị.** Mỗi key liên kết (`source`, `supports`, `contradicts`, `supersedes`, `mentions`,
  `derived_from`, `run`) là một cạnh. Định dạng chi tiết: skill `atomic-note`.
- Tên file: `YYYY-MM-DD-slug-khong-dau.md`, slug ngắn, chữ thường, gạch nối. `id` trong frontmatter trùng tên file.
- Link nội bộ dùng wikilink `[[ten-file]]`, không dùng đường dẫn tương đối.
- Viết tiếng Việt, ngắn gọn. Một ghi chú `claim` chỉ chứa một luận điểm.
- Mỗi luận điểm phải có `source` hoặc `derived_from`. Không có nguồn thì `confidence` ≤ 0.5 và ghi rõ là suy luận.
- Không bịa nguồn, số liệu, link. Không chắc thì ghi câu hỏi vào cuối file log trong `40-runs/`.

## Năm quy tắc vận hành

1. Bắt đầu rẻ: một agent, một vòng lặp đơn giản trước.
2. Đo trước khi nâng cấp: cải thiện dưới 5% thì không đáng thêm độ phức tạp.
3. Mức kiểm soát tương xứng với rủi ro: việc gửi ra ngoài (mail, chat) phải hỏi người dùng.
4. Đếm token, không đếm số agent.
5. Đồ thị phải bù lại được chi phí của nó.

Đọc `MEMORY.md` ở đầu mỗi phiên.
