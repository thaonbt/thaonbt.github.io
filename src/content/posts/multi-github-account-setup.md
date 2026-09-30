---
title: "Setup nhiều GitHub account trên một máy: SSH, Git identity và quy ước đặt tên repo"
description: "Cách tách biệt nhiều GitHub account trên cùng một máy macOS bằng SSH host alias và includeIf, kèm quy ước đặt tên repo và cấu trúc thư mục local."
pubDatetime: 2026-09-30T09:08:38+07:00
tags:
  - github
featured: true
draft: false
---

> [!Note] BLUF
>
> - Mỗi account dùng **1 SSH key riêng** + **1 host alias** trong `~/.ssh/config`.
> - Mỗi account có **1 thư mục gốc riêng** trên máy. Git tự chọn identity (name/email) theo thư mục nhờ `includeIf`.
> - Repo đặt tên theo `{prefix}-{mô-tả}` bằng **kebab-case**. Prefix để phân loại top-level, thư mục local giữ cấu trúc phẳng.
> - Bật `push.autoSetupRemote` để không dính lỗi thiếu tracking branch.

> Note này dùng placeholder thay cho tên thật: `<github-account-1>`, `<github-account-2>`, `<github-account-3>` và `<email-1>`, `<email-2>`, `<email-3>`.

## 1. Khi nào nên dùng nhiều account

GitHub khuyến nghị mỗi người một account cá nhân. Điều khoản dịch vụ của GitHub cũng hạn chế việc một người duy trì nhiều free account, nên hãy đọc kỹ [GitHub Terms of Service](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service) trước khi áp dụng.

Nhiều account chỉ hợp lý khi mục đích và mức độ riêng tư của từng nhóm nội dung khác hẳn nhau. Ví dụ:

| Account | Mục đích | Ghi chú |
| --- | --- | --- |
| `<github-account-1>` | Công việc, portfolio công khai | Dùng chung cho các Organization |
| `<github-account-2>` | Nội dung nhạy cảm hoặc riêng tư | Không liên kết với profile công khai |
| `<github-account-3>` | Học tập, thử nghiệm | Tránh làm loãng profile chính bằng code nháp |

**Trade-off:** tách account giúp cô lập profile và quyền truy cập, nhưng đổi lại phải duy trì thêm SSH key, config và thao tác chuyển đổi. Nếu chỉ cần ẩn nội dung, private repo trên 1 account hoặc 1 Organization thường đơn giản hơn.

## 2. Quy ước đặt tên repo

Format: `{prefix}-{tên-mô-tả-nội-dung}`, toàn bộ **kebab-case** (chữ thường, ngăn cách bằng dấu gạch ngang, không dùng underscore hay khoảng trắng).

### Prefix top-level

| Prefix | Ý nghĩa |
| --- | --- |
| `dev-` | Repo lập trình hoặc công cụ |
| `web-` | Repo website |
| `kb-` | Repo knowledge base hoặc ghi chú |

Prefix giúp repo tự nhóm lại khi sắp xếp alphabet, cả trên GitHub lẫn trong file explorer.

### Ngoại lệ hợp lý

| Loại | Cách đặt tên | Lý do |
| --- | --- | --- |
| Fork | Giữ nguyên hoặc gần tên gốc | GitHub đã hiển thị "forked from ..." nên không cần thêm prefix |
| Coursework | `coursework-{tên}-{nền-tảng}` | Cảnh báo đây là bài tập theo khoá học, không phải code gốc |
| Practice/scratch | `practice-{tên}` | Repo nháp, không cần gắn category cứng |
| Trùng tên với repo khác | `{prefix}-{username}-{tên}` | Tránh nhầm lẫn khi clone về local, xử lý theo từng trường hợp |
| GitHub Pages cá nhân | `{username}.github.io` | Bắt buộc khớp username để dùng domain mặc định |

### Nguyên tắc chọn công cụ phân loại

- Thông tin **loại trừ nhau** (repo chỉ thuộc đúng 1 nhóm) → dùng **prefix trong tên**.
- Thuộc tính **nhiều giá trị cùng lúc** (ngôn ngữ, framework) → về lý thuyết dùng GitHub Topics. Nếu tên repo đã đủ rõ và chưa cần filter theo công nghệ, có thể tạm bỏ qua để tránh phải đồng bộ thêm một lớp metadata.

