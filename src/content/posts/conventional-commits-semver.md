---
title: "Conventional Commits và Semantic Versioning: quy ước commit và đánh số version"
description: "Giải thích ngắn gọn Conventional Commits và Semantic Versioning, cách hai quy ước này liên kết để tự động hoá việc tăng version."
pubDatetime: 2026-09-29T09:30:00+07:00
tags:
  - git
  - conventional-commits
  - semver
featured: false
draft: false
---

> [!Note] BLUF
>
> - **Conventional Commits** là format cho commit message: `<type>(<scope>): <mô tả>`.
> - **Semantic Versioning** (viết tắt **SemVer**) là cách đánh số version `MAJOR.MINOR.PATCH`.
> - Hai quy ước nối với nhau: `feat` thì tăng `MINOR`, `fix` thì tăng `PATCH`, breaking change thì tăng `MAJOR`.
> - Cả hai đều là **thông lệ tuỳ chọn**, không có công cụ nào bắt buộc.
>
> Note này thuộc loạt note về GitHub, cùng với [đặt tên repo và Public/Private](/posts/github-repo-naming/), [setup SSH và Git](/posts/github-ssh-git-setup/) và [dùng nhiều account](/posts/github-multiple-accounts/).

## 1. Conventional Commits

Quy ước viết commit message theo format:

```text
<type>(<scope>): <mô tả ngắn>
```

`scope` là phần tuỳ chọn, cho biết commit ảnh hưởng tới module nào. Ví dụ:

```text
feat(auth): add password reset
fix(api): handle empty response
docs: update install steps in README
```

Các `type` thường dùng:

| Type | Ý nghĩa |
| --- | --- |
| `feat` | Thêm tính năng |
| `fix` | Sửa lỗi |
| `docs` | Cập nhật tài liệu |
| `refactor` | Tái cấu trúc code (không thêm tính năng, không sửa lỗi) |
| `style` | Thay đổi format hoặc style không ảnh hưởng logic |
| `test` | Thêm hoặc sửa test |
| `build` | Thay đổi build system hoặc dependencies |
| `chore` | Thay đổi kỹ thuật hoặc bảo trì không trực tiếp thêm tính năng hay sửa lỗi |

Lưu ý:

- Spec chính thức chỉ bắt buộc `feat` và `fix`. Các type còn lại là thông lệ phổ biến (bắt nguồn từ Angular convention), mỗi nhóm có thể chọn tập con phù hợp. Một số type khác hay gặp: `ci`, `perf`, `revert`.
- **Breaking change** (thay đổi không tương thích ngược) đánh dấu bằng dấu `!` sau type như `feat!: remove old API`, hoặc thêm footer `BREAKING CHANGE: ...`.

Chi tiết đầy đủ: [conventionalcommits.org](https://www.conventionalcommits.org).

## 2. Semantic Versioning (SemVer)

Quy ước đánh số version theo format `MAJOR.MINOR.PATCH`, ví dụ `1.4.2`. **SemVer** là cách viết tắt của Semantic Versioning, bạn sẽ thấy chữ này trong tên tài liệu, thư viện và công cụ:

| Thành phần | Tăng khi | Ví dụ |
| --- | --- | --- |
| `MAJOR` | Có thay đổi **không tương thích ngược** (breaking change) | `1.4.2` → `2.0.0` |
| `MINOR` | Thêm tính năng mới mà **vẫn tương thích ngược** | `1.4.2` → `1.5.0` |
| `PATCH` | Sửa lỗi mà **vẫn tương thích ngược** | `1.4.2` → `1.4.3` |

Quy tắc cần nhớ:

- Khi tăng số bên trái, **reset các số bên phải về 0** (`1.4.2` → `2.0.0`, không phải `2.4.2`).
- **Pre-release** gắn hậu tố sau dấu `-`, ví dụ `1.0.0-alpha.1`, `1.0.0-rc.1`. Version này thấp hơn bản `1.0.0` chính thức.
- Version `0.y.z` là giai đoạn phát triển ban đầu, API có thể thay đổi bất cứ lúc nào.
- Git tag thường thêm tiền tố `v` (ví dụ `v1.4.2`). Đây là thông lệ, không thuộc spec.

Chi tiết đầy đủ: [semver.org](https://semver.org).

## 3. Hai quy ước này liên kết với nhau

Nếu commit message theo Conventional Commits thì có thể suy ra bước tăng version theo SemVer:

| Loại commit | Tăng version |
| --- | --- |
| Có breaking change (`!` hoặc `BREAKING CHANGE`) | `MAJOR` |
| `feat` | `MINOR` |
| `fix` | `PATCH` |

Các type còn lại (`docs`, `style`, `test`, `chore`...) thường không làm tăng version.

## Kết luận

Commit message có cấu trúc giúp lịch sử dễ đọc và dễ tra cứu, còn SemVer giúp người dùng đoán được mức độ rủi ro khi nâng version. Khi dùng cả hai, việc quyết định tăng version trở thành hệ quả trực tiếp của các commit đã viết.
