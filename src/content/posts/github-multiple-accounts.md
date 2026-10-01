---
title: "Dùng nhiều GitHub account trên 1 máy: trade-off, SSH host alias và includeIf"
description: "Khi nào nên cân nhắc nhiều account và cách tách biệt bằng SSH host alias kết hợp includeIf, kèm các lỗi thường gặp."
pubDatetime: 2026-09-29T09:20:00+07:00
tags:
  - github
  - git
  - ssh
featured: false
draft: false
---

> [!Note] BLUF
>
> - Nhiều account là **nhu cầu tuỳ chọn**, có trade-off và rủi ro về điều khoản. Nếu chỉ cần ẩn nội dung, cân nhắc private repo hoặc Organization trước.
> - Mỗi account cần **1 SSH key riêng** và **1 host alias** trong `~/.ssh/config`.
> - Mỗi account có **1 thư mục gốc riêng**. Git tự chọn identity theo thư mục nhờ `includeIf`.
> - Clone bằng **host alias**, không dùng `github.com` trực tiếp.
>
> Note này thuộc loạt note về GitHub và giả định bạn đã làm xong [setup SSH và Git cho 1 account](/posts/github-ssh-git-setup/). Quy ước đặt tên repo nằm ở note [đặt tên repo và Public/Private](/posts/github-repo-naming/).

Placeholder: `<github-account-1>`, `<github-account-2>`, `<email-1>`, `<repo-name>`, `<org-1>`.

## 1. Khi nào nên cân nhắc

GitHub khuyến nghị mỗi người dùng 1 account cá nhân, và điều khoản dịch vụ hạn chế việc 1 người duy trì nhiều free account. Hãy đọc [GitHub Terms of Service](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service) trước khi quyết định.

Nhiều account chỉ hợp lý khi mục đích và mức độ riêng tư của các nhóm nội dung khác hẳn nhau. Đây là nhu cầu riêng của từng người, không phải yêu cầu của GitHub.

**Trade-off:**

| Được | Mất |
| --- | --- |
| Cô lập profile và quyền truy cập giữa các nhóm nội dung | Phải duy trì thêm SSH key, config và thao tác chuyển đổi |
| Tránh lẫn lộn nội dung công khai với nội dung nháp hoặc riêng tư | Dễ commit hoặc push nhầm identity nếu cấu hình sai |
| | Rủi ro về điều khoản dịch vụ |

**Phương án thay thế** thường đơn giản hơn: dùng **private repo** trên 1 account, hoặc tạo **Organization** để tách nhóm nội dung.

## 2. Nguyên tắc cốt lõi

- Mỗi account có **1 SSH key riêng** (GitHub không cho dùng chung 1 key cho nhiều account) và **1 host alias** trong `~/.ssh/config`.
- Mỗi account có **1 thư mục gốc riêng** trên máy. Git tự chọn identity theo thư mục nhờ `includeIf`.

Cấu trúc thư mục ví dụ:

```text
~/GitHub/
├── <github-account-1>/
│   ├── personal/
│   └── orgs/
│       └── <org-1>/
└── <github-account-2>/
    └── personal/
```

Tạo sẵn thư mục:

```bash
mkdir -p ~/GitHub/<github-account-1>/{personal,orgs/<org-1>} ~/GitHub/<github-account-2>/personal
```

## 3. Setup

### Bước 1: Tạo SSH key cho từng account

```bash
ssh-keygen -t ed25519 -C "<email-1>" -f ~/.ssh/id_ed25519_account_1
ssh-keygen -t ed25519 -C "<email-2>" -f ~/.ssh/id_ed25519_account_2
```

Add từng public key vào **đúng account** trên GitHub. Đăng nhập đúng account trước khi add để tránh gắn nhầm key.

### Bước 2: Host alias trong `~/.ssh/config`

Mỗi account một block, thay `github.com` bằng alias riêng:

```text
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
```

`IdentitiesOnly yes` rất quan trọng: nếu thiếu, ssh-agent có thể đưa nhầm key của account khác.

Kiểm tra từng alias, kết quả phải chào đúng username:

```bash
ssh -T git@github-account-1
ssh -T git@github-account-2
```

### Bước 3: Identity riêng cho từng thư mục bằng `includeIf`

Tạo file identity riêng, ví dụ `~/.gitconfig-account-1`:

```ini
[user]
    name = <github-account-1>
    email = <email-1>
```

Làm tương tự cho các account còn lại, rồi trỏ tới các file đó từ `~/.gitconfig` chính:

```ini
[includeIf "gitdir:~/GitHub/<github-account-1>/"]
    path = ~/.gitconfig-account-1
[includeIf "gitdir:~/GitHub/<github-account-2>/"]
    path = ~/.gitconfig-account-2
```

Lưu ý:

- Đường dẫn `gitdir:` phải **kết thúc bằng dấu `/`** để áp dụng cho mọi repo bên trong.
- Thiết lập dùng chung như `push.autoSetupRemote` vẫn chỉ đặt ở `--global`, không đặt trong các file identity.

### Bước 4: Clone bằng host alias

Thay `github.com` trong URL bằng alias đã khai báo. Đây là điểm quyết định Git dùng đúng key:

```bash
cd ~/GitHub/<github-account-1>/personal
git clone git@github-account-1:<github-account-1>/<repo-name>.git

cd ~/GitHub/<github-account-1>/orgs/<org-1>
git clone git@github-account-1:<org-1>/<repo-name>.git
```

Với repo đã clone bằng `github.com`, đổi remote:

```bash
git remote set-url origin git@github-account-1:<github-account-1>/<repo-name>.git
```

### Bước 5: Kiểm tra

```bash
# Phải ra đúng email của account tương ứng
git -C ~/GitHub/<github-account-1>/personal/<repo-name> config user.email
```

Lặp lại với các account còn lại.

## 4. Lỗi thường gặp

| Triệu chứng | Nguyên nhân thường gặp | Cách xử lý |
| --- | --- | --- |
| Push bị từ chối hoặc đi nhầm account | Thiếu `IdentitiesOnly yes`, hoặc clone bằng `github.com` thay vì alias | Thêm `IdentitiesOnly yes`, đổi remote URL sang alias |
| Commit hiện sai email | Repo nằm ngoài thư mục có `includeIf`, hoặc thiếu `/` cuối `gitdir:` | Kiểm tra vị trí repo và đường dẫn trong `~/.gitconfig` |
| `Permission denied (publickey)` | Public key chưa add vào đúng account, hoặc key chưa load vào agent | `ssh -T git@<alias>` và `ssh-add -l` |

## Kết luận

Cốt lõi là **tách biệt bằng cấu hình thay vì thao tác thủ công**: host alias quyết định key nào được dùng, `includeIf` quyết định identity nào được ghi vào commit, và thư mục là điểm neo cho cả hai. Chỉ áp dụng khi có lý do rõ ràng và đã cân nhắc trade-off cùng điều khoản của GitHub.
