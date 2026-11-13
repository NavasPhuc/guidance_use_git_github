# Hướng dẫn Git và GitHub qua một ví dụ thật

Tài liệu này giải thích **từng bước mình đã làm** để đưa ghi chép của cuộc thi lên GitHub: mỗi bước có mục đích, lệnh, ý nghĩa của lệnh và kết quả mong đợi. Có thêm phần giải thích khi nào cần token, phần dùng SSH thay token, và phần các lỗi đã gặp thật cùng cách sửa.

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
| **Token** | "Chìa khóa tạm" (một chuỗi ký tự, không phải đường link) để chứng minh bạn là chủ tài khoản khi push bằng địa chỉ HTTPS |
| **SSH** | Cách xác thực khác, dùng một **cặp khóa** thay cho token. Thiết lập một lần, dùng cho mọi repo |
| **Khóa công khai** (`.pub`) | "Ổ khóa": đưa cho GitHub, được phép chia sẻ |
| **Khóa riêng tư** | "Chìa khóa": ở lại trên máy bạn, **không bao giờ chia sẻ** |

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

> **Nếu bạn đã thiết lập SSH (xem phần 5):** dùng địa chỉ dạng SSH để khỏi phải làm Bước 9 (token). Repo mới: `git remote add origin git@github.com:NavasPhuc/zac2026-lifemate-notes.git`. Repo đã có `origin` dạng HTTPS: `git remote set-url origin git@github.com:NavasPhuc/zac2026-lifemate-notes.git`.

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

> **Nếu `origin` dùng địa chỉ SSH:** lệnh này **không hỏi** Username hay Password, bạn bỏ qua phần bảng hỏi ở trên.

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

### Cập nhật một file đã có trên GitHub

Khi bạn có bản mới của một file đã được đẩy lên, **không cần xóa hay upload lại**. Chỉ cần ghi đè file cũ trong thư mục repo bằng bản mới, rồi lặp lại nhịp `add`, `commit`, `push`. Git tự nhận ra file nào đã thay đổi, và bản cũ vẫn nằm trong lịch sử.

```bash
cd ~/zac2026-lifemate-notes
pwd
cp "$HOME/Downloads/huong-dan-git-github (1).md" docs/huong-dan-git-github.md
git status
git diff --stat
git add docs/huong-dan-git-github.md
git commit -m "Update Git guide: add token and SSH sections"
git push
```

| Điểm cần nhớ | Giải thích |
|---|---|
| `pwd` | Kiểm tra bạn đang đứng đúng thư mục repo, không phải thư mục thực hành hay thư mục khác |
| Tên file có khoảng trắng hoặc dấu `( )` phải đặt trong **ngoặc kép** | Nếu không, terminal báo `syntax error near unexpected token '('`. Mẹo: gõ vài chữ đầu của tên rồi nhấn Tab để terminal tự điền |
| Vì sao file tải về có chữ `(1)` | Trình duyệt thêm `(1)` khi tên trùng với file đã có trong `Downloads` |
| **Giữ nguyên tên cũ** trong repo | Để Git hiểu đây là cùng một file đã sửa. Đổi tên thì Git thấy là file mới |
| `cp` ghi đè **không hỏi** | Kiểm tra kỹ tên file đích trước khi chạy |
| `git status` phải ghi `modified:` | `modified` là file cũ có nội dung mới. `new file` là file mới hoàn toàn |
| `git diff --stat` | Tóm tắt số dòng thêm hoặc xóa trong mỗi file |

### Biết phiên bản nào là mới nhất

Git **không đánh số** "phiên bản 1, phiên bản 2". Thay vào đó mỗi commit có ba thứ nhận diện:

| Thứ | Ví dụ | Dùng để làm gì |
|---|---|---|
| Mã commit (hash) | `10ad462`, `5863e29` | Mã định danh duy nhất |
| Ghi chú | `Add README` | Bạn tự viết, cho biết mốc làm gì |
| Ngày giờ và tác giả | Git tự ghi | Biết mốc nào trước, mốc nào sau |

Cách xem lịch sử:

| Nơi | Cách làm |
|---|---|
| Trên máy | `git log --oneline` (mới nhất nằm **trên cùng**). Xem riêng một file: `git log --oneline -- docs/huong-dan-git-github.md` |
| Trên GitHub | Mở repo → bấm vào file → nút **History** → bấm vào một commit để thấy dòng nào được thêm (xanh) hoặc xóa (đỏ) |
| Trước khi commit | `git diff` cho thấy dòng nào bạn vừa sửa so với commit gần nhất |

Cách "đặt tên phiên bản":
1. **Ghi chú commit rõ ràng** (cách đơn giản nhất), ví dụ `Update Git guide: add token and SSH sections`.
2. **Dùng tag** nếu muốn gắn nhãn cho các cột mốc lớn:

```bash
git tag v2-git-guide
git push origin v2-git-guide
```

Lưu ý về tag:
- Tag cần ít nhất một commit để gắn vào. Chạy `git tag` để xem danh sách tag đã có.
- `git push origin <tên-tag>` chỉ đẩy **cái nhãn**, không cập nhật nhánh `main` trên GitHub. Hãy `git push` các commit trước, rồi mới đẩy tag.

---

## 4. Khi nào cần token, và token dùng cho những repo nào

Token **không phải** thứ phải tạo cho mỗi dự án. Nó chỉ cần ở một vài tình huống:

| Tình huống | Cần token không |
|---|---|
| Chỉ làm trên máy (`git init`, `git add`, `git commit`) | **Không**, vì chưa chạm đến GitHub |
| Đẩy lên GitHub bằng địa chỉ HTTPS (`git push`) | **Có** |
| Kéo (`git clone`) repo công khai của người khác | **Không** |
| Tạo repo mới trên trang web github.com | **Không**, vì bạn đang đăng nhập tài khoản |

