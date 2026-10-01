---
title: "Setup SSH và Git cho GitHub trên macOS (1 account)"
description: "Các bước tạo SSH key, cấu hình Git identity, bật push.autoSetupRemote, kèm lưu ý bảo mật và lỗi thường gặp."
pubDatetime: 2026-09-29T09:10:00+07:00
tags:
  - github
  - git
  - ssh
  - security
featured: false
draft: false
---

## TL;DR

- Dùng **SSH key riêng** (`ed25519`, có passphrase) thay cho HTTPS + password.
- Đặt `user.name` và `user.email` trong Git config.
- Bật `push.autoSetupRemote` (cần **Git 2.37+**) để tránh lỗi thiếu tracking branch.
- Bật **2FA** và không bao giờ commit private key hoặc secret.

Note này thuộc loạt note về GitHub, cùng với [đặt tên repo và Public/Private](/posts/github-repo-naming/), [dùng nhiều account](/posts/github-multiple-accounts/) và [Conventional Commits và Semantic Versioning](/posts/conventional-commits-semver/).

## 1. Setup

Placeholder dùng trong note: `<github-username>`, `<email>`, `<repo-name>`.

### Bước 1: Tạo SSH key

`ed25519` là thuật toán được khuyến nghị hiện nay. Nên đặt **passphrase** cho key.

```bash
ssh-keygen -t ed25519 -C "<email>" -f ~/.ssh/id_ed25519_github
```

### Bước 2: Add public key vào GitHub

Copy nội dung file `.pub` rồi dán vào **Settings > SSH and GPG keys > New SSH key**.

```bash
pbcopy < ~/.ssh/id_ed25519_github.pub
```

### Bước 3: Khai báo trong `~/.ssh/config`

```text
Host *
    AddKeysToAgent yes
    UseKeychain yes

Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_github
    IdentitiesOnly yes
```

- `UseKeychain yes` là tuỳ chọn riêng của macOS, giúp lưu passphrase vào Keychain.
- `IdentitiesOnly yes` buộc SSH chỉ dùng đúng key đã khai báo, tránh việc ssh-agent đưa nhầm key khác.

Add key vào agent (chỉ cần làm 1 lần nếu key có passphrase):

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_github
```

Kiểm tra kết nối. Kết quả phải chào đúng username của bạn:

```bash
ssh -T git@github.com
```

### Bước 4: Cấu hình Git identity

```bash
git config --global user.name "<github-username>"
git config --global user.email "<email>"
```

Nếu không muốn lộ email thật trong commit history, dùng địa chỉ `noreply` do GitHub cấp (Settings > Emails).

### Bước 5: Bật tự động set upstream khi push branch mới

Yêu cầu **Git 2.37 trở lên**. Kiểm tra version trước:

```bash
git --version
```

Nếu version thấp hơn, Git bỏ qua config này mà không báo lỗi, và lỗi `There is no tracking information for the current branch` vẫn xuất hiện. Trên macOS có thể nâng cấp bằng `xcode-select --install` hoặc tải installer từ git-scm.com.

```bash
git config --global push.autoSetupRemote true
```

### Bước 6: Clone và kiểm tra

```bash
git clone git@github.com:<github-username>/<repo-name>.git
cd <repo-name>
git config user.email   # phải ra đúng email đã cấu hình
```

## 2. Bảo mật

- Đặt **passphrase** cho private key. Không commit thư mục `~/.ssh` hay bất kỳ private key nào vào repo.
- Bật **2FA** cho account GitHub (ưu tiên passkey hoặc authenticator app).
- Rà secret trước khi public repo: API key, token, file `.env`. Có thể dùng thêm `gitleaks` hoặc GitHub secret scanning.
- Secret đã lỡ commit vẫn nằm trong history: **revoke và tạo secret mới**, không chỉ xoá khỏi commit mới nhất.
- Nghi ngờ lộ SSH key: xoá key khỏi Settings của account rồi tạo key mới.

## 3. Lỗi thường gặp

| Triệu chứng | Nguyên nhân thường gặp | Cách xử lý |
| --- | --- | --- |
| `Permission denied (publickey)` | Chưa add public key vào GitHub, hoặc key chưa được load vào agent | Kiểm tra bằng `ssh -T git@github.com` và `ssh-add -l` |
| `There is no tracking information for the current branch` | Git dưới 2.37 nên `push.autoSetupRemote` bị bỏ qua | Nâng cấp Git rồi bật lại config |
| Bị hỏi passphrase liên tục | Chưa add key vào Keychain | `ssh-add --apple-use-keychain <key>` |
| Commit hiện sai email | Chưa cấu hình `user.email` hoặc config bị ghi đè ở cấp repo | `git config user.email` để xem giá trị đang áp dụng |

## Kết luận

Với 1 account, **SSH key riêng + Git identity + `push.autoSetupRemote`** là đủ để làm việc ổn định. Hai việc dễ bị bỏ sót nhất là passphrase cho key và rà secret trước khi push.
