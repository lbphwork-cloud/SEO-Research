# Hướng dẫn Deploy SEO Report Studio

App gồm 2 phần:
- `index.html` — giao diện (chạy được offline, AI sẽ tự tắt nếu không có backend).
- `api/analyze.js` — hàm serverless gọi Claude, **giấu API key phía server**.

Deploy lên **Vercel** (miễn phí) để có link public + bật được AI.

---

## A. Lấy Claude API key
1. Vào https://console.anthropic.com → **API Keys** → **Create Key**.
2. Copy key dạng `sk-ant-...` (chỉ hiện 1 lần — lưu lại).
3. Nạp một ít credit ở mục **Billing** (mỗi báo cáo tốn vài cent).

## B. Deploy lên Vercel
1. Tạo tài khoản tại https://vercel.com (đăng nhập bằng GitHub `lbphwork-cloud`).
2. **Add New… → Project** → chọn repo **SEO-Research** → **Import**.
3. Giữ nguyên mọi thiết lập mặc định (Framework Preset: **Other**), bấm **Deploy**.
4. Sau khi deploy xong → vào **Settings → Environment Variables**, thêm:
   | Name | Value |
   |------|-------|
   | `ANTHROPIC_API_KEY` | `sk-ant-...` (key ở bước A) |
   | `CLAUDE_MODEL` *(tùy chọn)* | `claude-opus-4-8` (mặc định) — đổi sang `claude-haiku-4-5` để rẻ hơn, hoặc `claude-sonnet-4-6` cân bằng |
5. Vào tab **Deployments → … → Redeploy** để áp env var mới.

## C. Dùng
- Mở link Vercel cấp (vd `https://seo-research-xxxx.vercel.app`).
- Nhập domain + upload file + tích mục → **Tạo báo cáo**.
- Nếu tích "Dùng Claude": phần SWOT & Kết luận do Claude viết theo cấp độ tone.
- Nếu lỗi mạng/hết credit: app tự fallback sang nhận định rule-based.

---

## Chi phí
- **Hosting Vercel:** miễn phí (Hobby tier).
- **Claude API:** trả theo lượt tạo báo cáo. Haiku rẻ nhất, Opus chất lượng cao nhất.
- **Domain riêng (tùy chọn):** Settings → Domains → thêm tên miền bạn mua (~250–350k₫/năm).

## Bảo mật
- API key chỉ nằm trong Environment Variable của Vercel, **không lộ ra index.html** → an toàn khi public.
- Không commit key vào GitHub.
