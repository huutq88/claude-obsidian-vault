---
name: triage-inbox
description: Chạy một vòng lặp đầy đủ trên 00-inbox - đọc ghi chú thô, lấy ngữ cảnh, viết claim/source/entity trong một git worktree riêng, cho critic duyệt diff và linker kiểm tra link, rồi mới ghi vào vault và log vào 40-runs. Dùng khi người dùng gõ /triage-inbox, nói "xử lý inbox", hoặc khi chạy theo lịch.
---

# Triage inbox

Một lần gọi = một lượt của vòng lặp: capture → context → claude → draft → review → commit.
Bản nháp viết trong một git worktree riêng; vault đang dùng chỉ nhận thay đổi sau khi critic duyệt diff.

Gọi `VAULT` là thư mục vault, `RUN` là `YYYY-MM-DDTHHMM-triage`, `WT` là `../<tên-thư-mục-vault>-run-RUN`
(nằm ngoài vault để Obsidian không đọc nhầm bản nháp).

## Các bước

1. **Chọn việc.** Đọc `MEMORY.md`. Liệt kê file trong `00-inbox/`. Bỏ qua file đã có trong `derived_from` của một run
   trước (đã xử lý). Không còn gì thì trả lời "inbox trống" và dừng, không tạo run.
2. **Chốt inbox.** Ghi chú inbox người dùng mới viết thường chưa được commit, nên worktree sẽ không thấy chúng. Commit
   riêng thư mục inbox trên nhánh hiện tại: `git add 00-inbox && git commit -m "capture: <số> ghi chú inbox"`.
   Chỉ add `00-inbox`, không add file nào khác của người dùng.
3. **Mở worktree.** `git worktree add WT -b run/RUN`. Từ đây mọi bước đọc và ghi đều làm trong `WT`, không sửa gì trong
   `VAULT`. Tạo `WT/40-runs/RUN.md` theo mẫu run trong skill `atomic-note`.
4. **Context.** Với mỗi ghi chú inbox, tìm trong `10-notes/` và `20-sources/` các ghi chú cùng chủ đề (grep từ khoá,
   tên riêng, tag). Chỉ đọc những file liên quan.
5. **Draft.** Theo skill `atomic-note`:
   - có link, tài liệu hoặc tệp đính kèm → tạo `source` trong `20-sources/`
   - chỉ là ý tưởng tự ghi của người dùng → không tạo `source`; claim đặt `source` trỏ về file inbox
   - mỗi luận điểm → một `claim` trong `10-notes/`, `status: draft`
   - người, công ty, dự án mới được một claim nhắc tới trong thân → `entity`, và thêm vào `mentions` của claim đó
   - câu hỏi người dùng tự đặt trong inbox → chép vào mục câu hỏi của file run, không tạo claim hay entity cho nó
   - ý trùng với claim có sẵn → thêm cạnh `supports`, không tạo bản trùng
   - ý trái với claim có sẵn → tạo claim mới với `contradicts`, không sửa claim cũ

   Xong thì commit trong worktree: `git -C WT add -A && git -C WT commit -m "draft: RUN"`.
6. **Review diff.** Lấy diff của lượt chạy: `git -C WT diff <nhánh-chính>...run/RUN`. Gọi agent `critic`, truyền
   đường dẫn `WT` và toàn bộ diff. Sau đó gọi agent `linker` với `WT` và danh sách file mới. Sửa theo góp ý trong `WT`,
   commit thêm, rồi lấy diff mới cho critic duyệt lại. Tối đa 2 vòng sửa.
7. **Commit vào vault.**
   - **Đạt:** trong `WT`, đổi `status: reviewed` cho file đã qua review, ghi kết quả vào file run (file đã đọc, file đã
     tạo, kết quả critic, câu hỏi cho người dùng), commit. Điều đáng nhớ lâu dài thì thêm một dòng vào `MEMORY.md`.
     Quay về `VAULT`: `git merge --ff-only run/RUN`, rồi `git worktree remove WT` và `git branch -d run/RUN`.
   - **Chưa đạt sau 2 vòng:** không merge. Gỡ worktree (`git worktree remove --force WT`) nhưng giữ nhánh `run/RUN`
     để người dùng xem lại, và báo lý do.
   - **Merge không được** (vault có thay đổi khác chen vào): không ép, giữ nhánh và báo người dùng.
8. **Báo cáo.** Trả lời người dùng 3–5 dòng: đã xử lý gì, tạo mấy ghi chú, critic đạt hay không, có gì cần họ quyết
   định. Link tới file run.

## Vault không phải git repo

Bỏ bước 2, 3 và lệnh git; viết nháp thẳng trong vault với `status: draft`, cho critic đọc danh sách file mới thay cho
diff. Nhắc người dùng chạy `git init` để có worktree.

## Không được

- Sửa hoặc xoá file trong `00-inbox/`.
- Viết nháp trực tiếp trong vault đang dùng khi vault là git repo.
- Merge khi critic chưa đạt, hoặc dùng `push`, `reset --hard`, `merge` không có `--ff-only`.
- Xử lý lại ghi chú inbox đã nằm trong một run trước.
- Gửi gì ra ngoài vault (mail, chat) khi người dùng chưa đồng ý.