### Một token dùng cho bao nhiêu repo?

Phụ thuộc vào phần **Repository access** lúc tạo token:

| Cách chọn | Token mở được | Ghi chú |
|---|---|---|
| Only select repositories | Chỉ các repo đã chọn | An toàn nhất. Có repo mới thì phải xử lý (xem dưới) |
| All repositories | Mọi repo hiện tại và tương lai | Tiện, nhưng nếu lộ thì ảnh hưởng mọi repo |

Khi tạo **repo mới** mà token cũ chỉ chọn repo cũ, bạn có hai cách:
1. **Thêm repo mới vào token cũ:** Settings → Developer settings → Fine-grained tokens → bấm tên token → mục **Repository access** → thêm repo mới → lưu. Chuỗi token **không đổi**, nên bạn dùng lại chuỗi đã lưu. Chỉ dùng được khi bạn còn giữ chuỗi token và token chưa hết hạn.
2. **Tạo token mới** cho repo mới.

Token còn **hết hạn** (ví dụ 30 ngày), nên đến hạn thì dù cùng dự án vẫn phải tạo token mới. Muốn khỏi phải làm lại nhiều lần, dùng SSH (phần 5).

---

## 5. Dùng SSH thay token (thiết lập một lần)

### Ý tưởng: ổ khóa và chìa khóa

Bạn tạo một **cặp** khóa:

| Khóa | Giống như | Để ở đâu | Có chia sẻ được không |
|---|---|---|---|
| Khóa công khai (`.pub`) | Ổ khóa | Đưa cho GitHub | Được |
| Khóa riêng tư | Chìa khóa | Ở lại trên laptop | **Không bao giờ** |

Bạn gắn "ổ khóa" vào tài khoản GitHub. Khi push, laptop dùng "chìa khóa" để chứng minh nó khớp với ổ khóa đó. Nhờ vậy không phải nhập token hay mật khẩu mỗi lần. Khóa riêng tư **không rời khỏi máy** và không tự hết hạn sau 30 ngày.

Các bước 5.1 đến 5.4 chỉ cần làm **một lần cho cả máy**. Bước 5.5 làm cho từng repo.

### Bước 5.1: Kiểm tra máy đã có khóa SSH chưa

**Mục đích:** tránh tạo khóa mới **đè lên** khóa cũ (làm mất quyền truy cập vào những nơi đang dùng khóa cũ). Bước này chỉ đọc.

```bash
ssh -V
ls -al ~/.ssh
```

| Kết quả | Nghĩa là |
|---|---|
| `OpenSSH_8.9...` hoặc tương tự | SSH đã cài (bình thường trên Ubuntu) |
| `No such file or directory` hoặc thư mục `~/.ssh` trống | Chưa có khóa, an toàn để tạo mới |
| Có file dạng `id_ed25519` và `id_ed25519.pub` (hoặc `id_rsa` và `id_rsa.pub`) | **Đã có khóa**, đừng tạo đè. Dùng lại hoặc tạo khóa với tên khác |

### Bước 5.2: Tạo cặp khóa

```bash
ssh-keygen -t ed25519 -C "laptop-vivobook"
```

| Phần | Nghĩa |
|---|---|
| `-t ed25519` | Loại thuật toán hiện đại, ngắn và an toàn, GitHub khuyên dùng |
| `-C "laptop-vivobook"` | Nhãn để sau này nhận ra khóa thuộc máy nào. Đặt tên máy thay vì email để không đưa email vào khóa |

Terminal hỏi hai câu:
1. `Enter file in which to save the key (...)`: nhấn **Enter** để dùng vị trí mặc định.
2. `Enter passphrase (empty for no passphrase)`: **passphrase** là mật khẩu bảo vệ khóa riêng tư.

| Lựa chọn passphrase | Ưu điểm | Nhược điểm |
|---|---|---|
| Đặt passphrase | Ai lấy được file khóa riêng tư vẫn chưa dùng được | Có thể phải nhập khi dùng |
| Bỏ trống (Enter hai lần) | Không phải nhập gì | Ai chép được file khóa là dùng được ngay |

Muốn thêm passphrase cho khóa đã tạo, dùng `ssh-keygen -p` (không cần tạo khóa mới). Khi gõ passphrase, màn hình không hiện ký tự nào.

**Kết quả:** terminal in đường dẫn hai file, một dòng `SHA256:...` (dấu vân tay của khóa) và một khung hình vuông toàn ký tự. Kiểm tra bằng:

```bash
ls -l ~/.ssh
```

Sẽ có hai file: `id_ed25519` (khóa **riêng tư**, quyền `-rw-------`) và `id_ed25519.pub` (khóa **công khai**, đuôi `.pub`).

> **Không bao giờ** gửi, mở ra copy, hoặc để trong thư mục repo file `id_ed25519` (không có đuôi). Chỉ file `.pub` mới được chia sẻ.

### Bước 5.3: Đưa khóa công khai lên GitHub

```bash
cat ~/.ssh/id_ed25519.pub
```

Lệnh in ra **một dòng** dạng `ssh-ed25519 AAAAC3Nz... laptop-vivobook`. Bôi đen cả dòng, copy (`Ctrl + Shift + C` trong terminal Ubuntu).

Trên github.com: ảnh đại diện → **Settings** → **SSH and GPG keys** → **New SSH key**, rồi điền:

| Ô | Điền |
|---|---|
| Title | `laptop-vivobook` (tên để bạn nhận ra) |
| Key type | Authentication Key (giữ mặc định) |
| Key | Dán dòng vừa copy |

