---
name: atomic-note
description: Định dạng chuẩn cho mọi ghi chú trong vault (claim, source, entity, artifact, run) và frontmatter dùng làm cạnh đồ thị. Dùng mỗi khi tạo hoặc cập nhật ghi chú trong 10-notes, 20-sources, 30-artifacts, 40-runs.
---

# Ghi chú nguyên tử

Mỗi file là một nút của đồ thị. Frontmatter là API ghi: mỗi key liên kết là một cạnh.

## 5 loại nút

| `type` | Thư mục | Là gì |
|---|---|---|
| `source` | `20-sources/` | tài liệu gốc: bài viết, video, issue, email, biên bản họp |
| `claim` | `10-notes/` | một luận điểm duy nhất, rút ra từ nguồn |
| `entity` | `10-notes/` | người, công ty, sản phẩm, dự án được nhắc tới |
| `artifact` | `30-artifacts/` | sản phẩm tổng hợp: báo cáo, tóm tắt, kế hoạch |
| `run` | `40-runs/` | nhật ký một lần chạy vòng lặp |

## 5 loại cạnh

| Key | Nghĩa |
|---|---|
| `source` | claim này lấy từ nguồn nào |
| `supports` / `contradicts` | claim này ủng hộ / mâu thuẫn với claim nào |
| `supersedes` | claim này thay thế claim cũ nào |
| `mentions` | nhắc tới entity nào |
| `derived_from` | artifact/run này dựng từ những ghi chú nào |

Ngoài ra `run` trỏ tới lần chạy đã tạo ra ghi chú.

## Mẫu claim

```markdown
---
id: 2026-10-26-vault-la-trang-thai
type: claim
status: draft            # inbox | draft | reviewed | superseded
created: 2026-10-26
source: ["[[2026-10-26-kns-claude-obsidian]]"]
supports: []
contradicts: []
supersedes: []
mentions: []
run: "[[2026-10-26T0700-triage]]"
confidence: 0.7          # 0–1, mức tin vào luận điểm
tags: [loop-engineering]
---

# Vault là trạng thái của vòng lặp, không phải lịch sử chat

Một câu nêu luận điểm. Sau đó 2–5 câu giải thích hoặc dẫn chứng.

> Trích ngắn từ nguồn nếu có.
```

## Mẫu source

```markdown
---
id: 2026-10-26-ten-nguon
type: source
status: reviewed
created: 2026-10-26
url: https://...
author: ...
tags: []
---

# Tiêu đề nguồn

Tóm tắt 3–5 dòng. Đính kèm: ![[ten-file-dinh-kem.png]]
```

## Mẫu run

```markdown
---
id: 2026-10-26T0700-triage
type: run
created: 2026-10-26
derived_from: ["[[ghi-chu-inbox-da-xu-ly]]"]
---

# Run 2026-10-26 07:00 – triage

- Đã đọc: …
- Đã tạo: …
- Critic: đạt / cần sửa (lý do)
- Câu hỏi cho người dùng: …
```

## Quy tắc

- Tên file = `id` + `.md`. Slug không dấu, chữ thường, gạch nối.
- Một claim, một luận điểm. Hai ý thì tách hai file và nối bằng `supports`.
- Giá trị liên kết luôn là danh sách wikilink trong ngoặc kép: `["[[a]]", "[[b]]"]`. Obsidian hiển thị được dạng này.
- Trước khi tạo claim, tìm trong `10-notes/` xem đã có ý tương tự chưa. Có rồi thì thêm cạnh `supports`, không tạo bản trùng.
- Không xoá, không viết đè. Thay ý cũ bằng `supersedes`, rồi đặt `status: superseded` cho file cũ.
