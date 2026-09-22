# BÁO CÁO THỰC HÀNH LAB 3: PHÂN TÍCH XÁC THỰC, CƠ CHẾ DUY TRÌ VÀ PHÂN TÍCH NGUY CƠ AN TOÀN THÔNG TIN

## Thông tin sinh viên
- **Họ và tên:Nguyễn Tấn Hoàng
- **Mã số sinh viên (MSSV):1150080016
- **Lớp:** [Điền Mã Lớp của bạn]
- **Tên bài Lab:** Lab 3 - An Toàn & Bảo Mật Hệ Thống Thông Tin
- 
---

## 1. Môi trường thực hành
- **Hệ điều hành:** Windows Server 2025 Datacenter / Windows 11 Evaluation (VMware Workstation)
- **Phiên bản Python:** Python 3.14.0 (x64)
- **Công cụ rà soát & giám sát:**
  - Sysinternals Suite (Autoruns v14.x, Process Explorer v17.x)
  - Sysmon v15.x
  - Windows PowerShell 5.1 / PowerShell 7
  - Event Viewer (Security Log)

---

## 2. Hướng dẫn dựng môi trường thực hành
1. Khởi động máy ảo (VM) trên nền tảng VMware/VirtualBox với tài khoản quản trị Administrator.
2. Tải và giải nén gói tài nguyên bài lab vào thư mục `C:\LAB3`.
3. Cài đặt các công cụ phân tích từ bộ `Sysinternals` vào `C:\LAB3\Tools\Autoruns`.
4. Tạo thư mục chứa bằng chứng độc lập:
   ```powershell
   New-Item -ItemType Directory -Path "C:\LAB3\Evidence" -Force