## 3. Quy tắc Public/Private

| Public khi | Private khi |
| --- | --- |
| Dự án thật muốn trưng bày portfolio | Ghi chú hoặc dữ liệu cá nhân |
| Coursework có README giải thích rõ nguồn gốc | Logic có tính cạnh tranh |
| Demo kỹ năng, không chứa secret | Code nháp chưa đủ sạch |
| Fork thư viện dùng để học | Chứa API key hoặc config nhạy cảm chưa rà soát |

Khi public hoá coursework: **không commit tài liệu gốc** của khoá học (video, PDF, slide) vì vẫn thuộc bản quyền giảng viên hoặc nền tảng. Chỉ commit code tự viết.

## 4. Cấu trúc thư mục local

Mỗi account một thư mục gốc dưới `~/GitHub`. Bên trong giữ **cấu trúc phẳng**, không tách lại theo prefix vì thông tin đó đã có trong tên repo.

```text
~/GitHub/
├── <github-account-1>/
│   ├── personal/        # repo sở hữu cá nhân, cấu trúc phẳng
│   ├── orgs/            # repo thuộc Organization mà bạn là member
│   │   ├── <org-1>/
│   │   └── <org-2>/
│   └── company/         # dự phòng
├── <github-account-2>/
│   └── personal/
└── <github-account-3>/
    └── personal/
```

- `orgs/` giữ nguyên tên Organization như trên GitHub, không áp prefix.
- `orgs/` và `company/` nằm trong thư mục của account 1 nên tự động dùng chung identity với account đó.

Tạo sẵn thư mục:

```bash
mkdir -p ~/GitHub/<github-account-1>/{personal,orgs/<org-1>,orgs/<org-2>,company}
mkdir -p ~/GitHub/<github-account-2>/personal ~/GitHub/<github-account-3>/personal
```

## 5. Setup SSH và Git identity

### Bước 1: Tạo 3 SSH key riêng

GitHub không cho dùng chung 1 SSH key cho nhiều account, nên mỗi account cần 1 key. Nên đặt **passphrase** cho từng key.

```bash
ssh-keygen -t ed25519 -C "<email-1>" -f ~/.ssh/id_ed25519_account_1
ssh-keygen -t ed25519 -C "<email-2>" -f ~/.ssh/id_ed25519_account_2
ssh-keygen -t ed25519 -C "<email-3>" -f ~/.ssh/id_ed25519_account_3
```

### Bước 2: Add public key vào đúng account

Copy nội dung file `.pub` vào **Settings > SSH and GPG keys** của từng account:

```bash
pbcopy < ~/.ssh/id_ed25519_account_1.pub
```

Đăng nhập đúng account trước khi add, tránh gắn nhầm key.

### Bước 3: Khai báo host alias trong `~/.ssh/config`

```text
Host *
    AddKeysToAgent yes
    UseKeychain yes

Host github-account-1
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_account_1
    IdentitiesOnly yes

Host github-account-2
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_account_2
    IdentitiesOnly yes

Host github-account-3
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_account_3
    IdentitiesOnly yes
```

- `IdentitiesOnly yes` **rất quan trọng**: nếu thiếu, ssh-agent có thể đưa nhầm key của account khác và bạn sẽ bị xác thực sai account.
- Block `Host *` giúp macOS lưu passphrase vào Keychain. Chỉ cần nhập 1 lần:

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_account_1
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_account_2
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_account_3
```

Kiểm tra kết nối (kết quả phải chào đúng username của account tương ứng):

```bash
ssh -T git@github-account-1
```

### Bước 4: Tạo 3 file gitconfig riêng chứa identity

`~/.gitconfig-account-1`

```ini
[user]
    name = <github-account-1>
    email = <email-1>
```

`~/.gitconfig-account-2`

```ini
[user]
    name = <github-account-2>
    email = <email-2>
```

`~/.gitconfig-account-3`

```ini
[user]
    name = <github-account-3>
    email = <email-3>
