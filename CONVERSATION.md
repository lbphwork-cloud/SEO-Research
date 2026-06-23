# Nhật ký xây dựng — SEO Competitor Analysis Web App

> Bản ghi lại cuộc trao đổi giữa **Hưng (Business Development, SEO Agency)** và **Claude** trong quá trình build công cụ phân tích đối thủ SEO. Ngày: 2026-06-23.

---

## 1. Bối cảnh & mục tiêu ban đầu

**Hưng:** Là BD tại một agency SEO (Google). Mới tiếp cận Claude, muốn build một web app nghiên cứu sơ bộ website nội tại + đối thủ. Input là các chỉ số cơ bản lấy từ Ahrefs (DR, UR, AR, Backlinks, Ref. domains, Organic keywords/traffic, phân phối DR của backlink...). Mục tiêu: dán domain nội tại + đối thủ → trả ra **Competitor Analysis + mô hình SWOT** đơn giản, kết hợp kiến thức SEO của Claude. Tạm thời chưa cần hosting/domain, muốn test chức năng trước. Yêu cầu xem kế hoạch trước khi làm.

**Claude:** Đề xuất kiến trúc ban đầu (Web UI → Backend Node.js → Claude API). Hỏi 3 điều: Claude API key, Node.js đã cài chưa, nhập thủ công hay import CSV.

---

## 2. Làm rõ yêu cầu

**Hưng:** Muốn trả ra file HTML edit được mã màu, lấy data trực tiếp từ Ahrefs (không nhập thủ công), chỉ input domain. Tối đa 5 đối thủ + 1 nội tại = 6 domain.

**Claude:** Cảnh báo việc "lấy data trực tiếp từ Ahrefs" cần **Ahrefs API** (trả phí riêng) hoặc scrape (vi phạm ToS). Hỏi Hưng có gói Ahrefs API không.

**Hưng:** Đã kết nối Ahrefs API, yêu cầu kiểm tra kết nối.

**Claude:** Test thành công qua MCP — gói **Advanced 2022**, 1,000,000 units/tháng (mới dùng 650), test lấy DR `ahrefs.com` = 91. Đề xuất 2 hướng: (A) App chạy trong Claude, (B) Web app độc lập.

---

## 3. Chốt hướng đi: web app upload file

**Hưng:** Quyết định **không lấy data trực tiếp từ Ahrefs nữa** (không có quyền kết nối, và tương lai muốn public website cho ai cũng dùng). Thay vào đó:
- 1 ô input domain website nội tại
- 1 ô upload file chỉ số (Ahrefs Batch Analysis export `.xlsx`, không giới hạn số domain)
- 2 ô nhập mã màu: 1 màu chính + 1 hoặc nhiều màu phụ

**Claude:** Đọc file Excel mẫu (`batch_analysis_2026-06-23.xlsx`) — 35 cột, mỗi dòng 1 domain. Xác nhận app có thể chạy **100% trong trình duyệt, không cần server, không cần API key** → lý tưởng để public. Đưa ra quyết định "bộ não phân tích":
- **B1 — Logic dựng sẵn (JS thuần):** miễn phí, public được, không lộ key
- **B2 — Gọi Claude API:** thông minh hơn nhưng cần server + key + tốn phí

---

## 4. Chốt B1 + bổ sung yêu cầu giao diện

**Hưng:** Chọn **B1**. Bổ sung: ô nhập mã màu tùy chỉnh mỗi lần dùng, upload logo, font **Space Grotesk**.

**Claude:** Build `index.html` v1 — single file, đọc xlsx bằng SheetJS, engine phân tích + SWOT theo 35 cột, export báo cáo HTML sửa màu được.

---

## 5. Tinh chỉnh báo cáo (v2)

**Hưng:** Sửa header thành màu chính (cho màu chính xuất hiện nhiều hơn), thêm biểu đồ trực quan, **bỏ** phần "Khuyến nghị ưu tiên", thêm phần **SUM UP & đề xuất** chỉn chu (cho phép Claude tự do sáng tạo, "đôi khi số liệu không quá quan trọng, điều tôi muốn truyền tải là sự chỉn chu").

