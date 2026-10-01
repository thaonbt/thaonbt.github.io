---
title: "Markdown cheatsheet: cú pháp cơ bản"
description: "Tổng hợp nhanh cú pháp Markdown thường dùng: heading, định dạng chữ, list, link, ảnh, code, quote, bảng và footnote."
pubDatetime: 2026-10-01T09:00:00+07:00
tags:
  - cheatsheet
  - markdown
featured: true
draft: false
---

## TL;DR

- Phần lớn cú pháp dưới đây thuộc **CommonMark** hoặc **GFM** (GitHub Flavored Markdown) nên chạy được ở hầu hết nền tảng.
- Lỗi hay gặp nhất: thiếu **dòng trống** giữa các khối, thiếu **khoảng trắng** sau `#` hoặc `-`, và quên **escape** ký tự đặc biệt.
- Công thức toán, sơ đồ và callout không thuộc phần chuẩn, xem note [Math, Mermaid và cú pháp mở rộng](/posts/markdown-math-mermaid-cheatsheet/).

## 1. Heading

```markdown
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6
```

- Bắt buộc có **khoảng trắng** sau dấu `#`.
- Mỗi trang chỉ nên có 1 heading 1. Với blog, title trong front matter thường đã là heading 1 nên nội dung bắt đầu từ `##`.

## 2. Định dạng chữ

| Cú pháp | Kết quả |
| --- | --- |
| `**đậm**` | **đậm** |
| `*nghiêng*` | *nghiêng* |
| `***đậm và nghiêng***` | ***đậm và nghiêng*** |
| `~~gạch ngang~~` | ~~gạch ngang~~ |
| `` `code inline` `` | `code inline` |

Để viết dấu backtick bên trong code inline, bọc bằng 2 backtick và chừa khoảng trắng: ``` `` `x` `` ```.

## 3. Đoạn văn và xuống dòng

- Hai đoạn văn cách nhau bằng **1 dòng trống**.
- Xuống dòng trong cùng 1 đoạn (một trong ba cách):
  - Thêm **2 khoảng trắng** ở cuối dòng.
  - Thêm dấu `\` ở cuối dòng.
  - Dùng thẻ `<br>`.

Một dòng xuống đơn lẻ (không có dòng trống) **không** tạo đoạn mới, Markdown sẽ gộp lại thành 1 đoạn.

## 4. List

```markdown
- Gạch đầu dòng
- Mục khác
  - Mục con (thụt vào 2 hoặc 4 khoảng trắng)

1. Mục có số thứ tự
2. Mục tiếp theo
   1. Mục con có số

- [ ] Việc chưa làm (task list)
- [x] Việc đã xong
```

- Dấu `-` và `1.` đều cần **khoảng trắng** theo sau.
- Task list `- [ ]` là cú pháp GFM.

## 5. Link và ảnh

```markdown
[Văn bản link](https://example.com)
[Link có tooltip](https://example.com "Tiêu đề tooltip")
<https://example.com>

![Mô tả ảnh](./images/anh.png)
![Mô tả ảnh](https://example.com/anh.png "Chú thích")

[Link kiểu tham chiếu][ref-1]

[ref-1]: https://example.com
```

- **Alt text** của ảnh nên mô tả nội dung ảnh, hỗ trợ accessibility và SEO.
- Link nội bộ trong cùng site dùng đường dẫn tương đối hoặc tuyệt đối theo gốc site, ví dụ `/posts/ten-bai/`.

## 6. Code block

````markdown
```python
def hello(name: str) -> str:
    return f"Hello, {name}"
```
````

- Ghi tên ngôn ngữ ngay sau 3 backtick mở để có **syntax highlighting** (`python`, `bash`, `ts`, `json`...).
- Muốn hiển thị nguyên một code block **bên trong** code block, dùng **4 backtick** cho khối ngoài (như ví dụ trên).

## 7. Blockquote

```markdown
> Trích dẫn một dòng.
>
> Đoạn thứ hai trong cùng quote.
>
> > Quote lồng nhau.
```

## 8. Đường kẻ ngang

```markdown
---
```

Cần **dòng trống phía trên** dấu `---`. Nếu dòng ngay trên là chữ, Markdown sẽ hiểu đó là heading cấp 2.

## 9. Bảng (GFM)

```markdown
| Căn trái | Căn giữa | Căn phải |
| :------- | :------: | -------: |
| Ô 1      |   Ô 2    |      Ô 3 |
```

- Dấu `:` trong dòng phân cách quyết định căn lề: `:---` trái, `:---:` giữa, `---:` phải.
- Muốn dùng ký tự `|` trong ô, viết `\|`.
- Không cần căn thẳng hàng các cột bằng tay, chỉ cần đúng số cột. Editor có thể format giúp.
- Muốn đặt cả bảng vào giữa trang, xem phần HTML trong note [Math, Mermaid và cú pháp mở rộng](/posts/markdown-math-mermaid-cheatsheet/).

## 10. Footnote

```markdown
Câu có chú thích[^1].

[^1]: Nội dung chú thích hiển thị ở cuối trang.
```

Footnote không thuộc CommonMark gốc nhưng được GitHub và nhiều renderer hỗ trợ.

## 11. Escape ký tự đặc biệt

Thêm `\` phía trước để hiển thị nguyên ký tự thay vì để Markdown diễn giải:

```text
\\  \`  \*  \_  \{ \}  \[ \]  \( \)  \#  \+  \-  \.  \!  \|
```

Ví dụ: `\*không nghiêng\*` hiển thị thành \*không nghiêng\*.

## 12. HTML nhúng

Markdown cho phép chèn HTML trực tiếp (tuỳ renderer có cho phép hay không):

```markdown
Phím tắt: <kbd>Cmd</kbd> + <kbd>S</kbd>

H<sub>2</sub>O và x<sup>2</sup>

<details>
<summary>Bấm để mở rộng</summary>

Nội dung ẩn, có thể viết **Markdown** bên trong (chừa dòng trống sau thẻ mở).

</details>
```

## 13. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
| --- | --- | --- |
| Heading hoặc list không hiện | Thiếu khoảng trắng sau `#`, `-` hoặc `1.` | Thêm khoảng trắng |
| Hai đoạn dính làm một | Thiếu dòng trống giữa hai đoạn | Thêm 1 dòng trống |
| List con không thụt vào | Thụt lề không đều (trộn tab và space) | Dùng thống nhất 2 hoặc 4 khoảng trắng |
| Bảng hiển thị thành chữ thường | Thiếu dòng phân cách `---` hoặc thiếu dòng trống phía trên bảng | Thêm dòng phân cách và dòng trống |
| Ký tự `*` hoặc `_` làm chữ bị nghiêng ngoài ý muốn | Markdown diễn giải thành định dạng | Escape bằng `\` |
| Nội dung trong thẻ HTML không render Markdown | Thiếu dòng trống sau thẻ mở | Thêm dòng trống ngay sau thẻ mở |

## Kết luận

Nắm vững khoảng 10 cú pháp trên là đủ cho hầu hết ghi chú và tài liệu kỹ thuật. Những gì vượt ra ngoài (toán, sơ đồ, callout) phụ thuộc vào nền tảng hiển thị nên được tách riêng ở note khác.