Bấm **Add SSH key** (GitHub có thể hỏi xác nhận mật khẩu hoặc mã hai bước). Dấu vân tay `SHA256:...` hiện trên GitHub phải **trùng** với dòng terminal in lúc tạo khóa.

### Bước 5.4: Kiểm tra kết nối từ laptop đến GitHub

```bash
ssh -T git@github.com
```

- `-T` chỉ kiểm tra kết nối, không mở điều khiển từ xa.
- `git@github.com`: `git` là tên tài khoản đặc biệt mà GitHub dùng để nhận kết nối Git, **không phải tên của bạn**.

Lần đầu tiên terminal hỏi:

```
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Nghĩa là: "Đây là lần đầu máy bạn nói chuyện với github.com, có tin đúng là GitHub không?" (để chống kẻ mạo danh). Bạn đối chiếu dấu vân tay hiện ra với dấu vân tay ED25519 mà GitHub công bố:

```
SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU
```

Nếu khớp thì gõ **`yes`**. Nếu **không khớp**, nhấn `Ctrl + C` và không gõ `yes`. Có thể đối chiếu thêm trong tài liệu của GitHub về "SSH key fingerprints".

**Kết quả thành công:**

```
Hi <tên tài khoản của bạn>! You've successfully authenticated, but GitHub does not provide shell access.
```

Câu có chữ "but" nghe như có vấn đề nhưng thật ra là **thành công**: GitHub nhận ra bạn nhờ khóa SSH, và nhắc rằng nó chỉ dùng kết nối này để nhận code, không cho điều khiển từ xa.

### Bước 5.5: Đổi repo sang địa chỉ SSH rồi thử push

Làm trong từng repo muốn dùng SSH:

```bash
cd ~/zac2026-lifemate-notes
git remote set-url origin git@github.com:NavasPhuc/zac2026-lifemate-notes.git
git remote -v
git push
```

| Lệnh | Việc nó làm |
|---|---|
| `git remote set-url origin <địa chỉ mới>` | **Đổi** địa chỉ của `origin` đã có (không phải thêm mới) |
| `git remote -v` | Hiện lại địa chỉ để kiểm tra |
| `git push` | Thử đẩy. Nếu chưa có commit mới, lệnh chỉ kiểm tra kết nối |

Hai dạng địa chỉ:

| Dạng | Ví dụ | Xác thực bằng |
|---|---|---|
| HTTPS | `https://github.com/NavasPhuc/zac2026-lifemate-notes.git` | Username và token |
| SSH | `git@github.com:NavasPhuc/zac2026-lifemate-notes.git` | Khóa SSH |

Dạng SSH dùng dấu **`:`** sau `github.com`, không phải dấu `/`.

**Kết quả mong đợi:** `git push` **không hỏi** Username hay Password, và in `Everything up-to-date` (kết nối thành công, chưa có commit mới để gửi). Để thử thật, sửa một chữ trong README rồi chạy cả nhịp `git add`, `git commit`, `git push`.

### Khi dùng SSH: lệnh nào đổi, lệnh nào giữ nguyên

Chỉ những lệnh **nói chuyện với GitHub** (đẩy, kéo, tải repo) bị ảnh hưởng bởi việc chuyển sang SSH. Các lệnh chỉ làm việc trên máy bạn (`git add`, `git commit`, `git status`, `git log`, `git diff`, `git tag`) **hoàn toàn không đổi**.

| Việc | Lệnh | Khác gì so với HTTPS |
|---|---|---|
| Khai báo địa chỉ cho repo **mới** | `git remote add origin git@github.com:TEN_TAI_KHOAN/TEN_REPO.git` | Chỉ khác dạng địa chỉ |
| Đổi repo **đã có** từ HTTPS sang SSH | `git remote set-url origin git@github.com:TEN_TAI_KHOAN/TEN_REPO.git` | Dùng `set-url` vì `origin` đã tồn tại (dùng `add` sẽ báo lỗi `remote origin already exists`) |
| Kiểm tra đang dùng loại địa chỉ nào | `git remote -v` | `https://...` là dùng token, `git@github.com:...` là dùng SSH |
| Đẩy lần đầu | `git push -u origin main` | Giống, nhưng **không hỏi** Username và Password |
| Đẩy các lần sau | `git push` | Giống |
| Đẩy một tag | `git push origin TEN_TAG` | Giống |
| Tải repo về máy | `git clone git@github.com:TEN_TAI_KHOAN/TEN_REPO.git` | Chỉ khác dạng địa chỉ |
| Kiểm tra SSH hoạt động | `ssh -T git@github.com` | Chỉ có khi dùng SSH |

Nhớ: dạng SSH dùng dấu **`:`** sau `github.com`, dạng HTTPS dùng dấu `/`.

### Quy trình một repo mới hoàn chỉnh bằng SSH

So với các bước ở phần 2, **bỏ Bước 9 (tạo token)** và dùng địa chỉ SSH ở Bước 8:

**Trên web (github.com):** New repository → đặt tên → để **trống** các ô README, .gitignore, license → Create repository.

**Trên máy:**

```bash
mkdir -p ~/ten-repo
cd ~/ten-repo
git init
# tạo .gitignore nếu thư mục có thể chứa thứ không được công khai
# thêm file của bạn vào thư mục
git status
git add ten-file
git commit -m "Initial commit"
git branch -M main
git remote add origin git@github.com:TEN_TAI_KHOAN/TEN_REPO.git
git push -u origin main
```

