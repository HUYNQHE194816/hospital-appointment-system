# Hospital Appointment & Medical Service Management System

> Hệ thống quản lý lịch hẹn và dịch vụ y tế bệnh viện — SWD392 · SE1972-NET · Group 2

Ứng dụng web giúp bệnh nhân đặt và quản lý lịch khám, đồng thời giúp nhân viên, bác sĩ và quản lý bệnh viện vận hành lịch làm việc, dịch vụ y tế, hồ sơ bệnh án và báo cáo trên một hệ thống tập trung.

---

## 1. Thành viên nhóm

| MSSV | Họ tên | Vai trò |
|------|--------|---------|
| HE191686 | Bùi Huyền Diệu | Leader |
| HE194816 | Ngô Quang Huy | Member |
| HE191234 | Phạm Đình Khánh | Member |
| HE194050 | Nguyễn Nhật Minh | Member |
| HE200253 | Trần Huy Hoàng | Member |

---

## 2. Chức năng chính

| ID | Chức năng | Mô tả ngắn |
|----|-----------|------------|
| FE-01 | Account Management | Đăng ký, đăng nhập, quên mật khẩu, quản lý hồ sơ; Manager tạo tài khoản Doctor/Staff, phân quyền, khóa/mở khóa |
| FE-02 | Doctor & Schedule Management | Xem thông tin bác sĩ và lịch trống; Staff tạo và quản lý lịch làm việc |
| FE-03 | Appointment Management | Đặt, xem, hủy lịch hẹn, chọn dịch vụ; xác nhận và cập nhật trạng thái lịch hẹn |
| FE-04 | Patient Management | Quản lý thông tin và lịch sử bệnh nhân |
| FE-05 | Medical Service Management | Quản lý dịch vụ y tế và khoa/phòng ban |
| FE-06 | Medical Record Management | Tạo, xem, cập nhật hồ sơ bệnh án theo quyền |
| FE-07 | Reporting | Dashboard, thống kê lịch hẹn, bệnh nhân, dịch vụ; xuất báo cáo |

**Vai trò người dùng:** Patient, Doctor, Nurse, Receptionist, Scheduling Manager, Medical Service Manager, HR Manager, Hospital Manager.

Chi tiết xem tài liệu trong thư mục [`docs/`](./docs).

---

## 3. Kiến trúc và công nghệ

Hệ thống theo mô hình **Client–Server**, giao tiếp qua **RESTful API (JSON over HTTPS)**.

```
 Trình duyệt ──► Client (Blazor WebAssembly) ──► Server (ASP.NET Core Web API) ──► Supabase (PostgreSQL)
                                                        │
                                                        └──► Email Gateway (gửi thông báo lịch hẹn)
```

| Thành phần | Công nghệ |
|-----------|-----------|
| Client | Blazor WebAssembly (.NET 8) |
| Server | ASP.NET Core Web API (.NET 8), xác thực JWT, phân quyền theo vai trò (RBAC) |
| Truy cập dữ liệu | Entity Framework Core + Npgsql |
| Cơ sở dữ liệu | Supabase (PostgreSQL) |
| Kiểm thử API | Postman |
| Quản lý mã nguồn | Git + GitHub |

**Server gồm 3 lớp:** API Layer (controllers, middleware) → Business Layer (services) → Data Access Layer (repositories, Unit of Work).

> **Lưu ý:** tài liệu SRS/SDS ban đầu ghi frontend HTML/CSS/JS và database SQL Server. Khi triển khai nhóm chuyển sang Blazor WebAssembly và Supabase (PostgreSQL). Kiến trúc client–server và các lớp của server không thay đổi.

---

## 4. Cấu trúc thư mục

```
hospital-appointment-system/
├── client/
│   └── HospitalAppointment.Client/   # Blazor WebAssembly
├── server/
│   └── HospitalAppointment.Api/      # ASP.NET Core Web API
├── docs/                             # Report 1 (SRS), Report 2 (SDS)
├── .gitignore
└── README.md
```

