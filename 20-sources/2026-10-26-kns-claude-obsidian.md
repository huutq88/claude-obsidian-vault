---
id: 2026-10-26-kns-claude-obsidian
type: source
status: reviewed
created: 2026-10-26
url: "https://x.com/polydao/status/2087089133156208803"
author: Mr. Buzzoni (infographic), KNS – Kỹ năng số thời đại AI (bài đăng)
tags: [loop-engineering, claude, obsidian]
---

# Infographic "Claude + Obsidian – Loop Engineering"

Bài đăng của trang KNS – Kỹ năng số thời đại AI, giới thiệu mô hình của Mr. Buzzoni: một agent đọc và ghi vào vault
Obsidian thay vì dựa vào lịch sử chat. Vault là trạng thái của vòng lặp; mọi thứ Claude biết đều là file Markdown.

Nội dung chính:

- Vòng lặp capture → context → claude → draft → review → commit, mỗi lượt là một lần sửa đổi.
- Cấu trúc vault: `00-inbox`, `10-notes`, `20-sources`, `30-artifacts`, `40-runs`, `.claude/`, `MEMORY.md`.
- Frontmatter của ghi chú là cạnh của đồ thị.
- Sáu thành phần: automations, worktrees, skills, connectors, sub-agents, memory.
- Bốn kiểu kiến trúc và chi phí token: loop 2–4×, network 5–10×, graph 10–50× so với một lần gọi trực tiếp.
- Năm quy tắc và bảy lỗi cần tránh.

Phần chữ của bài đăng KNS nói thêm 2 điểm mà infographic chỉ vẽ lướt:

- Bản nháp được soạn trong một git worktree riêng, không chạm trực tiếp vào vault đang dùng.
- Critic agent rà toàn bộ phần thay đổi (diff) trước khi ghi vào vault.

Bài đăng gọi con số 55% → 72% → 84% là mức chính xác của một trợ lý kiểm duyệt khi áp dụng lần lượt các kiểu kiến trúc.

Ảnh và video gốc là nội dung của tác giả, không đưa vào repo. Bài gốc trên X: xem `url`.
