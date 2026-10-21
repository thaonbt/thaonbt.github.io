# Hướng dẫn dựng site GitHub Pages (`thaonbt.github.io`)

🌐 **Ngôn ngữ:** [English](README.md) · Tiếng Việt

Tài liệu tham khảo cho repo `thaonbt.github.io`: 
- cách site được dựng, các phương án làm trang chủ
- và cách xử lý lỗi 404.

## Tóm tắt nhanh

| Mục tiêu | File cần có | Cần `_config.yml` | Kết quả |
|---|---|---|---|
| Trang chủ viết bằng Markdown | `index.md` | Nên có (để chọn theme) | Jekyll render ra HTML có theme |
| Trang chủ tự thiết kế HTML/CSS | `index.html` | Không | Phục vụ nguyên file, không theme |
| Trang chủ tự chuyển sang site khác | `index.html` (chỉ chứa redirect) | Không | Người truy cập bị chuyển ngay |
| Trang báo lỗi tùy chỉnh | `404.html` | Không | Hiển thị khi URL không tồn tại |

Quy tắc cốt lõi: **GitHub Pages không đọc `README.md` để dựng site.** Nó chỉ dựng từ `index.md` hoặc `index.html`. `README.md` chỉ hiển thị trên trang repo GitHub.

---

## 0. Điều kiện chung

1. Tên repo phải đúng `thaonbt.github.io` (chữ thường và khớp username).
2. Repo phải **Public** (gói free).
3. Vào **Settings → Pages**, chọn *Deploy from a branch* → branch `main` → thư mục `/ (root)`.
4. Mỗi lần commit lên `main`, GitHub chạy workflow *pages build and deployment*. Theo dõi ở tab **Actions** (thường mất 1-2 phút).
5. Khi test, dùng cửa sổ ẩn danh hoặc hard refresh (`Ctrl+Shift+R`) vì có cache.

Cấu trúc repo tham khảo:

```
thaonbt.github.io/
├── README.md        # Tài liệu bản tiếng Anh (chỉ hiển thị trên trang repo)
├── README.vi.md     # Tài liệu bản tiếng Việt (chỉ hiển thị trên trang repo)
├── _config.yml      # Cấu hình Jekyll và theme (chỉ cần khi dùng index.md)
├── index.md  hoặc  index.html   # CHỌN MỘT, không để cả hai
├── about.md         # Trang phụ (tùy chọn)
└── 404.html         # Trang lỗi 404 (tùy chọn)
```

---

## 1. Dùng `index.md` (Markdown + Jekyll theme)

**Khi nào dùng:** muốn viết nội dung nhanh bằng Markdown (CV, giới thiệu, blog đơn giản) mà không phải tự viết HTML/CSS.

**Cách hoạt động:** GitHub Pages chạy Jekyll, đọc `_config.yml` để lấy theme, rồi render `index.md` thành `index.html` có giao diện theme.

### Bước 1: Tạo `_config.yml`

```yaml
title: Thao Nguyen
description: Senior QA engineer exploring the intersection of quality, data and finance.
theme: jekyll-theme-minimal   # theme có sẵn trong GitHub Pages
exclude:
  - README.md                 # tránh Jekyll biến README thành một trang của site
  - README.vi.md
```

Các theme có sẵn thường dùng: `jekyll-theme-minimal`, `jekyll-theme-cayman` và `jekyll-theme-slate`.

### Bước 2: Tạo `index.md`

```markdown
---
title: Thao Nguyen
---

# Hi, I'm Thao

Nội dung Markdown của bạn ở đây.
```

Front matter (khối giữa hai dòng `---`) là **bắt buộc** để Jekyll xử lý file. Thiếu khối này, file có thể bị copy nguyên mà không render.

### Bước 3: Commit và kiểm tra

Commit lên `main`, chờ workflow xong rồi mở `https://thaonbt.github.io/`.

### Trang phụ

Tạo file `.md` khác kèm `permalink`:

```markdown
---
title: About
permalink: /about/
---
```

Trang sẽ nằm ở `https://thaonbt.github.io/about/`.

### Pitfalls

- Bảng Markdown cần **một dòng trống phía trên**, nếu không kramdown có thể không render thành table.
- `README.md` và `README.vi.md` ở root có thể bị Jekyll render thành trang riêng nếu không có `exclude` như trên.
- Muốn blog lâu dài, cân nhắc **Minimal Mistakes** qua `remote_theme: mmistakes/minimal-mistakes` (cần plugin `jekyll-remote-theme`). Mạnh hơn nhưng cấu hình phức tạp hơn.

---

## 2. Dùng `index.html` (tự viết HTML)

**Khi nào dùng:** muốn toàn quyền kiểm soát giao diện, không phụ thuộc Jekyll theme.

**Cách hoạt động:** GitHub Pages phục vụ nguyên file HTML. Nếu file **không có front matter**, Jekyll copy nguyên văn mà không áp theme hay layout.

### Ví dụ tối thiểu

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Thao Nguyen</title>
  <style>
    body { font-family: system-ui, sans-serif; max-width: 720px; margin: 10vh auto; padding: 0 1rem; line-height: 1.6; }
  </style>
</head>
<body>
  <h1>Hi, I'm Thao</h1>
  <p>Senior QA Engineer.</p>