```

Nếu không muốn lộ email thật trong commit history, dùng địa chỉ `noreply` mà GitHub cấp (Settings > Emails).

### Bước 5: Trỏ `includeIf` trong `~/.gitconfig` chính

```ini
[includeIf "gitdir:~/GitHub/<github-account-1>/"]
    path = ~/.gitconfig-account-1
[includeIf "gitdir:~/GitHub/<github-account-2>/"]
    path = ~/.gitconfig-account-2
[includeIf "gitdir:~/GitHub/<github-account-3>/"]
    path = ~/.gitconfig-account-3
```

**Lưu ý:** đường dẫn `gitdir:` phải **kết thúc bằng dấu `/`** để match mọi repo con bên trong.

### Bước 6: Bật tự động set upstream khi push branch mới

Yêu cầu **Git 2.37 trở lên**. Kiểm tra trước:

```bash
git --version
```

Nếu version thấp hơn, Git bỏ qua config này mà không báo lỗi, và lỗi `There is no tracking information for the current branch` vẫn xuất hiện, rất khó nhận ra nguyên nhân. Trên macOS có thể nâng cấp bằng `xcode-select --install` hoặc tải installer từ git-scm.com.

```bash
git config --global push.autoSetupRemote true
```

Chỉ đặt ở `--global`. Các file `~/.gitconfig-account-*` chỉ nên chứa identity.

### Bước 7: Kiểm tra

```bash
# Phải ra đúng email của account tương ứng
git -C ~/GitHub/<github-account-1>/personal/<repo-bat-ky> config user.email

# Phải ra: true
git config --global push.autoSetupRemote
```

Lặp lại với 2 account còn lại.

## 6. Clone repo bằng host alias

Thay `github.com` trong URL bằng **host alias** đã khai báo. Đây là điểm mấu chốt để Git dùng đúng key.

```bash
cd ~/GitHub/<github-account-1>/personal
git clone git@github-account-1:<github-account-1>/<repo-name>.git

cd ~/GitHub/<github-account-1>/orgs/<org-1>
git clone git@github-account-1:<org-1>/<repo-name>.git

cd ~/GitHub/<github-account-2>/personal
git clone git@github-account-2:<github-account-2>/<repo-name>.git
```

Với repo đã clone bằng URL `git@github.com:...`, đổi remote:

```bash
git remote set-url origin git@github-account-1:<github-account-1>/<repo-name>.git
```

## 7. Lỗi thường gặp (pitfalls)

| Triệu chứng | Nguyên nhân thường gặp | Cách xử lý |
| --- | --- | --- |
| Push bị `Permission denied` hoặc sai account | Thiếu `IdentitiesOnly yes`, hoặc clone bằng `github.com` thay vì host alias | Thêm `IdentitiesOnly yes`, đổi remote URL sang alias |
| Commit hiện sai email | Repo nằm ngoài thư mục có `includeIf`, hoặc thiếu `/` cuối `gitdir:` | Kiểm tra vị trí repo và đường dẫn trong `~/.gitconfig` |
| `There is no tracking information for the current branch` | Git dưới 2.37 nên `push.autoSetupRemote` bị bỏ qua | Nâng cấp Git rồi bật lại config |
| Bị hỏi passphrase liên tục | Chưa add key vào Keychain | Chạy `ssh-add --apple-use-keychain <key>` |

## 8. Bảo mật

- Đặt **passphrase** cho mọi private key, không commit thư mục `~/.ssh` hay file `.pub`/private key vào bất kỳ repo nào.
- Bật **2FA** (ưu tiên passkey hoặc authenticator app) cho cả 3 account.
- Rà secret trước khi public repo (API key, token, file `.env`). Có thể dùng thêm `gitleaks` hoặc GitHub secret scanning.
- Nếu nghi ngờ lộ key: xoá key khỏi Settings của account rồi tạo key mới.

## Kết luận

Cốt lõi của setup này là **tách biệt bằng cấu hình thay vì bằng thao tác thủ công**: SSH host alias quyết định key nào được dùng, `includeIf` quyết định identity nào được ghi vào commit, và cấu trúc thư mục là điểm neo cho cả hai. Khi mọi thứ gắn với thư mục, bạn chỉ cần `cd` đúng chỗ là Git tự làm đúng.
