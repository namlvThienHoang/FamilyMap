# FamilyMap — Personal Family Relationship Map

**Trạng thái:** Design baseline, chưa triển khai mã nguồn. **Chủ sở hữu:** một người dùng cá nhân.

FamilyMap giúp ghi nhớ tên, khuôn mặt, tên thường gọi và quan hệ họ hàng từ góc nhìn của người dùng, bằng cách thêm dần từng người thân. Hồ sơ có thể tồn tại ngay cả khi quan hệ chưa xác minh.

## Cách bắt đầu với Codex
1. Đọc `AGENTS.md` (quy tắc) và `GOALS.md` (mục tiêu).
2. Đọc `PLANS.md` (milestone) và `docs/product/PRD.md` (nghiệp vụ).
3. Giao `prompts/00-discovery.md` cho Codex trong **Plan mode / read-only**.
4. Chốt các ADR và tiêu chí nghiệm thu trước khi yêu cầu tạo code.
5. Mỗi task: lập kế hoạch → phê duyệt → triển khai → test → review → merge.
6. Mỗi lần kết thúc task: cập nhật `harness/build-log.md` **chỉ sau khi được phép**.

## Điều hướng tài liệu
- Product: `docs/product/` — PRD, user flows, acceptance criteria.
- Architecture: `docs/architecture/` — kiến trúc, data model, quan hệ, API, ADR.
- UX: `docs/design/UX-SPEC.md`.
- Delivery: `docs/delivery/` — Codex workflow, DoD, test, privacy, deployment.
- Executable planning: `PLANS.md`, `harness/build/`.
- Prompt templates: `prompts/`.

## Hướng công nghệ

Web dùng Next.js, TypeScript, React Query và shadcn/ui. Chủ sở hữu đã phê duyệt ASP.NET Core trên **.NET 10 LTS**, EF Core 10 với Npgsql và **PostgreSQL 18.x** để lưu đồ thị gia đình trong các bảng quan hệ. Môi trường phát triển dùng PostgreSQL cục bộ qua Docker Compose. Cấu hình phát triển và bộ phiên bản cụ thể sẽ được kiểm chứng khi triển khai M1. [ADR-001](docs/architecture/adr/ADR-001-relational-graph.md) · [ADR-002](docs/architecture/adr/ADR-002-runtime-target.md)

Ứng dụng dành cho một tài khoản và ảnh được lưu ở chế độ riêng tư. Nhà cung cấp hosting và chính sách sao lưu production đang chờ phê duyệt riêng.

## Nguyên tắc riêng tư
Không commit dữ liệu thật của người thân, ảnh thật, ngày sinh, số điện thoại, mật khẩu hay backup vào Git. Dùng dữ liệu giả trong fixtures và tài liệu.

**Lưu ý:** Các tệp `AGENTS.md`, `PLANS.md`, `GOALS.md` và bộ tài liệu dưới đây là quy ước dự án; ngoại trừ `AGENTS.md`, Codex không tự yêu cầu phải có những tên tệp này.