| Lệnh | Việc nó làm | Tương ứng bước |
|---|---|---|
| `git init` | Biến thư mục thành repo | Bước 1 |
| `git add`, `git commit` | Chọn file và lưu phiên bản | Bước 4 và 6 |
| `git branch -M main` | Đặt tên nhánh chính là `main` | Bước 8 |
| `git remote add origin <địa chỉ SSH>` | Khai báo nơi sẽ đẩy lên | Bước 8 |
| `git push -u origin main` | Đẩy lên, không hỏi token | Bước 10 |

Không cần tạo thêm khóa hay token nào cho mỗi repo mới. Khóa SSH đã đăng ký với tài khoản GitHub của bạn dùng được cho mọi repo của tài khoản đó.

> **Nếu `git config user.name` chưa được thiết lập trong repo mới:** làm lại Bước 5 trong thư mục đó (lệnh không có `--global` chỉ có tác dụng trong repo hiện tại).

### Dọn dẹp sau khi SSH hoạt động

Khi bạn đã push thành công bằng SSH, token cũ không còn cần thiết. Vào Settings → Developer settings → Fine-grained tokens → bấm **Delete**, và xóa các bản token lưu tạm ở ghi chú hay file nào đó.

### So sánh token và SSH

| | Token (HTTPS) | SSH |
|---|---|---|
| Thiết lập | Tạo trên web, mỗi lần hết hạn lại tạo | Một lần cho cả máy |
| Hết hạn | Có (bạn đặt) | Không tự hết hạn |
| Phải nhập khi push | Có (ô Password) | Không |
| Phạm vi | Chọn repo lúc tạo | Mọi repo của tài khoản |
| Rủi ro nếu bị lộ | Chỉ trong phạm vi và thời hạn của token | Ai có khóa riêng tư là dùng được mọi repo, nên phải giữ kín và nên đặt passphrase |

---

## 6. Các lỗi đã gặp thật và cách sửa

> Bảng dưới đây là bản tóm tắt nhanh. Phần giải thích đầy đủ từng lỗi (chuyện gì xảy ra, sai ở đâu, cách sửa, ý nghĩa của từng lệnh sửa, cách phòng tránh) nằm ở **phần 8**.

| Thông báo hoặc hiện tượng | Nguyên nhân | Cách sửa |
|---|---|---|
| `python: command not found` | Ubuntu chỉ có lệnh `python3` | Dùng `python3`, hoặc bật môi trường ảo (`source .venv/bin/activate`) thì `python` chạy được |
| Git lưu tên là `<PhucTran>` | Gõ cả dấu `< >` trong hướng dẫn | Chạy lại `git config user.name "PhucTran"` (không có `< >`) |
| `Invalid username or token. Password authentication is not supported for Git operations.` | Dán token vào ô **Username** thay vì ô Password | Nhập đúng tên tài khoản ở Username. Token đã lộ ra màn hình thì nên xóa và tạo mới |
| `remote: invalid credentials` / `Authentication failed` | Ô Password không nhận đúng token: dán không thành công, token sai, thiếu hoặc dính khoảng trắng, hoặc dùng nhầm token đã xóa | Tạo token mới, copy ngay và kiểm tra đã dán được. Có thể kiểm tra độ dài và phần đầu (`github_pat_`) bằng lệnh `read -rs` mà không làm lộ token |
| Không copy lại được token | Token chỉ hiện một lần | Xóa token đó và tạo token mới |
| Push bị từ chối dù token đúng | Token thiếu quyền **Contents: Read and write**, hoặc không được cấp cho đúng repo | Sửa quyền hoặc tạo token mới với đúng thiết lập |
| `fatal: not in a git directory` khi chạy `git config user.name ...` | Đang đứng **ngoài** repo. Lệnh không có `--global` cần ghi vào thư mục `.git` của repo hiện tại | `cd` vào thư mục repo (kiểm tra bằng `pwd`). Nếu thư mục chưa có `.git` thì chạy `git init` |
| `fatal: not a git repository (or any of the parent directories): .git` khi chạy `git status` | Thư mục chưa được `git init` (ví dụ thư mục thực hành mới tạo) | Chạy `git init` trong thư mục đó |
| `Permission denied (publickey)` khi dùng SSH | Khóa công khai chưa được thêm đúng vào GitHub, hoặc dán thiếu | Làm lại Bước 5.3, đối chiếu dấu vân tay, rồi chạy lại `ssh -T git@github.com` |
| `Authentication failed` hoặc `Invalid username or token` khi push, mà `git remote -v` cho thấy địa chỉ dạng `https://` | Địa chỉ HTTPS đòi token, bạn chưa có token còn hiệu lực cho repo đó | Đổi sang SSH: `git remote set-url origin git@github.com:TEN_TAI_KHOAN/TEN_REPO.git`, rồi push lại |
| `bash: syntax error near unexpected token '('` khi chạy `cp` | Tên file có khoảng trắng hoặc dấu ngoặc nhưng không đặt trong ngoặc kép | Đặt tên file trong ngoặc kép, hoặc gõ vài chữ đầu rồi nhấn Tab |
| `error: src refspec TEN_TAG does not match any` khi đẩy tag | Tag chưa được tạo trong thư mục này, hoặc chưa có commit nào | Chạy `git tag` để xem danh sách. Tạo commit trước, rồi `git tag TEN_TAG` |
| `fatal: remote origin already exists` | Dùng `git remote add` khi `origin` đã có | Dùng `git remote set-url origin <địa chỉ mới>` để đổi |

> **Mẹo nhận biết đang ở trong repo hay không:** chạy `git status`. Nếu in `On branch main` thì bạn đang ở trong repo. Nếu in `fatal: not a git repository` thì bạn đang ở ngoài.

### Các lỗi khác có thể gặp (chưa gặp trong ví dụ này)

