# BÁO CÁO THỰC HÀNH - LAB 4: KHẢO SÁT VÀ ĐÁNH GIÁ BỀ MẶT MẠNG BẰNG NMAP

---

## 1. THÔNG TIN SINH VIÊN & MÔN HỌC
* **Họ và tên:** Nguyễn Tấn Hoàng
* **Mã số sinh viên (MSSV):** 1150080016
* **Học phần:** An toàn hệ thống thông tin
* **Tên bài thực hành:** LAB 4 – Khảo sát và đánh giá bề mặt mạng bằng Nmap
* **Thời gian thực hiện:** Tháng 09/2026

---

## 2. PHIÊN BẢN & MÔI TRƯỜNG THỰC HÀNH
* **Hệ điều hành máy thật (Host):** Windows 11 64-bit
* **Phần mềm ảo hóa:** Oracle VM VirtualBox
* **Môi trường mạng:** Mạng nội bộ riêng biệt `Host-Only Adapter` (`VirtualBox Host-Only Ethernet Adapter #2`), dải địa chỉ IP `192.168.56.0/24`
* **Máy quét (Scanner VM):**
  * Hệ điều hành: Kali Linux 2026.2 (64-bit)
  * Địa chỉ IP: Cấp phát tự động qua DHCP Host-Only trong dải `192.168.56.0/24`
  * Công cụ chính: Nmap
* **Máy mục tiêu (Target VM):**
  * Hệ điều hành: Metasploitable 2 (Linux Kernel 2.6.x)
  * Địa chỉ IP cố định: `192.168.56.101`
  * Giao diện mạng: `eth0` kết nối card Host-Only

---

## 3. CÁCH DỰNG MÔI TRƯỜNG THỰC HÀNH
1. **Thiết lập card mạng ảo Host-Only:**
   * Cấu hình adapter mạng `Host-Only` với dải mạng `192.168.56.0/24`.
   * Gán card mạng cho cả hai máy ảo về cùng adapter: `VirtualBox Host-Only Ethernet Adapter #2`.
2. **Cấu hình máy ảo mục tiêu (Metasploitable 2):**
   * Gắn tệp đĩa cứng ảo có sẵn hệ điều hành `Metasploitable.vmdk` (~1.81 GB) vào bộ điều khiển SATA.
   * Khởi động máy, đăng nhập bằng tài khoản mặc định `msfadmin` / `msfadmin`.
   * Chạy lệnh `ifconfig` để xác minh địa chỉ IP nhận diện là `192.168.56.101`.
3. **Cài đặt máy ảo quét (Kali Linux):**
   * Tạo máy ảo Kali Linux mới với ổ đĩa cứng ảo 25 GB VDI (Dynamically allocated).
   * Nạp tệp đĩa cài đặt `kali-linux-2026.2-installer-amd64.iso` vào ổ CD/DVD ảo để tiến hành cài đặt hệ thống.
   * Cấu hình mạng Host-Only bằng lệnh `VBoxManage` trên máy thật.
   * Kiểm tra thông mạng từ Kali Linux tới máy mục tiêu bằng lệnh: `ping -c 4 192.168.56.101` (xác nhận phản hồi 0% packet loss).

---

## 4. DANH SÁCH CÁC TÌNH HUỐNG ĐÃ THỰC HIỆN & KẾT QUẢ

