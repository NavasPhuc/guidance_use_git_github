# Hướng dẫn Git và GitHub qua một ví dụ thật

Tài liệu này giải thích **từng bước mình đã làm** để đưa ghi chép của cuộc thi lên GitHub: mỗi bước có mục đích, lệnh, ý nghĩa của lệnh và kết quả mong đợi. Cuối tài liệu có phần các lỗi đã gặp thật và cách sửa.

Ví dụ dùng xuyên suốt: thư mục `~/zac2026-lifemate-notes` và repo `NavasPhuc/zac2026-lifemate-notes`.

---

## 1. Bức tranh lớn

| Từ | Nghĩa dễ hiểu |
|---|---|
| **Git** | Công cụ chạy trên máy bạn, **lưu lại các phiên bản** của một thư mục, giống chức năng "lưu phiên bản" nhưng có ghi chú cho từng lần lưu |
| **GitHub** | Trang web **lưu bản sao của thư mục Git trên mạng**, để người khác xem được và để bạn có bản dự phòng |
| **Repository (repo)** | Một thư mục được Git theo dõi. Lịch sử các phiên bản nằm trong thư mục ẩn `.git` |
| **Commit** | Một phiên bản đã được lưu, kèm một dòng ghi chú |
| **Khu vực chuẩn bị (staging)** | "Cái thùng" bạn bỏ file vào trước khi đóng gói thành commit |
| **Nhánh (branch)** | Một dòng phát triển các phiên bản. Nhánh chính thường tên `main` |
| **Remote / `origin`** | Địa chỉ repo trên mạng. `origin` chỉ là tên gọi tắt, giống lưu số điện thoại vào danh bạ |
| **Push** | Gửi các commit từ máy lên GitHub |
| **Token** | "Chìa khóa tạm" để chứng minh bạn là chủ tài khoản khi push |

### Dòng chảy của một lần cập nhật

```
Thư mục làm việc  --git add-->  Khu vực chuẩn bị  --git commit-->  Lịch sử trên máy  --git push-->  GitHub
 (bạn sửa file)                 (chọn file sẽ lưu)                 (phiên bản + ghi chú)           (trên mạng)
```

Git tách thành nhiều nhịp như vậy để bạn **chọn** chính xác những gì sẽ được lưu, thay vì lưu mọi thứ một cách mù quáng. Điều này đặc biệt quan trọng khi thư mục có những thứ không được phép công khai (như dữ liệu cuộc thi).

---

## 2. Các bước đã làm

### Bước 1: Tạo thư mục riêng, khởi tạo Git, chặn dữ liệu

**Mục đích:** tạo một thư mục **sạch**, tách biệt với thư mục dữ liệu cuộc thi, để chỉ những gì bạn muốn mới có thể lên mạng. README của cuộc thi cấm chia sẻ bộ dữ liệu, nên nếu đăng thẳng thư mục dữ liệu lên GitHub công khai thì có thể bị loại.

```bash
git --version
mkdir -p ~/zac2026-lifemate-notes
cd ~/zac2026-lifemate-notes
git init
printf '.venv/\ntrain/\npublic_test/\nprivate_test/\n*.wav\n*.jsonl\n*.json\n' > .gitignore
cat .gitignore
git status
```

| Lệnh | Việc nó làm |
|---|---|
| `git --version` | Kiểm tra máy đã cài Git chưa |
| `mkdir -p <thư mục>` | Tạo thư mục (`-p` để không báo lỗi nếu đã có) |
| `cd <thư mục>` | Đi vào thư mục đó |
| `git init` | Biến thư mục thành repo Git (tạo thư mục ẩn `.git`) |
| `printf ... > .gitignore` | Tạo file `.gitignore` chứa danh sách thứ Git phải **bỏ qua** |
| `git status` | Xem trạng thái hiện tại (chỉ đọc) |

**File `.gitignore` là "lưới an toàn":** dù sau này bạn gõ nhầm `git add .`, những thứ khớp các dòng trong đó (thư mục dữ liệu, file `.wav`, `.json`, `.jsonl`) vẫn không bị đưa vào.

**Kết quả mong đợi:** `git status` ghi `No commits yet` và liệt kê `.gitignore` ở mục "Untracked files". "Untracked" chỉ có nghĩa là Git thấy file nhưng chưa được yêu cầu theo dõi, không phải lỗi.