| Thông báo | Nguyên nhân | Cách sửa |
|---|---|---|
| `Please tell me who you are` | Chưa khai báo tên và email | Chạy hai lệnh `git config user.name` và `git config user.email` |
| `Repository not found` | Sai địa chỉ repo, hoặc token không được cấp cho repo đó | Kiểm tra `git remote -v` và phạm vi của token |
| `Updates were rejected` | Repo trên GitHub có commit mà máy bạn không có (ví dụ GitHub tự tạo README lúc tạo repo) | Tránh bằng cách tạo repo trống. Nếu đã gặp thì cần kéo về (`git pull`) trước khi push |

---

## 7. Quy tắc an toàn cần nhớ

1. **Tạo `.gitignore` trước khi `git add`.** Đây là lưới an toàn cho dữ liệu không được công khai.
2. **Luôn chạy `git status` trước khi commit** để biết chính xác cái gì sắp được lưu.
3. **Repo công khai thì mọi thứ trong lịch sử ai cũng xem được**, kể cả file bạn đã xóa ở commit sau. Đã lỡ đẩy thứ nhạy cảm thì việc gỡ lại rất phức tạp, nên kiểm tra trước thay vì sửa sau.
4. **Tên và email trong commit là công khai.** Chọn email bạn chấp nhận để lộ, hoặc dùng email ẩn danh của GitHub.
5. **Token là bí mật.** Cấp quyền tối thiểu, chỉ một repo, đặt thời hạn ngắn, không chia sẻ, và xóa ngay nếu lộ.
6. **Dữ liệu và tài liệu của cuộc thi có điều khoản riêng.** Repo chỉ chứa code và ghi chép của riêng bạn, không chứa dữ liệu, âm thanh, file nhãn hay thư viện của ban tổ chức. Nên đọc điều khoản về việc công bố giải pháp và kết quả trước khi đăng.
7. **Khóa SSH riêng tư (file `id_ed25519`, không có đuôi `.pub`) là bí mật tuyệt đối.** Không gửi, không dán vào chat, không để trong thư mục repo. Chỉ file `.pub` mới được chia sẻ. Cân nhắc đặt passphrase để nếu file bị lấy thì vẫn chưa dùng được.
8. **Trước khi tạo khóa SSH mới, kiểm tra `~/.ssh`** để không ghi đè khóa đang dùng ở nơi khác.

---

## 8. Nhật ký lỗi chi tiết: sai ở đâu, cách sửa, ý nghĩa từng lệnh

Phần này kể lại các lỗi **đã xảy ra thật** trong quá trình thực hành. Với mỗi lỗi có: chuyện gì xảy ra, sai ở đâu, cách sửa, ý nghĩa từng lệnh sửa, và cách phòng tránh. Mắc lỗi khi học là chuyện bình thường, và đọc được thông báo lỗi là một kỹ năng đáng luyện.

### Tóm tắt

| # | Lỗi | Nguyên nhân gốc | Cách phòng tránh ngắn gọn |
|---|---|---|---|
| 1 | Tên lưu thành `<PhucTran>` | Gõ cả dấu `< >` của hướng dẫn | `< >` chỉ đánh dấu chỗ cần thay, không gõ |
| 2 | Token chọn nhầm quyền | Chọn "Repository creation" thay vì "Contents" | Quyền ghi nội dung là **Contents: Read and write** |
| 3 | Token dán vào ô Username | Hai ô nhập nằm liền nhau | Ô hiện chữ khi gõ là Username, ô im lặng là Password |
| 4 | Không copy được token, dán không được | Token chỉ hiện một lần, hoặc dán không thành công | Copy ngay, kiểm tra đã dán được trước khi rời trang |
| 5 | `not in a git directory` hoặc `not a git repository` | Đứng ngoài repo, hoặc thư mục chưa `git init` | `pwd` và `git status` trước khi làm |
| 6 | `syntax error near unexpected token '('` | Tên file có `( )` nhưng thiếu ngoặc kép | Đặt tên file trong ngoặc kép hoặc dùng phím Tab |
| 7 | Push repo thực hành bị đòi token | `origin` dùng địa chỉ HTTPS | Đổi `origin` sang địa chỉ SSH |
| 8 | Tag v3 không chứa nội dung mới | Gắn tag **trước** khi commit | Thứ tự: `add`, `commit`, `push`, rồi mới `tag` |
| 9 | Hiểu nhầm `nothing to commit, working tree clean` | Tưởng là lỗi | Đó chỉ là "không còn thay đổi nào chưa lưu" |
| 10 | Máy và GitHub lệch nhau về tag | Xóa tag ở một nơi (trang web) | Kiểm tra cả hai nơi: `git tag` và `git ls-remote --tags origin` |
| 11 | Chép nhầm phiên bản file | Trình duyệt đặt tên `(1)`, `(2)`... | Chọn file có số lớn nhất, kiểm tra bằng `grep -n "^## "` |

---

### Lỗi 1: Gõ cả dấu `< >` khi khai báo tên và email

**Chuyện gì xảy ra:** lệnh `git config user.name "<PhucTran>"` được gõ nguyên văn. Git lưu tên là `<PhucTran>` (có ngoặc nhọn), email cũng có ngoặc nhọn.

**Sai ở đâu:** trong hướng dẫn, dấu `< >` chỉ dùng để đánh dấu "thay phần này bằng thông tin của bạn". Nó không phải một phần của lệnh.

**Cách sửa:**

```bash
git config user.name "PhucTran"
git config user.email "email-cua-ban"
git config user.name
git config user.email
```

| Lệnh | Ý nghĩa |
|---|---|
| `git config user.name "..."` | **Ghi** tên tác giả vào cấu hình của repo hiện tại. Ghi lại lần nữa sẽ **đè** giá trị cũ |
| `git config user.email "..."` | Tương tự cho email |
| `git config user.name` (không có giá trị) | **Đọc** lại giá trị đang lưu để kiểm tra |

