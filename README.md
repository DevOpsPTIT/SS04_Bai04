# Báo Cáo Thực Hành: Quản Lý Tệp Bỏ Qua (.gitignore) Và Chỉnh Sửa Lịch Sử (Amend)

- **Bài tập**: Bài 4 - Quản lý tệp tin bỏ qua (.gitignore) và Sửa lịch sử (Amend) (Session 04 / Git Version Control)
- **Đường dẫn nộp bài**: `SS04/Bai04/README.md`
- **GitHub Remote Repository**: `https://github.com/DevOpsPTIT/SS04_Bai04.git`

---

## 1. Bối cảnh & Mục tiêu

### Bối cảnh
Trong quá trình lập trình và vận hành dự án, thành viên nhóm DevOps có thể vô tình đưa tệp tin chứa thông tin nhạy cảm (như `credentials.txt`, mật khẩu cơ sở dữ liệu, API Key hoặc tệp nhật ký `.log`) vào Staging Area và thực hiện commit lên Git. Việc gỡ bỏ tệp tin này khỏi hệ thống theo dõi của Git là cực kỳ cấp thiết, đồng thời **phải giữ lại tệp tin trên đĩa cứng cục bộ** để phục vụ việc cấu hình môi trường chạy máy chủ local, cấu hình `.gitignore` để tránh lặp lại sự cố và chỉnh sửa lại lịch sử commit (`--amend`) cho sạch sẽ.

### Mục tiêu
- Loại bỏ tệp `credentials.txt` khỏi Index/Cache của Git bằng lệnh `git rm --cached` mà **không xóa tệp tin vật lý**.
- Khởi tạo và cấu hình tệp `.gitignore` để tự động loại bỏ các tệp nhạy cảm và rác hệ thống.
- Chỉnh sửa thông điệp và nội dung của commit gần nhất thông qua tùy chọn `git commit --amend`.
- Trích xuất nhật ký `git status`, `git log -n 1` và `git ls-files` chứng minh sự cố đã được khắc phục hoàn toàn.

---

## 2. Thông Số Cấu Hình & Ràng Buộc

| Thông số / Cấu hình | Giá trị lựa chọn | Ghi chú / Ràng buộc |
| :--- | :--- | :--- |
| **Thư mục làm việc** | `SS04/Bai04` | Repository quản lý cục bộ |
| **Tệp tin nhạy cảm** | `credentials.txt` | **Bắt buộc giữ lại tệp vật lý** trên đĩa cứng |
| **Lệnh bỏ theo dõi** | `git rm --cached credentials.txt` | Gỡ khỏi Staging Area/Index, không dùng `git rm` |
| **Tệp tin cấu hình bỏ qua** | `.gitignore` | Chứa quy tắc bỏ qua `credentials.txt`, `*.env`, `*.log` |
| **Lệnh sửa lịch sử commit** | `git commit --amend` | Ghi đè commit cũ bằng commit sạch mới |
| **Remote Origin** | `https://github.com/DevOpsPTIT/SS04_Bai04.git` | Kho lưu trữ remote trên GitHub |

---

## 3. Các Bước Thực Hiện Chi Tiết

### Bước 1: Mô phỏng sự cố commit nhầm tệp nhạy cảm

Tạo tệp mã nguồn `index.html` và tệp chứa thông tin bảo mật `credentials.txt`:

```bash
# Tạo tệp credentials.txt chứa thông tin đăng nhập
echo "DB_PASSWORD=SuperSecretPassWord2026!" > credentials.txt

# Vô tình add và commit cả tệp nhạy cảm
git add .
git commit -m "feat: initial commit with credentials.txt included by mistake"
```

---

### Bước 2: Loại bỏ tệp khỏi Cache của Git bằng `git rm --cached`

Sử dụng cờ `--cached` để xóa tệp khỏi chỉ mục theo dõi (Index) của Git nhưng vẫn bảo toàn tệp vật lý tại thư mục làm việc:

```bash
git rm --cached credentials.txt
```

*Kết quả*: Git thông báo `rm 'credentials.txt'`, tệp chuyển sang trạng thái bị xóa trong Staging Area và trở thành `Untracked file`. Tệp `credentials.txt` vẫn nằm nguyên vẹn tại ổ đĩa.

---

### Bước 3: Cấu hình tệp tin bỏ qua `.gitignore`