| STT | Tình huống / Nhiệm vụ thực hành | Cú pháp câu lệnh chính | Kết quả | Ghi chú & Đánh giá |
| :---: | :--- | :--- | :---: | :--- |
| **01** | Kiểm tra kết nối mạng 2 chiều | `ping -c 4 192.168.56.101` | **PASS** | Phản hồi thông suốt 4/4 gói tin, 0% packet loss. |
| **02** | Khảo sát tìm kiếm host sống (Host Discovery) | `sudo nmap -sn 192.168.56.0/24` | **PASS** | Phát hiện đầy đủ các host đang hoạt động trong dải Host-Only (bao gồm `192.168.56.101`). |
| **03** | Khảo sát cổng TCP Connect Scan | `nmap -sT 192.168.56.101` | **PASS** | Hoàn tất bắt tay 3 bước đầy đủ, không yêu cầu đặc quyền root. |
| **04** | Khảo sát cổng TCP SYN Scan (Nửa mở) | `sudo nmap -sS 192.168.56.101` | **PASS** | Quét nhanh với gói tin raw SYN/RST, yêu cầu quyền sudo. |
| **05** | Quét cổng TCP bất thường (FIN / Xmas / NULL) | `sudo nmap -sF/-sX/-sN 192.168.56.101` | **PASS** | Khảo sát phản ứng TCP stack của mục tiêu theo chuẩn RFC 793. |
| **06** | Khảo sát chính sách lọc gói (ACK Scan) | `sudo nmap -sA 192.168.56.101` | **PASS** | Phân biệt trạng thái filtered và unfiltered trên firewall mục tiêu. |
| **07** | Quét cổng UDP có kiểm soát | `sudo nmap -sU --top-ports 20 192.168.56.101` | **PASS** | Phát hiện các dịch vụ UDP mở và giải thích trạng thái `open\|filtered`. |
| **08** | Dò quét phiên bản dịch vụ chi tiết | `sudo nmap -sV 192.168.56.101` | **PASS** | Nhận diện chính xác phiên bản các dịch vụ: vsftpd 2.3.4, Apache 2.2.8, Samba 3.X, MySQL 5.0... |
| **09** | Nhận diện hệ điều hành mục tiêu | `sudo nmap -O 192.168.56.101` | **PASS** | Phân tích TCP/IP stack fingerprinting, dự đoán nhân Linux 2.6.X. |
| **10** | Thu thập thông tin SMB bằng NSE Script | `sudo nmap -p 445 --script smb-os-discovery 192.168.56.101` | **PASS** | Lấy được Computer name (`metasploitable`), OS (`Unix/Samba`), Workgroup. |
| **11** | Kiểm tra lỗ hổng MS17-010 qua NSE | `sudo nmap -p 445 --script smb-vuln-ms17-010 192.168.56.101` | **PASS** | Kiểm tra rủi ro dịch vụ SMB (không bị ảnh hưởng do mục tiêu chạy Linux). |
| **12** | Xuất kết quả hồ sơ bằng chứng | `sudo nmap -sV 192.168.56.101 -oA ketqua_lab4` | **PASS** | Xuất đồng thời 3 định dạng tệp chuẩn: `.nmap`, `.xml`, `.gnmap`. |

---

## 5. LỖI GẶP PHẢI VÀ CÁCH KHẮC PHỤC

### Lỗi 1: Máy ảo Metasploitable 2 báo "No bootable medium found"
* **Nguyên nhân:** Máy ảo được tạo với một ổ cứng ảo trắng (`.vdi` dung lượng 2 MB) thay vì liên kết đến tệp đĩa hệ điều hành đã được cài đặt sẵn.
* **Cách khắc phục:** Vào `Settings` $\rightarrow$ `Storage` của máy ảo Metasploitable 2, gỡ bỏ ổ đĩa trắng và thêm tệp đĩa gốc `Metasploitable.vmdk` (~1.81 GB) vào `Controller: SATA`.

### Lỗi 2: Giao diện VirtualBox không hiển thị tùy chọn card Host-Only
* **Nguyên nhân:** Menu xổ xuống ở phần cài đặt mạng `Settings -> Network` của VirtualBox không đồng bộ danh sách adapter Host-Only từ hệ thống Windows.
* **Cách khắc phục:** Dùng Command Prompt (Admin) trên Windows Host để ép gán card mạng thông qua lệnh:
  ```cmd
  "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" modifyvm "Kali-Linux" --nic1 hostonly --hostonlyadapter1 "VirtualBox Host-Only Ethernet Adapter #2"
