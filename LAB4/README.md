# BÁO CÁO THỰC HÀNH AN TOÀN HỆ THỐNG THÔNG TIN

## 1. THÔNG TIN SINH VIÊN & BÀI LAB
* **Họ và tên:** Nguyễn Phước Thịnh
* **Mã số sinh viên (MSSV):** 1150070040
* **Lớp:** 11TMDT
* **Tên bài lab:** LAB 4 – KHẢO SÁT VÀ ĐÁNH GIÁ BỀ MẶT MẠNG BẰNG NMAP
* **Môn học:** An toàn hệ thống thông tin (Giảng viên: Thầy Huỳnh)
* **Link Video YouTube:** *https://youtu.be/NjXfHiKdLzA*

---

## 2. PHIÊN BẢN MÔI TRƯỜNG
* **Nền tảng ảo hóa:** VMware Workstation 26
* **Máy quét (Scanner):**
  * Hệ điều hành: Kali Linux (User: `nguyenphuocthinh`)
  * Phiên bản Nmap: `Nmap 7.99`
  * Địa chỉ IP: `192.168.119.129`
* **Máy mục tiêu (Target):**
  * Hệ điều hành: Metasploitable 2
  * Địa chỉ IP: `192.168.119.128` 
* **Máy thật đối chiếu:** Windows (Cài đặt Nmap 7.991 + Npcap)

---

## 3. CÁCH DỰNG MÔI TRƯỜNG
* **Bước 1 (Cấu hình mạng cô lập):** Cấu hình Network Adapter của cả 2 máy ảo (Kali Linux và Metasploitable 2) sang chế độ **Host-Only** để đảm bảo an toàn tuyệt đối và cùng chung dải subnet `192.168.119.0/24`.
* **Bước 2 (Xác định IP máy đích):** Khởi động Metasploitable 2, đăng nhập tài khoản `msfadmin / msfadmin`, chạy lệnh `ifconfig` ghi nhận IP `192.168.119.128`.
* **Bước 3 (Xác định IP máy quét):** Khởi động Kali Linux, mở Terminal chạy lệnh `ip -br addr` ghi nhận IP `192.168.119.129`.
* **Bước 4 (Kiểm tra thông mạng):** Từ Kali Linux, gửi 4 gói tin ICMP kiểm tra kết nối: `ping -c 4 192.168.119.128` (kết quả trả về 0% packet loss, kết nối thông suốt).

---

## 4. CÁC TÌNH HUỐNG ĐÃ THỰC HIỆN & KẾT QUẢ PASS/FAIL

| STT | Tình huống thực hiện | Câu lệnh Nmap | Kết quả ghi nhận | Đánh giá 
| :---: | :--- | :--- | :--- | :---: | :---: |
| **1** | Xác định IP máy quét & máy đích | `ip -br addr` / `ifconfig` | Ghi nhận IP `192.168.119.129` và `192.168.119.128` | **PASS** |
| **2** | Dò quét thiết bị mạng (Host Discovery) | `sudo nmap -sn 192.168.119.0/24` | Phát hiện 3 hosts UP, xác định MAC VMware của máy đích | **PASS** | 
| **3** | Khảo sát TCP Connect Scan | `nmap -sT 192.168.119.128` | Hoàn tất bắt tay 3 bước, phát hiện 23 cổng TCP open | **PASS** 
| **4** | Khảo sát TCP SYN Stealth Scan | `sudo nmap -sS 192.168.119.128` | Bắt tay nửa mở (gửi RST ngắt kết nối), 23 cổng open | **PASS** | 
| **5** | Nhận diện Dịch vụ & Phiên bản | `sudo nmap -sV 192.168.119.128` | Bóc tách chính xác: vsftpd 2.3.4, OpenSSH 4.7p1, Apache 2.2.8, Samba 3.X, MySQL 5.0.51a... | **PASS** 
| **6** | Nhận diện Hệ điều hành | `sudo nmap -O 192.168.119.128` | Nhận diện chính xác nhân OS: `Linux 2.6.X` | **PASS** | [Ảnh 6](img/Anh6_OS_Detection.png) |
| **7** | Mở rộng với NSE Script | `sudo nmap -p 139,445 --script smb-os-discovery 192.168.119.128` | Trích xuất Computer name `metasploitable`, Workgroup `WORKGROUP`, OS `Unix Samba 3.0.20` | **PASS**  |
| **8** | Xuất báo cáo & Tạo HTML | `sudo nmap -sV -O -oA Lab4_Report_NguyenPhuocThinh 192.168.119.128`<br>`xsltproc ...xml -o ...html` | Xuất thành công 4 file bằng chứng: `.nmap`, `.xml`, `.gnmap`, `.html` | **PASS** | 

---

## 5. LỖI GẶP PHẢI VÀ CÁCH KHẮC PHỤC
* **Lỗi 1: Nhầm lẫn địa chỉ Host thay vì địa chỉ Subnet khi Host Discovery**
  * *Mô tả:* Khi gõ lệnh quét mạng diện rộng, ban đầu dễ gõ nhầm IP máy đích kèm `/24` (ví dụ `192.168.119.128/24`).
  * *Cách khắc phục:* Đổi phần Host ID về số `0` theo đúng chuẩn phân chia mạng CIDR (`192.168.119.0/24`) để đảm bảo tính chuẩn xác học thuật và quét toàn bộ dải IP từ `.1` đến `.254`.
* **Lỗi 2: Thiếu quyền Root khi thực thi các kỹ thuật can thiệp gói tin thô (Raw Packet)**
  * *Mô tả:* Chạy các lệnh `-sS`, `-O`, `-sn` dưới user thông thường bị báo lỗi từ chối quyền truy cập raw socket.
  * *Cách khắc phục:* Luôn thêm tiền tố `sudo` trước câu lệnh Nmap (`sudo nmap ...`) và nhập mật khẩu quản trị để cấp quyền tạo gói tin thô.
* **Lỗi 3: Thời gian quét dịch vụ `-sV` kéo dài nếu quét toàn bộ cổng**
  * *Mô tả:* Quét toàn bộ 65.535 cổng với tham số `-sV` tốn rất nhiều thời gian chờ đợi phản hồi banner.
  * *Cách khắc phục:* Tập trung quét top 1000 cổng mặc định phổ biến của Nmap hoặc lọc theo danh sách cổng trọng yếu bằng tham số `-p` để tối ưu thời gian quay video và ghi nhận kết quả nhanh chóng.