### Bước 2: Sao chép code vào repo

**Mục đích:** đưa hai file code của riêng bạn (`view.py`, `stats.py`) vào repo. Hai file này chỉ chứa code, không chứa dữ liệu cuộc thi, nên an toàn để đăng.

```bash
mkdir -p scripts
cp ~/zaic_contest/view.py ~/zaic_contest/stats.py scripts/
ls scripts
```

- `cp` là **sao chép** (bản gốc vẫn còn). Khác với `mv` là **dời** (bản gốc biến mất).

### Bước 3: Thêm tài liệu

```bash
mkdir -p docs
cp ~/Downloads/giai-doan-1.md docs/
```

File `.md` (Markdown) là văn bản có định dạng nhẹ; GitHub tự hiển thị đẹp (tiêu đề, bảng, danh sách).

### Bước 4: Đưa file vào khu vực chuẩn bị và kiểm tra lần cuối

**Mục đích:** chọn file sẽ lưu, rồi **soi lại** xem có file nào không nên có trước khi lưu.

```bash
git add .
git status
```

- `git add .` thêm mọi thứ trong thư mục hiện tại vào khu vực chuẩn bị (trừ những thứ bị `.gitignore` chặn).
- `git status` lúc này liệt kê những file **sẽ được lưu** dưới mục `Changes to be committed`.

**Kết quả mong đợi:** đúng 4 file (`.gitignore`, `docs/giai-doan-1.md`, `scripts/stats.py`, `scripts/view.py`). Nếu thấy bất kỳ file dữ liệu nào, **dừng lại** và không commit.

> **Thói quen tốt:** `git add .` tiện nhưng thêm tất cả. Với repo có thể chứa thứ nhạy cảm, hãy thêm từng file (`git add README.md`) và luôn chạy `git status` trước khi commit.

### Bước 5: Khai báo "tên tác giả"

**Mục đích:** mỗi commit được ký bằng tên và email của người tạo. Nếu Git chưa biết bạn là ai, lệnh commit sẽ báo lỗi.

```bash
git config user.name "PhucTran"
git config user.email "ten-email-cua-ban@example.com"
git config user.name
git config user.email
```

- Không có chữ `--global`, nên thiết lập **chỉ áp dụng cho repo này**, không ảnh hưởng các dự án khác trên máy.
- Hai lệnh cuối đọc lại để kiểm tra.

> **Tên và email này hiện công khai** trên mọi commit của repo công khai. Nếu không muốn lộ email cá nhân, dùng email ẩn danh của GitHub (dạng `ID+tendangnhap@users.noreply.github.com`, lấy ở GitHub → Settings → Emails).

> **Lỗi đã gặp:** trong hướng dẫn, dấu `< >` chỉ để đánh dấu chỗ cần thay. Nếu gõ cả dấu `< >` vào lệnh thì Git sẽ lưu tên là `<PhucTran>` (có ngoặc nhọn). Phải gõ chỉ phần nội dung trong dấu ngoặc kép.

### Bước 6: Tạo commit đầu tiên

**Mục đích:** biến 4 file trong khu vực chuẩn bị thành **một phiên bản được lưu**, kèm ghi chú.

```bash
git commit -m "Add stage 1 notes and helper scripts"
git log --oneline
```

- `-m "..."` là dòng ghi chú. Ghi chú tốt ngắn gọn và nói rõ phiên bản này thêm hoặc sửa gì.
- `git log --oneline` hiện lịch sử, mỗi commit một dòng: một mã ngắn (ví dụ `10ad462`) và ghi chú.

### Bước 7: Tạo repo trống trên GitHub (làm trên web)

**Mục đích:** tạo "ngăn chứa" trống trên mạng để đẩy commit vào.

Trên github.com: bấm **+** → **New repository** → đặt tên (`zac2026-lifemate-notes`) → chọn Public hoặc Private → bấm **Create repository**.

> **Quan trọng:** để **trống** các ô "Add a README file", "Add .gitignore", "Choose a license". Máy bạn đã có lịch sử riêng; nếu GitHub tạo sẵn các file đó, hai bên sẽ có lịch sử khác nhau và lúc push sẽ bị báo xung đột.

