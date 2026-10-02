# Báo Cáo Bài Tập 4: Quản Lý Tệp Tin Bỏ Qua (.gitignore) và Sửa Lịch Sử (Amend)

## 1. Mục Tiêu Bài Thực Hành
- **Cấu hình `.gitignore`**: Thiết lập để Git tự động bỏ qua các tệp tin chứa dữ liệu nhạy cảm (như mật khẩu, API keys) hoặc các file rác sinh ra bởi hệ điều hành / môi trường phát triển.
- **Gỡ bỏ file khỏi chỉ mục (Cache) Git an toàn**: Sử dụng lệnh `git rm --cached` để ngừng theo dõi tệp tin đã lỡ commit mà **không làm mất file vật lý** trên ổ cứng cục bộ.
- **Sửa đổi lịch sử commit với `--amend`**: Kết hợp các thay đổi mới và viết lại commit message cho commit gần nhất.

---

## 2. Bối Cảnh & Kịch Bản Giả Lập
1. **Lỗi ban đầu**: Trong quá trình khởi tạo dự án, vô tình thêm tệp `credentials.txt` chứa thông tin nhạy cảm vào Git và đã thực hiện commit:
   ```bash
   git add .
   git commit -m "feat: Initial commit with credentials"
   ```
2. **Yêu cầu khắc phục**:
   - Gỡ bỏ `credentials.txt` khỏi sự theo dõi của Git.
   - Giữ nguyên file `credentials.txt` trên thư mục làm việc (working directory).
   - Thêm `.gitignore` để chặn vĩnh viễn việc theo dõi `credentials.txt`.
   - Sửa đổi commit gần nhất để loại bỏ hoàn toàn dấu vết của `credentials.txt` và cập nhật thông điệp commit thành `feat: Initial commit without sensitive files`.

---

## 3. Các Bước Thực Hiện & Giải Thích Chi Tiết

### Bước 1: Tạo tệp cấu hình `.gitignore`
Tạo tệp `.gitignore` tại thư mục gốc của kho lưu trữ để thông báo cho Git bỏ qua file nhạy cảm:

```gitignore
# Sensitive and credentials files
credentials.txt
.env
*.key
*.pem

# OS & IDE junk files
.DS_Store
Thumbs.db
.vscode/
.idea/

# Logs
*.log
```

### Bước 2: Gỡ file khỏi bộ nhớ đệm (Cache) của Git
Sử dụng cờ `--cached` để chỉ gỡ tệp khỏi khu vực theo dõi (Index/Staging Area) của Git mà vẫn bảo toàn file vật lý:

```bash
git rm --cached credentials.txt
```

> **Giải thích cơ chế `git rm --cached`**:
> - Lệnh `git rm <file>` thông thường sẽ xóa cả file trong chỉ mục theo dõi của Git **và** xóa vật lý tệp trên ổ đĩa.
> - Lệnh `git rm --cached <file>` chỉ xóa đường dẫn của tệp khỏi khu vực theo dõi (Staging Index) của Git, chuyển trạng thái tệp thành `deleted` trong Staging, đồng thời giữ nguyên vẹn tệp vật lý tại Working Tree.

### Bước 3: Đưa tệp `.gitignore` vào Staging Area
```bash
git add .gitignore
```

Kiểm tra trạng thái trước khi amend:
```bash
git status
```
*Kết quả:*
```
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   .gitignore
	deleted:    credentials.txt
```

### Bước 4: Chỉnh sửa commit gần nhất bằng `git commit --amend`
Gộp thay đổi (xóa theo dõi `credentials.txt` và thêm `.gitignore`) vào commit trước đó, đồng thời thay đổi thông điệp commit:

```bash
git commit --amend -m "feat: Initial commit without sensitive files"
```

---

## 4. Kết Quả Kiểm Tra (Verification)

### 4.1. Kiểm tra trạng thái Git (`git status`)
```bash
$ git status
On branch master
nothing to commit, working tree clean
```
*Nhận xét:* Tệp `credentials.txt` không còn xuất hiện trong danh sách theo dõi của Git (không ở dạng Staged hay Modified), và không bị hiện Untracked nhờ quy tắc trong `.gitignore`.

### 4.2. Kiểm tra sự tồn tại vật lý của tệp tin trên ổ đĩa
```powershell
$ Test-Path credentials.txt
True
```
*Nhận xét:* Tệp `credentials.txt` vẫn được giữ nguyên vẹn trên máy cục bộ, đáp ứng đúng ràng buộc bài toán.

### 4.3. Kiểm tra lịch sử commit (`git log -n 1`)
```bash
$ git log -n 1
commit 4809dabec58c2fe063f966e619d78e6a24ee0f54
Author: LLL <Zeikezan12345@gmail.com>
Date:   Fri Oct 2 15:53:35 2026 +0700

    feat: Initial commit without sensitive files
```

### 4.4. Kiểm tra các tệp tin trong commit (`git log --stat -n 1`)
```bash
$ git log --stat -n 1
commit 4809dabec58c2fe063f966e619d78e6a24ee0f54
Author: LLL <Zeikezan12345@gmail.com>
Date:   Fri Oct 2 15:53:35 2026 +0700

    feat: Initial commit without sensitive files

 .gitignore | 13 +++++++++++++
 app.js     |  3 +++
 2 files changed, 16 insertions(+)
```
*Nhận xét:* Commit mới nhất chỉ bao gồm `.gitignore` và `app.js`, hoàn toàn không lưu vết tệp `credentials.txt`.

---

## 5. Tổng Kết Lệnh Đã Sử Dụng

| Lệnh | Mục đích |
| :--- | :--- |
| `git rm --cached <file>` | Gỡ bỏ tệp khỏi theo dõi của Git nhưng giữ lại file vật lý ở ổ cứng |
| `git add .gitignore` | Thêm cấu hình bỏ qua tệp vào Staging Area |
| `git commit --amend -m "..."` | Ghi đè commit gần nhất với các thay đổi mới và cập nhật message |
| `git status` | Kiểm tra trạng thái vùng làm việc (Working Tree) và chỉ mục (Staging) |
| `git log -n 1` | Xem thông tin commit mới nhất để xác thực lịch sử đã sửa đổi |