</body>
</html>
```

Có thể tách CSS/JS thành file riêng (ví dụ `/assets/style.css`) và gọi bằng đường dẫn tuyệt đối như `/assets/style.css`.

### Pitfalls

- **Không để `index.md` và `index.html` cùng tồn tại.** Cả hai đều sinh ra `/index.html` nên xung đột.
- Không cần `_config.yml` cho trường hợp này. Nếu repo vẫn có file đó thì không ảnh hưởng.
- Nếu muốn dùng Jekyll layout/include trong file HTML, phải thêm front matter (`---` rồi `---`). Khi đó file được Jekyll xử lý.

---

## 3. Dùng `index.html` để tự chuyển trang (redirect)

**Khi nào dùng:** muốn `thaonbt.github.io` đưa người truy cập thẳng sang trang khác (ở đây là `https://profile.thaonbt.workers.dev/`).

**Cách hoạt động:** GitHub Pages không hỗ trợ server-side redirect (301). Cách thực tế là phục vụ một trang HTML chỉ có lệnh chuyển hướng phía client.

### Tạo `index.html` (không front matter)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Redirecting…</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <!-- Redirect chính: hoạt động cả khi tắt JavaScript -->
  <meta http-equiv="refresh" content="0; url=https://profile.thaonbt.workers.dev/">
  <!-- Báo cho search engine biết trang đích -->
  <link rel="canonical" href="https://profile.thaonbt.workers.dev/">
  <!-- Fallback: không để lại entry trong history nên nút Back không bị kẹt -->
  <script>window.location.replace("https://profile.thaonbt.workers.dev/");</script>
</head>
<body>
  <p>Đang chuyển hướng… <a href="https://profile.thaonbt.workers.dev/">Bấm vào đây</a> nếu trình duyệt không tự chuyển.</p>
</body>
</html>
```

### Giữ lại trang CV cũ (tùy chọn)

Đổi `index.md` thành `about.md` và thêm `permalink: /about/` vào front matter. Trang sẽ nằm ở `/about/`.

### Các phương án khác

| Phương án | Ưu điểm | Nhược điểm |
|---|---|---|
| Meta refresh + JS (ở trên) | Không cần plugin và ổn định | Không phải 301 thật |
| `jekyll-redirect-from` (`redirect_to:` trong front matter của file `.md`) | Ít code | Vẫn là redirect phía client và phụ thuộc Jekyll build |
| Custom domain + Cloudflare Redirect Rules | 301 thật và tốt cho SEO | Cần domain riêng và không áp dụng cho `*.github.io` |

### Pitfalls

- Không để `index.md` và `index.html` cùng tồn tại.
- **Redirect loop:** nếu trang đích có link hoặc redirect ngược về `thaonbt.github.io` thì sẽ lặp vô hạn.
- Trình duyệt có thể cache redirect nên hãy test bằng cửa sổ ẩn danh.

---

## 4. Trang `404.html` tùy chỉnh

**Khi nào dùng:** muốn người gõ sai URL thấy một trang rõ ràng kèm link điều hướng thay vì trang 404 mặc định của GitHub.

**Cách hoạt động:** GitHub Pages tự động dùng `404.html` ở root cho mọi đường dẫn không tồn tại. HTTP status vẫn là 404.

### 404 mặc định trông như thế nào

- **Site đã deploy nhưng URL sai:** trang trắng với logo GitHub và dòng "404 File not found".
- **Site chưa bật hoặc repo sai tên:** thông báo "There isn't a GitHub Pages site here".

### Tạo `404.html` (không front matter)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>404 - Page not found</title>
  <meta name="robots" content="noindex">
  <style>
    body { font-family: system-ui, sans-serif; max-width: 480px; margin: 20vh auto; padding: 0 1rem; text-align: center; color: #222; }
    h1 { font-size: 4rem; margin: 0; }
    a { color: #0b5fff; }
  </style>
</head>
<body>
  <h1>404</h1>
  <p>Trang bạn tìm không tồn tại hoặc đã được di chuyển.</p>
  <p>
    <a href="https://profile.thaonbt.workers.dev/">Hồ sơ của mình</a> ·
    <a href="https://notes.thaonbt.workers.dev/">Notes</a> ·
    <a href="https://thaonbt.github.io/about/">About</a>
  </p>
</body>
</html>
```

Test: mở `https://thaonbt.github.io/bat-ky-gi` trong cửa sổ ẩn danh.

### Trade-off: báo lỗi hay tự redirect

- **Trang thông báo (khuyên dùng):** giữ đúng status 404, người dùng biết mình gõ sai và dễ phát hiện link hỏng.
- **Tự redirect về profile:** tiện nhưng bị coi là *soft 404* và che mất link hỏng.
- **Dung hòa:** giữ trang thông báo và thêm `<meta http-equiv="refresh" content="5; url=https://profile.thaonbt.workers.dev/">` để tự chuyển sau 5 giây.

### Pitfalls

- Dùng **đường dẫn tuyệt đối** cho CSS/JS/link vì `404.html` được phục vụ tại chính URL bị lỗi (đường dẫn tương đối sẽ hỏng ở URL lồng nhau như `/a/b/c`).
- URL phân biệt chữ hoa/thường: `/About/` sẽ 404 trong khi `/about/` thì không.
- Nếu viết bằng Markdown (`404.md`) thì phải có `permalink: /404.html`, nếu không file sinh ra `/404/index.html` và GitHub không nhận.
- Chỉ có **một** `404.html` ở root và không có 404 riêng cho từng thư mục con.

---

## 5. Checklist khi site không hiển thị đúng

1. Tên repo có đúng `thaonbt.github.io` và đang Public không?
2. **Settings → Pages** có đang chọn `main` / `/ (root)` không?
3. Tab **Actions** có báo lỗi ở workflow *pages build and deployment* không?
4. Có đang tồn tại cả `index.md` lẫn `index.html` không?
5. File `.md` có front matter (`---`) chưa?
6. Đã hard refresh hoặc thử cửa sổ ẩn danh chưa?
7. Chữ hoa/thường của tên file và URL có khớp không?