**Phòng tránh:** sau khi cấu hình, luôn đọc lại và nhìn xem có dấu `<` `>` nào còn sót không.

---

### Lỗi 2: Chọn nhầm quyền cho token

**Chuyện gì xảy ra:** ở phần Permissions của token, bạn thêm **Repository creation** (quyền tạo repo mới) thay vì quyền cần thiết.

**Sai ở đâu:** muốn `git push` thì token cần quyền **ghi nội dung** của repo, tên là **Contents**. Quyền Repository creation không cho phép đẩy code, và còn rộng hơn mức cần thiết.

**Cách sửa:**
1. Bấm dấu **X** bên phải dòng Repository creation để bỏ quyền này.
2. Bấm **Add permissions**, gõ `Contents` vào ô tìm kiếm, tick chọn.
3. Ở dòng Contents, đổi **Access** thành **Read and write**.
4. Kết quả đúng: Permissions có 2 dòng, Metadata (Read-only, tự có sẵn) và Contents (Read and write).

| Quyền | Ý nghĩa |
|---|---|
| Contents: Read and write | Đọc và **ghi file** trong repo, tức là cho phép `git push` |
| Metadata: Read-only | Đọc thông tin cơ bản của repo, bắt buộc và tự có sẵn |

**Phòng tránh:** theo nguyên tắc "quyền tối thiểu": chỉ cấp đúng quyền cần dùng, và chỉ chọn đúng repo cần thiết ("Only select repositories").

---

### Lỗi 3: Dán token vào ô Username

**Chuyện gì xảy ra:** khi `git push`, bạn dán token vào ô `Username`, và ô Password để trống. GitHub trả về `Invalid username or token. Password authentication is not supported for Git operations.` Token còn hiện rõ trên màn hình (và nếu chụp hay gửi đi đâu đó thì đã bị lộ).

**Sai ở đâu:** hai ô nhập nằm liền nhau. Username là **tên tài khoản** (ví dụ `NavasPhuc`), còn token dán ở ô **Password**.

**Cách sửa:**
1. **Coi token đó là đã lộ**: vào Settings → Developer settings → Fine-grained tokens, bấm **Delete** token đó.
2. Tạo token mới (hoặc chuyển sang SSH, xem phần 5).
3. Push lại, nhập đúng: Username là `NavasPhuc`, Password là token.

| Việc | Ý nghĩa |
|---|---|
| Xóa token đã lộ | Giống mất chìa khóa nhà thì thay ổ khóa: token cũ hết hiệu lực ngay |
| Nhập token ở ô Password | Đúng chỗ GitHub đọc "mật khẩu" |

**Phòng tránh:** ô **hiện chữ khi gõ** là Username, ô **im lặng không hiện ký tự nào** mới là chỗ dán token. Nếu dán token mà thấy chữ hiện lên thì dừng lại, nhấn `Ctrl + C`.

---

### Lỗi 4: Không copy được token, hoặc dán không thành công

**Chuyện gì xảy ra:** token chưa được copy kịp, hoặc đã rời trang mà không lưu. Lần khác, push báo `remote: invalid credentials` vì ô Password nhận chuỗi sai hoặc rỗng.

**Sai ở đâu:** token **chỉ hiện đúng một lần** lúc tạo xong, sau đó không xem lại được. Ngoài ra ô Password không hiện ký tự nên không biết dán có thành công không.

**Cách sửa:**
1. Xóa token cũ (nút **Delete**), tạo token mới với cùng thiết lập.
2. Copy ngay, mở Text Editor, dán thử để chắc chắn đã copy được, rồi mới rời trang GitHub.
3. Có thể kiểm tra chuỗi đã dán vào terminal đúng chưa mà không làm lộ token:

```bash
read -rs -p "Dán token rồi Enter: " T; echo
echo "Độ dài: ${#T}"
echo "11 ký tự đầu: ${T:0:11}"
case "$T" in *[[:space:]]*) echo "Có khoảng trắng: CÓ";; *) echo "Có khoảng trắng: KHÔNG";; esac
unset T
```

| Phần | Ý nghĩa |
|---|---|
| `read -rs` | Nhận chuỗi bạn dán **mà không hiện ra màn hình** |
| `${#T}` | Độ dài chuỗi. Token loại này thường khoảng 93 ký tự. Độ dài 0 nghĩa là dán chưa thành công |
| `${T:0:11}` | 11 ký tự đầu, phải là `github_pat_` |
| `case ... *[[:space:]]*` | Kiểm tra có dấu cách dính ở đầu hoặc cuối không |
| `unset T` | Xóa chuỗi khỏi bộ nhớ terminal |

**Phòng tránh:** trong terminal Ubuntu, dán bằng `Ctrl + Shift + V` hoặc chuột phải → Paste (`Ctrl + V` không dùng được). Hoặc dùng SSH để khỏi phải xử lý token.

---

### Lỗi 5: Chạy lệnh Git khi không đứng trong repo

**Chuyện gì xảy ra:** hai thông báo khác nhau cùng một nguyên nhân:
- `fatal: not in a git directory` khi chạy `git config user.name ...`
- `fatal: not a git repository (or any of the parent directories): .git` khi chạy `git status` trong thư mục thực hành.

**Sai ở đâu:** Git chỉ làm việc trong thư mục đã được `git init` (có thư mục ẩn `.git`). Lệnh `git config` không có `--global` cần ghi vào `.git` của repo hiện tại, nên ngoài repo thì không có chỗ để ghi.

**Cách sửa:**

```bash
pwd
cd ~/zac2026-lifemate-notes
git status
```