---

## 5. Yêu cầu môi trường

- [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0) (kiểm tra bằng `dotnet --list-sdks`)
- Git
- Visual Studio 2022 hoặc Visual Studio Code (kèm extension C# Dev Kit)
- Tài khoản [Supabase](https://supabase.com) (xem mục 6)

---

## 6. Chạy dự án trên máy

**Bước 1. Clone repo**

```bash
git clone https://github.com/<username>/hospital-appointment-system.git
cd hospital-appointment-system
```

**Bước 2. Cấu hình biến môi trường cho server**

Tạo file `server/HospitalAppointment.Api/.env` hoặc dùng User Secrets (xem mục 7). Các giá trị cần có:

```
SUPABASE_CONNECTION_STRING=<lấy từ trưởng nhóm>
JWT_SECRET=<lấy từ trưởng nhóm>
```

Không tự tạo project Supabase riêng; hãy xin thông tin kết nối từ trưởng nhóm để cả nhóm dùng chung một database.

**Bước 3. Chạy server**

```bash
cd server/HospitalAppointment.Api
dotnet run
```

Mở `/swagger` trên cổng được in ra trong terminal để xem danh sách API.

**Bước 4. Chạy client** (terminal khác)

```bash
cd client/HospitalAppointment.Client
dotnet run
```

Mở địa chỉ được in ra trong terminal. Client cần biết địa chỉ của server (cấu hình trong `wwwroot/appsettings.json` của client).

---

## 7. Bảo mật: quy tắc bắt buộc

- **Tuyệt đối không commit** khóa Supabase (đặc biệt là `service_role key`), connection string, JWT secret hay bất kỳ mật khẩu nào lên repo.
- File `.env` đã nằm trong `.gitignore`. Chỉ commit file mẫu `.env.example` với giá trị giả.
- Khi phát triển, ưu tiên dùng .NET User Secrets:

```bash
cd server/HospitalAppointment.Api
dotnet user-secrets init
dotnet user-secrets set "ConnectionStrings:Supabase" "<connection-string>"
```

- Nếu lỡ commit khóa nhầm: báo ngay cho trưởng nhóm để **đổi (rotate) khóa** trên Supabase. Xóa commit là chưa đủ.

---

## 8. Quy trình làm việc với Git

> Phần này là đề xuất để cả nhóm thống nhất, có thể chỉnh lại sau khi họp nhóm.

**Nhánh**

- `main`: nhánh ổn định, không push trực tiếp.
- Mỗi chức năng một nhánh riêng, đặt tên theo dạng `feature/<ten-chuc-nang>`, ví dụ `feature/appointment-booking`, hoặc `fix/<mo-ta>` cho sửa lỗi.

**Commit message** (theo Conventional Commits)

```
feat: add appointment booking endpoint
fix: prevent double booking of the same slot
docs: update README
chore: update dependencies
```

**Quy trình**

1. Cập nhật `main`: `git checkout main && git pull`
2. Tạo nhánh mới: `git checkout -b feature/<ten>`
3. Code, commit nhỏ và thường xuyên.
4. Push nhánh và tạo **Pull Request** vào `main`.
5. Ít nhất 1 thành viên khác review rồi mới merge.
6. Trước khi tạo PR, đảm bảo server và client build và chạy được: `dotnet build`.

---

## 9. Tài liệu

| Tài liệu | Nội dung |
|----------|----------|
| `docs/` Report 1 — Overview & SRS | Mô tả dự án, yêu cầu, use case, ERD, business rules |
| `docs/` Report 2 — Software Design Specification | Kiến trúc, thiết kế database, thiết kế chi tiết từng chức năng, class specification |

---

## 10. Liên hệ

Có thắc mắc về dự án, hãy hỏi trong nhóm chat hoặc liên hệ trưởng nhóm (Bùi Huyền Diệu).