**Claude:** Tham khảo cách agency dựng executive summary (The Blueprint Training, AgencyAnalytics, Search Engine Land). Cập nhật v2:
- Header nền gradient màu chính
- 4 biểu đồ SVG (offline): Donut Share of Voice + bar Organic Traffic / Domain Rating / Referring Domains + thanh phân bổ thứ hạng từ khóa
- Bỏ Khuyến nghị ưu tiên
- Thêm "Tổng kết & Đề xuất chiến lược": đoạn dẫn share of voice + 3 trụ cột (Authority / Nội dung / Đa kênh) + Kết luận

---

## 6. Nút định hướng đánh giá 5 cấp + màu mặc định + 2 logo (v3)

**Hưng:** Thêm nút chọn đầu ra theo **5 cấp độ đánh giá website nội tại**: chê mạnh / chê / bình thường / khen / khen mạnh. Đổi màu mặc định:
- Màu chính: `#004aed`
- Màu phụ: `#00ffaa`, `#ffce04`, `#15cfff`
- (vẫn chỉnh sửa được, chỉ là cài mặc định)
- Thêm chỗ upload **logo khách hàng** (hiển thị bên **phải**), logo của agency bên **trái**.

**Claude:** Cập nhật v3:
- Dropdown "Định hướng đánh giá" 5 cấp → đổi văn phong Tổng kết, thứ tự SWOT, kết luận (số liệu & biểu đồ giữ nguyên)
- Màu mặc định mới
- Header 2 logo: agency trái, client phải

---

## 7. Hỏi đáp về nguồn dữ liệu & tính offline

**Hưng:** Hỏi: gửi file HTML cho người khác xài được không? Claude lấy nguồn đâu để tạo SWOT/khuyến nghị — có phải tự search trên máy không? Đề xuất: tích hợp sẵn rule/công thức trong code để chạy offline khi người nhận không có AI/search; nếu có kết nối thì vẫn bật search.

**Claude:** Giải thích rõ:
- File HTML **chạy 100% bằng rule trong JavaScript** (`analyze()` + `execSummary()`), **không gọi Claude, không search, không cần internet**.
- Việc Claude search trước đó chỉ ở **thời điểm build** để học cách viết, đã "đóng băng" thành rule cứng trong code.
- → Gửi ai cũng chạy được.
- Giải thích giới hạn: HTML tĩnh không thể tự gọi Claude trừ khi nhúng API key (lộ key → không nên). Khuyến nghị: rule-based thuần làm mặc định; tùy chọn dán API key để sau.

**Hưng:** Chốt hướng **offline** với 2 việc: nhúng font + thư viện để chạy offline hẳn, làm dày bộ rule.

---

## 8. Sửa text trên trang → xuất PDF (v4)

**Hưng:** Đính chính: muốn xuất **PDF** (không phải Excel). Nêu vấn đề: nếu tải HTML về sửa text rồi quay lại web bấm xuất thì ra file gốc chưa sửa. Muốn **sửa xong mới chuyển sang PDF**.

**Claude:** Giải pháp v4 — sửa ngay trên trang:
- Nút **✏️ Bật chỉnh sửa** → click thẳng vào chữ trên báo cáo để gõ đè, thêm/bớt (contentEditable)
- Nút **🖨️ Xuất PDF** → in→PDF của trình duyệt, xuất đúng bản vừa sửa
- Thanh công cụ + panel nhập liệu tự ẩn khi in
- Lưu ý bật "Background graphics" để giữ màu khi in

---

## 9. Đẩy lên GitHub

**Hưng:** Yêu cầu upload 2 file lên GitHub: (1) file kết quả `index.html`, (2) file transcript cuộc trò chuyện này.

---

## Tổng kết kỹ thuật

| Hạng mục | Chi tiết |
|---|---|
| **Kiến trúc** | Single-file `index.html`, chạy 100% trình duyệt, không server/API |
| **Bộ não phân tích** | Rule-based JavaScript (B1) — offline, không gọi AI |
| **Input** | Domain nội tại + upload `.xlsx` (Ahrefs Batch Analysis) + màu chính/phụ + 2 logo + tone 5 cấp |
| **Output** | Báo cáo: tổng quan, 4 biểu đồ SVG, bảng so sánh, SWOT, Tổng kết & Đề xuất |
| **Tùy biến** | Màu, logo, định hướng đánh giá 5 cấp, sửa text trên trang |
| **Xuất** | PDF (in trình duyệt) + HTML độc lập |
| **Font** | Space Grotesk |