Nếu thư mục chưa phải repo (ví dụ thư mục thực hành mới tạo), chạy:

```bash
git init
git status
```

| Lệnh | Ý nghĩa |
|---|---|
| `pwd` | In ra thư mục bạn đang đứng, để biết mình ở đâu |
| `cd <thư mục>` | Đi vào thư mục repo |
| `git init` | Biến thư mục hiện tại thành repo (tạo `.git`) |
| `git status` | Nếu in `On branch ...` thì bạn đang ở trong repo; nếu in `fatal: not a git repository` thì bạn đang ở ngoài |

**Phòng tránh:** đầu mỗi phiên làm việc, chạy `pwd` và `git status`. Ngoài ra nên đặt dòng nhắc lệnh để hiển thị tên thư mục (mặc định Ubuntu đã hiện, ví dụ `~/zac2026-lifemate-notes$`).

---

### Lỗi 6: Tên file có dấu ngoặc, thiếu ngoặc kép (và đứng nhầm thư mục)

**Chuyện gì xảy ra:** lệnh `cp ~/Downloads/huong-dan-git-github (1).md docs/huong-dan-git-github.md` báo `bash: syntax error near unexpected token '('`. Lúc đó bạn cũng đang đứng ở thư mục thực hành, không phải repo thật.

**Sai ở đâu:** trong terminal, khoảng trắng và dấu `( )` có ý nghĩa đặc biệt. Nếu không đặt tên file trong ngoặc kép, terminal hiểu sai.

**Cách sửa:**

```bash
cd ~/zac2026-lifemate-notes
pwd
cp "$HOME/Downloads/huong-dan-git-github (1).md" docs/huong-dan-git-github.md
git status
git diff --stat
```

| Lệnh | Ý nghĩa |
|---|---|
| `"..."` (ngoặc kép) | Báo cho terminal: "mọi thứ bên trong là **một cái tên**" |
| `$HOME` | Đường dẫn thư mục cá nhân của bạn, an toàn khi nằm trong ngoặc kép |
| `cp A B` | Sao chép A đè lên B, **ghi đè không hỏi** |
| `git status` | Phải ghi `modified:` (file cũ có nội dung mới) |
| `git diff --stat` | Tóm tắt số dòng thêm và xóa |

**Phòng tránh:** gõ vài chữ đầu của tên file rồi nhấn **Tab** để terminal tự điền và tự xử lý ký tự đặc biệt. Luôn `pwd` trước khi chép file vào repo.

---

### Lỗi 7: Push repo thực hành bị đòi token

**Chuyện gì xảy ra:** `git push origin v2-git-guide` hỏi Username và Password rồi báo `Authentication failed`.

**Sai ở đâu:** `origin` của repo thực hành dùng địa chỉ **HTTPS** (`https://github.com/...`), loại này luôn đòi token. Bạn chưa có token còn hiệu lực cho repo đó.

**Cách sửa:** đổi `origin` sang địa chỉ **SSH** (sau khi đã thiết lập SSH ở phần 5):

```bash
git remote -v
git remote set-url origin git@github.com:NavasPhuc/guidance_use_git_github.git
git remote -v
git push origin v2-git-guide
```

| Lệnh | Ý nghĩa |
|---|---|
| `git remote -v` | Xem địa chỉ `origin` hiện tại (`https://...` là dùng token, `git@github.com:...` là dùng SSH) |
| `git remote set-url origin <địa chỉ>` | **Đổi** địa chỉ của `origin` đã có. Dùng `add` sẽ báo `remote origin already exists` |
| `git push origin <tag>` | Đẩy tag lên GitHub |

**Phòng tránh:** với mọi repo mới, dùng địa chỉ SSH ngay từ đầu (`git remote add origin git@github.com:...`).

---

### Lỗi 8: Gắn tag trước khi commit

**Chuyện gì xảy ra:** bạn chép file mới vào (`git status` ghi `modified`), rồi chạy `git tag v3-git-guidance` và `git push origin v3-git-guidance`. Tag lên GitHub nhưng phiên bản đó **không chứa nội dung mới**. Dòng `Total 0 (delta 0)` lúc push là dấu hiệu: không có dữ liệu mới nào được gửi.

**Sai ở đâu:** tag là cái **nhãn dán lên một commit đã tồn tại**, không chứa nội dung. Khi chưa `commit`, nội dung mới chưa nằm trong commit nào, nên nhãn dán lên commit **cũ**. Ví dụ đời thường: viết xong tài liệu nhưng chưa bấm Lưu, rồi dán nhãn "bản 3" lên bản đã lưu lần trước.

**Thứ tự đúng:**

```
sửa file  →  git add  →  git commit  →  git push  →  git tag  →  git push (tag)
```

**Cách sửa:**

```bash
git add docs/huong-dan-git-github.md
git commit -m "Update Git guide: add token and SSH sections"
git push
git tag -d v3-git-guidance
git tag v3-git-guidance
git push origin v3-git-guidance
git log --oneline --decorate
```

(Nếu tag cũ đã đẩy lên GitHub thì thêm `git push origin --delete v3-git-guidance` trước khi tạo lại.)

| Lệnh | Ý nghĩa |
|---|---|
| `git add <file>` | Đưa file đã sửa vào khu vực chuẩn bị |
| `git commit -m "..."` | Lưu thành một phiên bản mới |
| `git push` | Gửi commit lên GitHub |
| `git tag -d <tag>` | Gỡ nhãn cũ **trên máy** (`-d` là delete) |
| `git push origin --delete <tag>` | Gỡ nhãn cũ **trên GitHub** |
| `git tag <tag>` | Dán nhãn lên commit **hiện tại** (commit mới nhất) |
| `git push origin <tag>` | Đẩy nhãn lên GitHub. Chỉ đẩy nhãn, **không** cập nhật nhánh `main` |
| `git log --oneline --decorate` | Xem nhãn đang nằm ở commit nào |