> **Gợi ý:** tạo ở chế độ **Private** trước nếu bạn chưa chắc về việc công bố. Đổi sang Public sau chỉ mất vài cú nhấp chuột ở Settings, ngược lại thì phiền hơn.

### Bước 8: Nối repo trên máy với repo trên GitHub

**Mục đích:** hai nơi này chưa biết nhau. Bước này khai báo địa chỉ để lệnh push biết gửi đi đâu.

```bash
git branch -M main
git remote add origin https://github.com/NavasPhuc/zac2026-lifemate-notes.git
git remote -v
```

| Lệnh | Việc nó làm |
|---|---|
| `git branch -M main` | Đổi tên nhánh hiện tại thành `main` (GitHub mặc định dùng `main`, Git cũ dùng `master`) |
| `git remote add origin <địa chỉ>` | Lưu địa chỉ repo trên mạng với tên gọi tắt `origin` |
| `git remote -v` | Liệt kê các địa chỉ đã lưu để kiểm tra (chỉ đọc) |

Bước này **chưa gửi gì lên mạng**.

### Bước 9: Tạo token để chứng minh bạn là chủ tài khoản

**Mục đích:** GitHub **không còn nhận mật khẩu thường** khi dùng Git từ terminal. Thay vào đó dùng token: một chuỗi ký tự dài, là "chìa khóa tạm" bạn tự cấp quyền và có thể thu hồi bất kỳ lúc nào.

Trên github.com: ảnh đại diện → **Settings** → **Developer settings** → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**.

| Mục | Chọn gì | Vì sao |
|---|---|---|
| Token name | Tên dễ nhớ, ví dụ `laptop-zac2026` | Để nhận ra nó trong danh sách |
| Expiration | 30 ngày | Hết hạn thì tự vô hiệu, an toàn hơn |
| Repository access | **Only select repositories**, chọn đúng repo | Chìa khóa chỉ mở được đúng một repo, nếu lộ thì thiệt hại nhỏ |
| Permissions | **Contents: Read and write** (Metadata tự có sẵn) | Quyền ghi nội dung là quyền cho phép `git push` |

> **Lỗi đã gặp:** lúc đầu chọn nhầm quyền **Repository creation** (tạo repo mới) thay vì **Contents**. Với quyền đó, push sẽ bị từ chối. Cách thêm đúng: bấm **Add permissions**, gõ `Contents` vào ô tìm kiếm, tick chọn, rồi đổi **Access** thành **Read and write**.

**Quy tắc an toàn với token:**
- Token **chỉ hiện đúng một lần** ngay sau khi bấm Generate. Phải copy ngay và dán vào nơi riêng tư trước khi rời trang. Nếu lỡ đóng trang, xóa token đó và tạo cái mới.
- **Không gửi token cho bất kỳ ai**, không dán vào file nào trong repo, không đăng ảnh chụp màn hình có token.
- Nếu lỡ lộ (gửi vào chat, đăng lên mạng...), vào lại trang Fine-grained tokens, bấm **Delete** là chìa khóa hết hiệu lực ngay.

### Bước 10: Đẩy commit lên GitHub

```bash
git push -u origin main
```

- `git push` gửi commit lên mạng.
- `origin main` nghĩa là "đến `origin`, nhánh `main`".
- `-u` ghi nhớ cặp này, nên những lần sau chỉ cần gõ `git push`.

Terminal hỏi hai câu:

| Terminal hỏi | Điền |
|---|---|
| `Username for 'https://github.com':` | Tên tài khoản GitHub (ví dụ `NavasPhuc`). Ô này **hiện chữ** khi gõ |
| `Password for ...:` | **Dán token** (không phải mật khẩu GitHub). Ô này **không hiện ký tự nào**, đó là bình thường |

**Cách nhớ:** ô nào hiện chữ khi gõ là Username; ô nào im lặng mới là chỗ dán token. Trong terminal Ubuntu, dán bằng `Ctrl + Shift + V` hoặc chuột phải → Paste.

**Kết quả mong đợi:** vài dòng `Writing objects: 100%` và dòng cuối dạng `* [new branch] main -> main`.

### Bước 11: Thêm README và đẩy lần hai

**Mục đích:** GitHub tự hiển thị file `README.md` ở trang chủ repo, giúp người mở link hiểu ngay repo là gì. Đây cũng là nhịp làm việc bạn sẽ lặp lại mỗi khi có thay đổi mới:

