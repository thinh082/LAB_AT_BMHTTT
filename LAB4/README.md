# BÁO CÁO THỰC HÀNH LAB 4 - NMAP

## 👤 THÔNG TIN SINH VIÊN
* **Họ và tên:** Nguyễn Phước Thịnh
* **MSSV:** 1150070040
* **Lớp:** 11TMDT
* **Môn học:** An toàn hệ thống thông tin * **Link Video YouTube:** *https://youtu.be/NjXfHiKdLzA*

---

## MÔI TRƯỜNG THỰC HÀNH (HOST-ONLY)
* **Máy quét (Kali Linux):** `192.168.119.129` (User: `nguyenphuocthinh`)
* **Máy đích (Metasploitable 2):** `192.168.119.128` (MAC: `00:0C:29:43:9B:41`)
* **Dải mạng Subnet:** `192.168.119.0/24`

---

## CÁC CÔNG VIỆC ĐÃ LÀM ĐƯỢC
1. **Kiểm tra thông mạng & Dò quét Host (Host Discovery):**
   * Kiểm tra thông suốt kết nối giữa Kali và Metasploitable 2 (`ping -c 4 192.168.119.128`).
   * Dò tìm toàn bộ thiết bị đang hoạt động trong dải mạng (`sudo nmap -sn 192.168.119.0/24`).
2. **Khảo sát các kỹ thuật quét cổng TCP:**
   * Thực hiện TCP Connect Scan (`nmap -sT`) hoàn tất bắt tay 3 bước TCP.
   * Thực hiện TCP SYN Stealth Scan (`sudo nmap -sS`) bắt tay nửa mở, phát hiện 23 cổng mở.
3. **Nhận diện Dịch vụ, Phiên bản và Hệ điều hành:**
   * Nhận diện chi tiết phần mềm và phiên bản trên các cổng mở (`sudo nmap -sV`).
   * Nhận diện hệ điều hành máy đích qua TCP/IP fingerprint (`sudo nmap -O` $\rightarrow$ Linux 2.6.X).
4. **Mở rộng khảo sát bằng Nmap Scripting Engine (NSE):**
   * Sử dụng script `smb-os-discovery` trích xuất thông tin Computer Name, Workgroup và OS Samba.
5. **Xuất hồ sơ báo cáo đa định dạng & Tạo trang HTML:**
   * Xuất kết quả đồng thời ra 3 định dạng `.nmap`, `.xml`, `.gnmap` bằng tham số `-oA`.
   * Sử dụng công cụ `xsltproc` chuyển đổi tệp XML sang giao diện Web HTML trực quan.

---