**Kết quả đúng** (nhãn nằm ở commit trên cùng):

```
2b08feb (HEAD -> main, tag: v3-git-guidance, origin/main) Update Git guide: add token and SSH sections
8aef37b (tag: v2-git-guide) Application git in project
```

**Lưu ý thực tế:** dời tag chỉ nên làm ở repo thực hành. Ở dự án thật mà người khác đã dùng tag đó, hãy **tạo tag mới** (ví dụ `v4`) thay vì sửa tag cũ.

---

### Lỗi 9: Hiểu nhầm `nothing to commit, working tree clean`

**Chuyện gì xảy ra:** `git commit` báo `nothing to commit, working tree clean`, bạn tưởng đây là lỗi.

**Sai ở đâu:** không có gì sai. Thông báo nghĩa là thư mục làm việc **giống hệt** phiên bản đã lưu gần nhất, không còn thay đổi nào chưa lưu. Trong ví dụ này, nguyên nhân là nội dung mới **đã được commit từ trước**, và lần chạy `git commit` này là lần thứ hai.

**Cách kiểm tra** (chỉ đọc, không thay đổi gì):

```bash
git log --oneline --decorate
git status
grep -n "^## " docs/huong-dan-git-github.md
```

| Lệnh | Ý nghĩa |
|---|---|
| `git log --oneline --decorate` | Xem các commit đã có và nhãn nằm ở đâu |
| `git status` | Xem còn thay đổi nào chưa lưu không |
| `grep -n "^## " <file>` | Liệt kê các tiêu đề mục lớn trong file để biết đang là bản nào (số mục và tên mục) |

**Phòng tránh:** thông báo của Git đọc kỹ thường tự nói rõ. Hãy đọc từng dòng thay vì chỉ nhìn dòng cuối.

---

### Lỗi 10: Máy và GitHub lệch nhau về tag

**Chuyện gì xảy ra:** bạn xóa tag `v3` trên **trang web GitHub**, nhưng tag vẫn còn trên máy. Hai nơi không khớp nhau.

**Sai ở đâu:** tag tồn tại **độc lập ở từng nơi** (máy và GitHub). Xóa ở nơi này không tự xóa ở nơi kia.

**Cách kiểm tra và sửa:**

```bash
git tag
git ls-remote --tags origin
git tag -d v3-git-guidance
git tag v3-git-guidance
git push origin v3-git-guidance
git log --oneline --decorate
git ls-remote --tags origin
```

| Lệnh | Ý nghĩa |
|---|---|
| `git tag` | Liệt kê tag **trên máy** |
| `git ls-remote --tags origin` | Liệt kê tag **trên GitHub** (kèm mã commit mà mỗi tag trỏ tới) |
| Các lệnh còn lại | Xóa nhãn cũ trên máy, dán lại lên commit đúng, đẩy lên GitHub |

**Cách đọc kết quả:** hai nhãn trỏ vào hai mã commit khác nhau nghĩa là chúng nằm ở hai phiên bản khác nhau. Nếu hai nơi trỏ cùng một mã thì hai nơi đang khớp nhau.

**Phòng tránh:** chọn một nơi thống nhất để thao tác với tag (khuyên dùng dòng lệnh) và luôn kiểm tra lại cả hai nơi sau khi sửa.

---

### Lỗi 11: Chép nhầm phiên bản của file

**Chuyện gì xảy ra:** file tải về có tên `huong-dan-git-github (1).md`, `(2)`... và không chắc bản nào mới nhất.

**Sai ở đâu:** khi tên trùng, trình duyệt thêm số `(n)` vào tên file để không đè lên file cũ. Số lớn nhất thường là bản tải về sau cùng. Bạn dễ chép nhầm bản cũ hơn vào repo.

**Cách sửa và kiểm tra:**

```bash
ls ~/Downloads | grep huong
grep -n "^## " "$HOME/Downloads/huong-dan-git-github (1).md"
grep -n "^## " docs/huong-dan-git-github.md
diff docs/huong-dan-git-github.md "$HOME/Downloads/huong-dan-git-github (1).md" | head -20
```

| Lệnh | Ý nghĩa |
|---|---|
| `ls ~/Downloads \| grep huong` | Liệt kê tất cả các bản đã tải để biết số `(n)` lớn nhất |
| `grep -n "^## " <file>` | Liệt kê các mục lớn của từng bản để so sánh |
| `diff A B` | Hiện các dòng khác nhau. Dòng bắt đầu bằng `>` là có ở file B mà A không có. Không in gì nghĩa là hai file giống hệt |

**Phòng tránh:** sau khi tải bản mới, đổi tên về tên chuẩn hoặc xóa các bản cũ trong Downloads. Sau khi chép vào repo, dùng `git diff --stat` để xem Git thấy bao nhiêu dòng thay đổi.

---

### Danh sách kiểm tra trước khi push

1. `pwd` — đang đứng đúng thư mục repo?
2. `git status` — chính xác những gì sắp được lưu? Có file nào không nên có không?
3. `git add <tên file>` — thêm từng file thay vì `git add .` khi repo có thể chứa thứ nhạy cảm.
4. `git commit -m "Mô tả ngắn việc đã làm"`.
5. `git push` — không hỏi gì (nếu dùng SSH).
6. Nếu cần tag: `git tag <tên>` **sau** commit, rồi `git push origin <tên>`.
7. `git log --oneline --decorate` — xem lại kết quả, và mở trang GitHub (F5) để kiểm tra bằng mắt.