Tạo tệp `.gitignore` tại thư mục gốc dự án và khai báo các quy tắc bỏ qua:

```gitignore
# Sensitive credentials & environment files
credentials.txt
*.env
*.pem
*.key

# Build output & dependencies
node_modules/
dist/
build/

# System & IDE temporary files
.DS_Store
Thumbs.db
*.log
```

Đưa tệp `.gitignore` vào Staging Area:

```bash
git add .gitignore
```

---

### Bước 4: Chỉnh sửa commit gần nhất bằng `git commit --amend`

Thực hiện lệnh `--amend` để gộp việc gỡ bỏ `credentials.txt` và thêm `.gitignore` vào trực tiếp commit ban đầu, đồng thời thay đổi thông điệp commit mới sạch sẽ hơn:

```bash
git commit --amend -m "feat: initial project setup with gitignore configured"
```

---

## 4. So Sánh `git rm --cached` vs `git rm`

| Tiêu chí | Lệnh `git rm --cached <file>` | Lệnh `git rm <file>` |
| :--- | :--- | :--- |
| **Tác động lên Git Index** | Xóa tệp khỏi Staging Area/Index (Ngừng theo dõi) | Xóa tệp khỏi Staging Area/Index (Ngừng theo dõi) |
| **Tác động lên Đĩa cứng (Disk)** | **Giữ nguyên tệp vật lý** tại thư mục làm việc | **Xóa vĩnh viễn tệp vật lý** khỏi ổ đĩa cứng |
| **Mục đích sử dụng** | Khi lỡ commit file cấu hình cá nhân/credential mà vẫn cần chạy ở local | Khi muốn xóa hẳn file rác/file cũ không còn dùng nữa |

---

## 5. Trích Xuất Log Terminal Chứng Minh (Verification Logs)

### 1. Kiểm tra trạng thái Git (`git status`)

```console
$ git status
On branch main
nothing to commit, working tree clean
```

> **Đánh giá**: Tệp `credentials.txt` không còn hiển thị ở dạng Modified hay Staged, cũng như không hiển thị ở Untracked nhờ đã được `.gitignore` ghi nhận.

---

### 2. Kiểm tra danh sách tệp tin do Git quản lý (`git ls-files`)

```console
$ git ls-files
.gitignore
index.html
```

> **Đánh giá**: Trong danh sách tệp được Git theo dõi chỉ còn `.gitignore` và `index.html`. Tệp `credentials.txt` đã bị loại bỏ hoàn toàn khỏi Git Index.

---

### 3. Kiểm tra sự tồn tại thực tế của tệp `credentials.txt` trên đĩa cứng

```console
$ Test-Path credentials.txt
True

$ Get-Content credentials.txt
DB_HOST=localhost
DB_PORT=5432
DB_USER=admin
DB_PASSWORD=SuperSecretPassWord2026!
API_KEY=sk_live_998877665544332211
```

> **Đánh giá**: Đạt 100% ràng buộc bài tập: Tệp `credentials.txt` vẫn tồn tại an toàn trên máy cục bộ.

---

### 4. Kiểm tra lịch sử commit mới nhất (`git log -n 1`)

```console
$ git log -n 1
commit 360140223fe9d52d3cdabf0f2e57490502c4dc55
Author: DevOps Student <devops.student@example.com>
Date:   Mon Oct 5 23:52:17 2026 +0700

    feat: initial project setup with gitignore configured
```

> **Đánh giá**: Thông điệp commit đã được cập nhật chuẩn xác qua lệnh `git commit --amend`, loại bỏ hoàn toàn vết tích commit nhầm file nhạy cảm.

---

## 6. Kết Luận

- Thực hiện thành công việc tháo gỡ tệp nhạy cảm `credentials.txt` khỏi sự theo dõi của Git bằng `git rm --cached`.
- Đảm bảo giữ lại tệp tin vật lý trên đĩa cứng phục vụ công việc local.
- Cấu hình tệp `.gitignore` chuẩn hóa ngăn chặn nguy cơ commit nhầm trong tương lai.
- Sử dụng thành thạo `git commit --amend` để làm sạch lịch sử commit trước khi đẩy lên remote.
- Đã đẩy kho lưu trữ hoàn chỉnh lên GitHub remote `https://github.com/DevOpsPTIT/SS04_Bai04.git`.
