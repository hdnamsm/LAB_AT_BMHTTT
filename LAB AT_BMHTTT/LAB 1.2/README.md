# LAB 1: BẮT GÓI TIN TELNET - SSH (EXAMINING SSH & TELNET IN WIRESHARK)

**Học phần:** Thực hành An toàn Hệ thống thông tin  
**Bộ môn:** An toàn Thông tin

## THÔNG TIN SINH VIÊN & BÀI LÀM
- **Họ và tên sinh viên:** Nguyễn Tấn Hoàng
- **Mã số sinh viên:** 1150080016
- **Lớp:** 11_ĐH_THMT
- **Tên file báo cáo:** Lab1_11_ĐH_THMT_1150080016_Nguyễn Tấn Hoàng.docx
- **Video thực hành (YouTube):** [Youtube : Hoàng Nguyễn](https://www.youtube.com/@HoangNguyen-wy3gs)

---

## A. TỔNG QUAN BÀI THỰC HÀNH

### 1. Mục tiêu
- Giả lập hệ thống mạng Telnet – SSH theo mô hình Client/Server.
- Nắm vững cơ chế bắt và phân tích gói tin bằng phần mềm Wireshark.
- So sánh, đánh giá sự khác biệt về mức độ an toàn dữ liệu giữa Telnet (Plaintext) và SSH (Mã hóa).

### 2. Kịch bản & Mô hình mạng
**Mô hình triển khai:** 3 nút mạng kết nối vào cùng một switch nội bộ (Subnet: 10.0.0.0/24):
- **Server (10.0.0.1):** Cung cấp dịch vụ Telnet Server và SSH Server.
- **Client (10.0.0.2):** Sử dụng PuTTY hoặc CMD/Terminal để kết nối quản trị từ xa tới Server.
- **Attacker (10.0.0.3):** Đóng vai trò máy thu thập dữ liệu, chạy Wireshark để bắt lưu lượng mạng.

**Vấn đề đặt ra:** Khi người dùng kết nối đến máy chủ qua Telnet/SSH, kẻ tấn công trong cùng mạng nội bộ có thể xem hoặc đánh cắp được các thông tin nhạy cảm nào?

### 3. Môi trường & Công cụ sử dụng
- **Phần mềm ảo hóa:** VMware Workstation / VirtualBox.
- **Công cụ hỗ trợ:**
  - Wireshark (kèm driver Npcap).
  - PuTTY.
  - Cygwin (nếu triển khai SSH trên Windows Server 2008) hoặc OpenSSH tích hợp.

---

## B. CÁC BƯỚC THỰC HÀNH CHI TIẾT

### 1. Thiết lập môi trường & Tạo tài khoản thử nghiệm
- Cấu hình địa chỉ IP tĩnh cho 3 máy trạm và kiểm tra thông mạng bằng lệnh `ping`.
- Tạo tài khoản quản trị thử nghiệm trên Server:
  - **Tên đăng nhập (Username):** nguyentanhoang
  - **Mật khẩu ban đầu (Password):** 1150080016
  - **Lệnh Command Prompt:** `sudo adduser nguyentanhoang`

### 2. Bắt gói tin giao thức Telnet (TCP Port 23)
- **Bước 1:** Kích hoạt tính năng Telnet Server trong Server Manager và bật dịch vụ trong `services.msc`.
- **Bước 2:** Thêm tài khoản vừa tạo vào nhóm TelnetClients qua tiện ích `lusrmgr.msc`.
- **Bước 3:** Khởi động Wireshark trên máy theo dõi, chọn card mạng tương ứng và thiết lập bộ lọc: `tcp.port == 23`.
- **Bước 4:** Tại Client, mở PuTTY (Connection type: Telnet, Port: 23) đăng nhập vào Server và thực hiện các lệnh kiểm tra (`dir`, `mkdir`).
- **Bước 5:** Dừng bắt gói tin; sử dụng chức năng Analyze > Follow > TCP Stream để đọc lại nội dung cuộc hội thoại.
- **Bước 6 (Thử nghiệm tăng độ phức tạp mật khẩu):**
  - Đổi mật khẩu sang chuỗi ký tự phức tạp (> 10 ký tự gồm chữ hoa, chữ thường, số, ký tự đặc biệt).
  - Lặp lại quá trình bắt gói và kiểm tra xem độ phức tạp của mật khẩu có ngăn chặn được việc lộ thông tin qua Telnet hay không.

### 3. Bắt gói tin giao thức SSH (TCP Port 22)
- **Bước 1:** Cài đặt dịch vụ SSH Server (Cài OpenSSH qua Cygwin hoặc kích hoạt OpenSSH Server trên hệ điều hành).
- **Bước 2:** Cấu hình và chạy dịch vụ sshd (`net start sshd` hoặc `Start-Service sshd`).
- **Bước 3:** Bật Wireshark với bộ lọc hiển thị: `tcp.port == 22`.
- **Bước 4:** Tại Client, dùng PuTTY chọn SSH, Port 22 kết nối đến Server:
  - Kiểm tra và xác thực cảnh báo Server Host-key Fingerprint.
  - Đăng nhập và thực thi một số lệnh thao tác thư mục.
- **Bước 5:** Dừng bắt gói tin, kiểm tra payload của các gói tin SSH trên Wireshark. Đối chiếu kết quả thu được với phiên Telnet.

---

## C. BẢNG TRẢ LỜI CÂU HỎI BÁO CÁO (11 CÂU)
*Lưu ý: Các câu trả lời dưới đây cần đối chiếu trực tiếp với ảnh chụp màn hình và dữ liệu bắt gói thực tế thu được trong bài làm.*

### Câu 1: Khái niệm & Ứng dụng của Telnet và SSH
**Trả lời:** 

**a. Telnet (Port TCP 23)**
- **Khái niệm:** Giao thức điều khiển máy chủ từ xa qua giao diện dòng lệnh (CLI). Truyền toàn bộ dữ liệu (tài khoản, mật khẩu, lệnh) dưới dạng văn bản rõ (Plaintext), không mã hóa nên dễ bị nghe lén (Sniffing).
- **Ứng dụng:** Chủ yếu dùng trong môi trường Lab học tập để minh họa bắt gói tin, kiểm tra trạng thái mở cổng mạng (`telnet <IP> <Port>`), hoặc quản trị thiết bị nhúng cũ không hỗ trợ mã hóa.

**b. SSH - Secure Shell (Port TCP 22)**
- **Khái niệm:** Giao thức quản trị từ xa an toàn thay thế cho Telnet. Tích hợp mã hóa mạnh (AES, ChaCha20), xác thực máy chủ qua Host Key và kiểm tra tính toàn vẹn dữ liệu (HMAC), ngăn chặn lộ mật khẩu và tấn công Man-in-the-Middle.
- **Ứng dụng:** Tiêu chuẩn quản trị từ xa máy chủ Linux/Windows Server, cấu hình thiết bị mạng (Router/Switch), truyền file bảo mật (SFTP/SCP), và xác thực an toàn qua SSH Key (Git/GitHub, CI/CD).

### Câu 2: So sánh Telnet và SSH
**Trả lời:** 

| Tiêu chí | Telnet | SSH (Secure Shell) |
| :--- | :--- | :--- |
| **Cổng mặc định** | TCP 23 | TCP 22 |
| **Cơ chế truyền tin** | Văn bản rõ (Plaintext), hoàn toàn không mã hóa | Mã hóa toàn bộ dữ liệu (Symmetric + Asymmetric Encryption) |
| **Bảo mật mật khẩu** | Mật khẩu lộ thô trên mạng, dễ bị bắt lén (Sniffing) | Mật khẩu được mã hóa an toàn, không thể đọc trộm |
| **Tính toàn vẹn** | Không hỗ trợ kiểm tra tính toàn vẹn gói tin | Dùng mã xác thực MAC/HMAC để chống giả mạo, sửa gói tin |
| **Phương thức xác thực** | Chỉ dùng tài khoản và mật khẩu truyền thống | Hỗ trợ mật khẩu, cặp khóa SSH (Public/Private Key), MFA/2FA |
| **Khả năng chống MITM** | Kém, không có cơ chế xác minh máy chủ | Cao, dùng Host Key Fingerprint để định danh server thật |
| **Ứng dụng thực tế** | Dùng kiểm tra cổng dịch vụ, demo lab học tập | Chuẩn mực quản trị máy chủ, mạng và truyền file (SFTP/SCP) |

### Câu 3: Các phương thức đăng nhập SSH ngoài mật khẩu truyền thống & Demo
**Trả lời:** 

**Các phương thức:**
- **SSH Key (Public/Private Key):** Dùng cặp khóa mật mã (an toàn và chuẩn mực nhất).
- **SSH Certificate:** Máy chủ CA cấp chứng chỉ số có thời hạn.
- **2FA/MFA:** Kết hợp khóa/mật khẩu với mã OTP (Google Authenticator) hoặc khóa bảo mật vật lý (FIDO2/YubiKey).

**Demo nhanh với SSH Key:**
1. Tạo khóa trên Client (Windows): `ssh-keygen -t rsa -b 4096`
2. Thêm Public Key vào Server (Ubuntu): Dán nội dung `id_rsa.pub` vào file `~/.ssh/authorized_keys`.
3. Đăng nhập không mật khẩu: Chạy lệnh `ssh ubuntu@192.168.10.109` (hoặc nạp file `.ppk` vào PuTTY), hệ thống tự động xác thực và vào thẳng Terminal mà không lộ mật khẩu trên mạng.

### Câu 4: Phân tích ba thuộc tính an toàn (CIA) giữa Telnet và SSH
**Trả lời:** 
- **Tính bí mật (Confidentiality):**
  - **Telnet:** Không có. Dữ liệu truyền dạng thô (Plaintext), mật khẩu và lệnh điều khiển bị đọc trọn vẹn khi bị bắt gói tin.
  - **SSH:** Rất cao. Sử dụng các thuật toán mã hóa mạnh (AES, ChaCha20) để mã hóa toàn bộ dữ liệu truyền tải, chống nghe lén tuyệt đối.
- **Tính toàn vẹn (Integrity):**
  - **Telnet:** Không có. Không có cơ chế kiểm tra gói tin bị thay đổi hay chèn ép trên đường truyền.
  - **SSH:** Đảm bảo tuyệt đối. Tích hợp mã xác thực thông điệp (HMAC); nếu gói tin bị can thiệp dù chỉ 1 bit, phiên làm việc sẽ lập tức bị hủy bỏ.
- **Tính xác thực (Authentication):**
  - **Telnet:** Yếu kém. Chỉ hỗ trợ đăng nhập mật khẩu văn bản rõ, không xác thực được máy chủ kết nối đến có phải là giả mạo hay không.
  - **SSH:** Mạnh mẽ và đa dạng. Hỗ trợ xác thực bằng mật khẩu mã hóa, SSH Key, chứng chỉ số; có cơ chế Host Key Fingerprint giúp Client xác minh đúng danh tính Server trước khi truyền dữ liệu.

### Câu 5: Dữ liệu Telnet vs SSH quan sát được qua Wireshark & Bằng chứng
**Trả lời:** 

**So sánh dữ liệu thu thập:**
- **Telnet (Port 23):** Thu được 100% dữ liệu dạng văn bản rõ (Plaintext) qua tính năng `Follow > TCP Stream`. Quan sát thấy đầy đủ tài khoản, mật khẩu, lệnh gõ và dữ liệu phản hồi từ server.
- **SSH (Port 22):** Chỉ thấy thông tin tầng mạng/vận chuyển (IP nguồn/đích, Port 22, kích thước gói) và chuỗi chào phiên bản giao thức ban đầu. Toàn bộ dữ liệu phiên sau trao đổi khóa hiển thị dưới dạng chuỗi ký tự mã hóa ngẫu nhiên (Encrypted packet), không đọc được tài khoản hay lệnh.

**Bằng chứng từ thực nghiệm:**
- **Telnet Stream:** Hiển thị rõ chuỗi nhập `nguyentanhoang`, `1150080016`, tài khoản `ubuntu` và mật khẩu `123456`.
- **SSH Stream:** Toàn bộ payload hiển thị dạng rác mã hóa (ví dụ: `....@..#$%%...`), chứng minh kênh truyền được bảo mật tuyệt đối.

### Câu 6: Tại sao mật khẩu dài và phức tạp không bảo vệ được Telnet?
**Trả lời:** 
- **Thiếu cơ chế mã hóa (Lack of Encryption):** Telnet truyền toàn bộ dữ liệu ở dạng văn bản rõ (Plaintext) trực tiếp trong trường dữ liệu của gói tin TCP.
- **Mật khẩu phức tạp chỉ chống Brute-force:** Độ dài và độ phức tạp chỉ có tác dụng ngăn kẻ tấn công đoán mò hoặc dùng từ điển để dò mật khẩu.
- **Vô hiệu trước Sniffing (Bắt gói tin):** Dù mật khẩu dài 10 hay 50 ký tự, chứa cả chữ hoa, số và ký tự đặc biệt, phần mềm bắt gói (như Wireshark) vẫn ghi nhận đầy đủ 100% từng ký tự đó dưới dạng ASCII nguyên bản trên đường truyền.

### Câu 7: Metadata quan sát được trong phiên SSH và rủi ro tiềm ẩn
**Trả lời:** 

**Các metadata quan sát được qua Wireshark:**
- Địa chỉ IP nguồn / đích, cổng dịch vụ (TCP 22).
- Banner phiên bản giao thức ban đầu (ví dụ: `SSH-2.0-OpenSSH_9.6`).
- Kích thước gói tin (packet length), tần suất truyền và độ trễ thời gian giữa các gói.

**Rủi ro bảo mật tiềm ẩn (Traffic Analysis):**
- **Tấn công thời gian gõ phím (Keystroke Timing Attack):** SSH gửi gói tin theo từng ký tự gõ tương tác. Kẻ tấn công phân tích độ trễ giữa các gói để suy đoán độ dài hoặc thói quen gõ mật khẩu.
- **Khai thác lỗ hổng đã biết (Targeted Exploits):** Thông tin banner để lộ chính xác phiên bản phần mềm SSH, giúp kẻ tấn công tìm các mã khai thác (CVE) tương ứng để tấn công hệ thống.
- **Phân tích hành vi (Profiling):** Kích thước và lưu lượng truyền cho phép kẻ theo dõi đoán biết người dùng đang truyền file dung lượng lớn (SFTP/SCP) hay chỉ gõ lệnh quản trị thông thường.

### Câu 8: Vai trò của Host-key Fingerprint & Rủi ro khi chấp nhận tùy tiện
**Trả lời:** 

**Vai trò của Host-key Fingerprint:**
- Là chuỗi băm (hash) đại diện duy nhất cho khóa công khai (Public Key) của SSH Server.
- Dùng để định danh và xác thực máy chủ, giúp người dùng (Client) xác minh mình đang kết nối đúng vào máy chủ thật chứ không phải hệ thống giả mạo.

**Rủi ro khi chấp nhận tùy tiện (Bấm "Yes/Accept" không kiểm tra):**
- **Bị tấn công Man-in-the-Middle (MITM):** Kẻ tấn công đứng ở giữa có thể chặn kết nối, mạo danh máy chủ đích và gửi một Host Key giả cho Client.
- **Đánh cắp thông tin xác thực:** Nếu người dùng chấp nhận Host Key lạ mà không đối chiếu fingerprint, kẻ tấn công có thể nghe lén mật khẩu đăng nhập, thu thập Private Key hoặc chèn lệnh độc hại vào phiên làm việc mà nạn nhân không hề hay biết.

### Câu 9: Nguyên nhân máy Attacker chưa chắc bắt được lưu lượng unicast & Giải pháp
**Trả lời:** 

**Nguyên nhân:**
- Các hệ thống mạng và hạ tầng ảo hóa hiện đại dùng Switch (thay vì Hub).
- Switch hoạt động ở tầng Data Link (Layer 2), tự học địa chỉ MAC và chỉ chuyển tiếp gói tin unicast đến đúng cổng (port) của máy nhận đích.
- Máy Attacker nằm ở cổng mạng khác nên Switch không bao giờ chuyển tiếp các gói tin unicast của phiên Client-Server qua máy này.

**Giải pháp trong thực tế:**
- **Port Mirroring (SPAN port) hoặc Network TAP:** Cấu hình trên switch để nhân bản toàn bộ lưu lượng qua cổng của Client/Server sang cổng của Attacker.
- **ARP Spoofing / Poisoning:** Gửi các gói tin ARP giả mạo để lừa Client và Server chuyển hướng lưu lượng qua máy Attacker trước khi tới đích.
- **Bắt gói tin trực tiếp trên máy đầu cuối:** Cài đặt công cụ bắt gói (tcpdump, Wireshark) ngay tại máy Client hoặc Server để thu thập trực tiếp lưu lượng.

### Câu 10: Nguyên lý của Public-key Authentication trong SSH
**Trả lời:** 

**Khái niệm:** Là phương thức xác thực người dùng dựa trên mật mã khóa bất đối xứng (Asymmetric Cryptography) gồm một cặp khóa: Private Key (khóa riêng tư bí mật đặt ở máy Client) và Public Key (khóa công khai lưu tại file `~/.ssh/authorized_keys` của Server).

**Nguyên lý hoạt động (Cơ chế Thử thách - Phản hồi / Challenge-Response):**
1. **Yêu cầu kết nối:** Client gửi yêu cầu đăng nhập kèm định danh của Public Key tương ứng lên Server.
2. **Gửi thử thách (Challenge):** Server kiểm tra nếu Public Key tồn tại trong danh sách tin cậy, nó sẽ tạo ra một chuỗi ngẫu nhiên (gọi là Challenge) rồi gửi về cho Client.
3. **Ký số (Response):** Client sử dụng Private Key bí mật của mình để tạo chữ ký số (Digital Signature) trên chuỗi thử thách đó và gửi trả lại Server.
4. **Xác minh:** Server dùng Public Key đã lưu sẵn để kiểm tra chữ ký. Nếu chữ ký hợp lệ, Server xác nhận danh tính thành công và mở phiên làm việc.

**Ưu điểm so với xác thực bằng mật khẩu:**
- **Triệt tiêu nguy cơ rò rỉ mật khẩu:** Không có bất kỳ mật khẩu nào được truyền trên mạng, vô hiệu hóa hoàn toàn nguy cơ bị nghe lén (Sniffing).
- **Chống tấn công Brute-force/Dictionary:** Cặp khóa mật mã (thường từ 2048-bit đến 4096-bit RSA hoặc Ed25519) có độ phức tạp toán học cực lớn, không thể dò đoán mò.
- **Tăng cường bảo vệ cục bộ:** Private Key trên máy khách có thể được bảo vệ bằng mật mã bảo vệ riêng (Passphrase).

### Câu 11: Đề xuất 3 biện pháp củng cố an mật (Hardening) cho dịch vụ SSH
**Trả lời:** 
1. **Vô hiệu hóa xác thực bằng mật khẩu, chỉ cho phép dùng SSH Key (`PasswordAuthentication no`):**
   - **Mục đích:** Ép buộc mọi kết nối phải dùng cặp khóa bất đối xứng (Public/Private Key), triệt tiêu hoàn toàn nguy cơ bị dò quét mật khẩu tự động (Brute-force và Dictionary attacks).
2. **Chặn đăng nhập trực tiếp bằng tài khoản root (`PermitRootLogin no`):**
   - **Mục đích:** Ngăn chặn kẻ tấn công nhắm thẳng vào tài khoản có đặc quyền cao nhất của hệ thống; buộc quản trị viên phải đăng nhập bằng tài khoản người dùng thường rồi mới nâng quyền (`sudo`), giúp phân định rõ trách nhiệm và ghi vết nhật ký truy cập (Audit Log).
3. **Đổi cổng mặc định (Thay đổi Port 22 sang một cổng ngẫu nhiên, ví dụ: 2222, 5022):**
   - **Mục đích:** Tránh các botnet và công cụ quét tự động trên diện rộng (mass scanners) quét trúng cổng 22 mặc định, giảm thiểu tối đa các cảnh báo giả và tải xử lý ghi log trên máy chủ.
