# BÁO CÁO THỰC HÀNH LAB 3: NHẬN DIỆN VÀ ỨNG PHÓ CÁC MỐI ĐE DỌA ATTT

## 1. Thông Tin Sinh Viên
- **Họ và tên:** Nguyễn Phước Thịnh
- **MSSV:** 1150070040
- **Lớp:** 11TMDT
- **Môn học:** An toàn Hệ thống Thông tin
- **Tên bài thực hành:** Lab 3 - Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin
- **Link Video Thực Hành: https://youtu.be/OYgzuUHa0d8
---

## 2. Phiên Bản Môi Trường Thực Hành
- **Hệ điều hành:** Windows 11 x64, OS Build 26200
- **Endpoint Protection:** Microsoft Defender Antivirus (RealTimeProtection: True, TamperProtection: True)
- **Tường lửa:** Windows Defender Firewall (Enabled: True trên cả Domain, Private, Public)
- **Công cụ phân tích & giám sát:**
  - Python: 3.14.7
  - Wireshark / TShark: 4.6.8
  - Microsoft Sysmon: 15.22 (Schema 4.90)
  - Microsoft Autoruns: 14.3
  - Microsoft Process Explorer: 17.14

---

## 3. Cách Dựng Môi Trường
1. Khởi tạo cấu trúc thư mục chuẩn trên ổ C: `C:\LAB3` gồm các thư mục con: `Evidence`, `Tools`, `Downloads`, `Assets`.
2. Đồng bộ các công cụ Sysinternals vào `C:\LAB3\Tools` và tài nguyên bài lab vào `C:\LAB3\lab3_assets`.
3. Kiểm tra tính sẵn sàng và xác nhận phiên bản chuẩn của toàn bộ 5 công cụ.
4. Thu thập thông tin nền (Baseline) của hệ điều hành, Defender, Firewall và lưu bằng chứng vào `C:\LAB3\Evidence`.

---

## 4. Các Tình Huống Đã Thực Hiện & Kết Quả

| Tình huống | Nội dung thực hiện | Kết quả | Bằng chứng |
| :--- | :--- | :--- | :--- |
| **TH1** | Xây dựng Risk Register & Phân loại 5 nguồn đe dọa | **PASS** | Trình bày trong file báo cáo Word |
| **TH2** | Kiểm chứng chu trình phát hiện mã độc bằng tệp EICAR | **PASS** | `H4_ProtectionHistory_EICAR.png`, `defender_eicar.txt` |
| **TH3** | Kích hoạt Audit Logon, thử nghiệm tài khoản `lab3user`, xoay vòng mật khẩu | **PASS** | `H5_Event4625.png`, `auth_events_before_rotation.txt` |
| **TH4** | Nhận diện Persistence (Run key, Scheduled Task) & HTTP Listener loopback | **PASS** | Ghi nhận qua Autoruns, Process Explorer |
| **TH5** | Sniffing lưu lượng HTTP loopback (plaintext) và so sánh mã hóa HTTPS TLS | **PASS** | Wireshark capture |
| **TH6** | Đánh giá DoS tải cục bộ và phân tích dataset DDoS / Mail bombing | **PASS** | Thống kê log phân tán |
| **TH7** | Phân tích 5 chỉ dấu email lừa đảo và phân loại 6 case Social Engineering | **PASS** | Phân tích mẫu offline |

---

## 5. Lỗi Gặp Phải Và Cách Khắc Phục
1. **Lỗi tham số lệnh `auditpol` (Error 0x00000057):**
   - *Nguyên nhân:* PowerShell hiểu nhầm cặp ngoặc nhọn `{GUID}` không đặt trong dấu ngoặc kép.
   - *Khắc phục:* Đặt GUID trong dấu nháy kép hoặc sử dụng trực tiếp tên danh mục `auditpol /set /subcategory:"Logon" /success:enable /failure:enable`.
2. **Lỗi `runas` báo `Unable to acquire user password`:**
   - *Nguyên nhân:* Cửa sổ PowerShell trên Windows 11 xung đột tính năng đọc mật khẩu ẩn khi dán phím qua clipboard.
   - *Khắc phục:* Nhập trực tiếp mật khẩu từ bàn phím hoặc sử dụng đối tượng `PSCredential` của PowerShell để xác thực tự động.
3. **Đường dẫn Wireshark không nằm ở ổ C:**
   - *Nguyên nhân:* Wireshark được cài đặt tại `D:\Software\Wireshark`.
   - *Khắc phục:* Tự động kiểm tra và nhận diện đường dẫn thực thi của `tshark.exe` trên hệ thống.

---

## 6. Tính Toàn Vẹn Bằng Chứng Số
- Toàn bộ các tệp bằng chứng và nhật ký trong thư mục `Evidence` được tính toán mã băm SHA-256 lưu tại `evidence_sha256.csv`.