```bash
cp ~/Downloads/README.md .
git status
git add README.md
git commit -m "Add README"
git push
```

Lần này chỉ cần `git push` (không cần `origin main`) vì đã dùng `-u` ở lần trước.

---

## 3. Nhịp làm việc từ giờ về sau

Mỗi khi bạn thêm hoặc sửa file:

```bash
cd ~/zac2026-lifemate-notes
# ... thêm hoặc sửa file ...
git status                      # xem đã thay đổi những gì
git add <tên file>              # chọn file sẽ lưu
git status                      # kiểm tra lại cái thùng
git commit -m "Mô tả ngắn"      # đóng gói thành phiên bản
git push                        # gửi lên GitHub
```

Ghi chú commit nên cho biết **việc đã làm**, ví dụ `Add stage 2 notes`, `Fix typo in README`.

---

## 4. Các lỗi đã gặp thật và cách sửa

| Thông báo hoặc hiện tượng | Nguyên nhân | Cách sửa |
|---|---|---|
| `python: command not found` | Ubuntu chỉ có lệnh `python3` | Dùng `python3`, hoặc bật môi trường ảo (`source .venv/bin/activate`) thì `python` chạy được |
| Git lưu tên là `<PhucTran>` | Gõ cả dấu `< >` trong hướng dẫn | Chạy lại `git config user.name "PhucTran"` (không có `< >`) |
| `Invalid username or token. Password authentication is not supported for Git operations.` | Dán token vào ô **Username** thay vì ô Password | Nhập đúng tên tài khoản ở Username. Token đã lộ ra màn hình thì nên xóa và tạo mới |
| `remote: invalid credentials` / `Authentication failed` | Ô Password không nhận đúng token: dán không thành công, token sai, thiếu hoặc dính khoảng trắng, hoặc dùng nhầm token đã xóa | Tạo token mới, copy ngay và kiểm tra đã dán được. Có thể kiểm tra độ dài và phần đầu (`github_pat_`) bằng lệnh `read -rs` mà không làm lộ token |
| Không copy lại được token | Token chỉ hiện một lần | Xóa token đó và tạo token mới |
| Push bị từ chối dù token đúng | Token thiếu quyền **Contents: Read and write**, hoặc không được cấp cho đúng repo | Sửa quyền hoặc tạo token mới với đúng thiết lập |

### Các lỗi khác có thể gặp (chưa gặp trong ví dụ này)

| Thông báo | Nguyên nhân | Cách sửa |
|---|---|---|
| `Please tell me who you are` | Chưa khai báo tên và email | Chạy hai lệnh `git config user.name` và `git config user.email` |
| `Repository not found` | Sai địa chỉ repo, hoặc token không được cấp cho repo đó | Kiểm tra `git remote -v` và phạm vi của token |
| `Updates were rejected` | Repo trên GitHub có commit mà máy bạn không có (ví dụ GitHub tự tạo README lúc tạo repo) | Tránh bằng cách tạo repo trống. Nếu đã gặp thì cần kéo về (`git pull`) trước khi push |

---

## 5. Quy tắc an toàn cần nhớ

1. **Tạo `.gitignore` trước khi `git add`.** Đây là lưới an toàn cho dữ liệu không được công khai.
2. **Luôn chạy `git status` trước khi commit** để biết chính xác cái gì sắp được lưu.
3. **Repo công khai thì mọi thứ trong lịch sử ai cũng xem được**, kể cả file bạn đã xóa ở commit sau. Đã lỡ đẩy thứ nhạy cảm thì việc gỡ lại rất phức tạp, nên kiểm tra trước thay vì sửa sau.
4. **Tên và email trong commit là công khai.** Chọn email bạn chấp nhận để lộ, hoặc dùng email ẩn danh của GitHub.
5. **Token là bí mật.** Cấp quyền tối thiểu, chỉ một repo, đặt thời hạn ngắn, không chia sẻ, và xóa ngay nếu lộ.
6. **Dữ liệu và tài liệu của cuộc thi có điều khoản riêng.** Repo chỉ chứa code và ghi chép của riêng bạn, không chứa dữ liệu, âm thanh, file nhãn hay thư viện của ban tổ chức. Nên đọc điều khoản về việc công bố giải pháp và kết quả trước khi đăng.
