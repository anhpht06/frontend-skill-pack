---
name: 08-jira-task-logging
description: Hướng dẫn quy tắc và định dạng bắt buộc khi lập kế hoạch và tạo log công việc hàng ngày trên Jira.
---

# Jira Daily Task Logging

Tài liệu này quy định cách AI Agent hỗ trợ Developer phân tách, lập kế hoạch (trước khi code) và viết log công việc (Worklog) hàng ngày lên Jira một cách chuẩn xác, đúng định dạng và logic thời gian.

## 1. Cấu Trúc Bắt Buộc Của Một Task Log

Mỗi một log công việc phải tuân thủ nghiêm ngặt template sau:

### [PROJECT-KEY] | [FE/BE] <Nội dung công việc ngắn gọn>
- **title**: [PROJECT-KEY] | [FE/BE] <Nội dung công việc ngắn gọn>
- **Thời lượng**: <0.5h | 1h | 2h | 3h>
- **Ngày**: dd/mm/yyyy (Thứ X)
- **description**:
  - Repo: <Mã ticket Jira (vd: PROJ-123)>
  - <Liệt kê các đầu mục việc cụ thể: làm gì, ở file/package nào, input/output ra sao>
  - Test: (Bắt buộc đối với các task làm tính năng - feature)
    - <2-4 gạch đầu dòng: ghi rõ loại test (unit/integration/manual) — input là gì, kỳ vọng (expectation) là gì>

*Lưu ý: Thay `[PROJECT-KEY]` bằng mã dự án thực tế. Thêm `[FE]` vào title nếu là task Frontend, hoặc `[BE]` nếu là task Backend.*

## 2. Quy Tắc Về Thời Gian (Time Tracking)

Khi AI Agent giúp chia nhỏ công việc của một ngày, **BẮT BUỘC** phải áp dụng các quy tắc toán học sau:

1. **Khối lượng (Chunking):** Thời lượng của mỗi task con được phép là **0.5h**, **1h**, **2h**, hoặc tối đa **3h**.
2. **Tổng thời gian mỗi ngày:** Không nhất thiết phải luôn gò ép đủ 8h-9h. Tổng thời lượng một ngày có thể là **7h, 8h, hoặc 9h** tùy thuộc vào tình hình thực tế của Developer.
3. **Không vắt ngang ngày:** Tuyệt đối không tách 1 task để log vắt qua 2 ngày khác nhau. Nếu một việc lớn mất 5 tiếng, phải tách thành 2 task con có title/description khác biệt (Ví dụ: 3h cho ngày 1 và 2h cho ngày 2 với nội dung tiếp nối).

## 3. Hướng Dẫn Dành Cho AI Agent

AI Agent phải linh hoạt xử lý 2 trường hợp (Log TRƯỚC KHI code và Log SAU KHI code):

### Trường hợp 1: Lên kế hoạch Log Jira TRƯỚC khi làm
Khi Dev yêu cầu *"Tôi chuẩn bị làm tính năng X, hãy chia task và viết log Jira cho tôi"*:
1. Đọc yêu cầu nghiệp vụ của tính năng.
2. Dự đoán các component, service, api cần làm.
3. Chẻ nhỏ chúng thành các task `0.5h`, `1h`, `2h`, `3h` (tổng thời gian 7h - 9h/ngày) để Developer dùng làm bảng kế hoạch log Jira.

### Trường hợp 2: Viết Log Jira SAU khi đã code xong
Khi Dev yêu cầu *"Hãy viết log Jira hôm nay cho tôi dựa trên code vừa viết"*:
1. Đọc lướt qua các file đã thay đổi (git diff) hoặc nghe mô tả sơ lược của Dev.
2. Gom nhóm các thay đổi logic lại với nhau.
3. Chẻ nhỏ các nhóm đó theo khối thời gian cho phù hợp với khối lượng code thực tế (0.5h đến 3h/task).
4. Viết output ra Markdown áp dụng đúng Template ở phần 1.
