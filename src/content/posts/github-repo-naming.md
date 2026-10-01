---
title: "GitHub repo cơ bản: đặt tên và Public/Private"
description: "Giới thiệu nhanh về GitHub repo, cách đặt tên theo thông lệ chung và khi nào nên để Public hoặc Private."
pubDatetime: 2026-09-29T09:00:00+07:00
tags:
  - github
  - git
  - naming-convention
featured: true
draft: false
---

> [!Note] BLUF
>
> - Tên repo theo thông lệ: **chữ thường, kebab-case, mô tả rõ nội dung**.
> - Phân loại repo bằng **Topics** thay vì nhét vào tên.
> - Mặc định để **Private**, chỉ **Public** khi đã rà soát nội dung và secret.
> - Quy ước commit message và đánh số version nằm ở note riêng: [Conventional Commits và Semantic Versioning](/posts/conventional-commits-semver/).
>
> Note này thuộc loạt note về GitHub, cùng với [setup SSH và Git](/posts/github-ssh-git-setup/), [dùng nhiều account](/posts/github-multiple-accounts/) và [Conventional Commits và Semantic Versioning](/posts/conventional-commits-semver/).

## 1. Repo là gì

Repository (repo) là nơi chứa code cùng toàn bộ lịch sử thay đổi của một dự án. Một repo mới nên có tối thiểu:

| File | Tác dụng |
| --- | --- |
| `README.md` | Giới thiệu dự án, cách cài đặt và sử dụng |
| `.gitignore` | Loại file không nên commit (file build, `.env`, thư mục dependency) |
| `LICENSE` | Điều kiện cho người khác sử dụng code, quan trọng với repo Public |

Branch mặc định hiện nay là `main`.

## 2. Quy ước đặt tên repo

Không có chuẩn bắt buộc cho tên repo. Dưới đây là thông lệ phổ biến cộng với các ràng buộc có thật của GitHub.

### Thông lệ chung

| Nguyên tắc | Ví dụ tốt | Nên tránh |
| --- | --- | --- |
| Chữ thường, ngăn cách bằng dấu gạch ngang (**kebab-case**) | `stock-analysis-tool` | `Stock_Analysis_Tool` |
| Mô tả được nội dung hoặc mục đích | `rest-api-testing-examples` | `test2`, `new-project` |
| Ngắn gọn nhưng đủ nghĩa | `blog-site` | `my-personal-blog-site-for-notes-v2-final` |
| Không có khoảng trắng hoặc ký tự đặc biệt | `dotfiles` | `my repo!` |

Các điểm kỹ thuật cần biết:

- Tên repo chỉ nên gồm chữ cái, số, dấu gạch ngang, gạch dưới và dấu chấm. GitHub tự đổi khoảng trắng thành dấu gạch ngang khi tạo repo.
- Tên nên **ổn định lâu dài**. Đổi tên vẫn được (GitHub có redirect) nhưng URL, remote và link ngoài có thể bị ảnh hưởng.
- Repo fork nên giữ nguyên hoặc gần tên gốc. GitHub đã hiển thị "forked from ..." ở đầu trang.

### Tên có ý nghĩa đặc biệt

| Tên repo | Tác dụng |
| --- | --- |
| `<github-username>.github.io` | GitHub Pages cho user site, phải khớp chính xác username |
| `<github-username>` (trùng username) | Nội dung `README.md` hiển thị trên trang profile |
| `.github` | Community health files (issue template, `CONTRIBUTING.md`...) áp dụng cho các repo không có file riêng |

### Phân loại bằng Topics thay vì tên

GitHub cung cấp **Topics** làm cơ chế phân loại chính thức: gắn nhiều nhãn cho 1 repo (ngôn ngữ, framework, lĩnh vực) và filter theo nhãn đó.

- Topic chỉ gồm chữ thường, số và dấu gạch ngang.
- Phù hợp với thuộc tính **có thể nhiều giá trị cùng lúc** như `python`, `astro`, `testing`.
- Với số repo ít, tên repo rõ nghĩa có thể đã đủ. Khi số repo tăng và cần tìm theo công nghệ thì bắt đầu dùng Topics.

## 3. Public hay Private

| Nên Public khi | Nên Private khi |
| --- | --- |
| Dự án muốn trưng bày portfolio | Ghi chú hoặc dữ liệu cá nhân |
| Bài tập hoặc demo có README giải thích rõ nguồn gốc | Code nháp chưa đủ sạch |
| Không chứa secret | Chứa API key hoặc config nhạy cảm chưa rà soát |

Khi public hoá bài tập theo khoá học, **không commit tài liệu gốc** (video, PDF, slide) vì vẫn thuộc bản quyền của giảng viên hoặc nền tảng. Chỉ commit code tự viết.

Trước khi chuyển repo sang Public, rà lại **toàn bộ lịch sử commit** chứ không chỉ trạng thái hiện tại, vì secret đã từng commit vẫn còn trong history. Xem thêm phần bảo mật trong note [setup SSH và Git](/posts/github-ssh-git-setup/).

## Kết luận

Tên repo rõ nghĩa, ổn định và theo kebab-case là đủ cho đa số trường hợp. Dùng Topics khi cần phân loại, và luôn rà secret trước khi để repo Public.
