# Báo Cáo Đánh Giá Kiểm Thử Hiệu Năng 

**Công cụ thực hiện:** Apache JMeter  
**Mục tiêu tổng quát:** Đánh giá hiệu năng hoạt động và giới hạn chịu tải của ứng dụng Web cũng như hệ thống API thông qua kỹ thuật giả lập lượng lớn người dùng truy cập đồng thời.

---

## Phần 1: Kiểm Thử Hiệu Năng Ứng Dụng Web (Web Performance Testing)

### 1. Mục tiêu kiểm thử
* Thiết lập kịch bản trên JMeter nhằm mô phỏng truy cập đồng thời từ nhiều người dùng vào chuyên mục bài viết mới của nền tảng công nghệ: `https://viblo.asia/newest`.
* Thực thi kịch bản, thu thập các chỉ số hiệu năng thông qua các Listener.
* Phân tích các số liệu đo lường (thời gian phản hồi, thông lượng, tỷ lệ lỗi) để đánh giá năng lực đáp ứng của máy chủ.

### 2. Cấu hình kịch bản (Test Plan)
* **Tham số Thread Group (Nhóm người dùng ảo):**
    * **Number of Threads:** 150 (Giả lập 150 người dùng đồng thời).
    * **Ramp-up period:** 15 giây (Trung bình kích hoạt 10 người dùng/giây cho đến khi đạt mức 150).
    * **Loop Count:** Infinite (Vòng lặp vô hạn).
    * **Duration:** 60 giây.
* **Cấu hình HTTP Request:**
    * **Giao thức:** HTTPS
    * **Server Name:** `viblo.asia`
    * **Path:** `/newest`
    * **Phương thức:** GET
    * **Mã hóa:** UTF-8
* **Công cụ thu thập dữ liệu (Listeners):** View Results Tree, Aggregate Report.

### 3. Kết quả đo lường
> <img width="1516" height="841" alt="image" src="https://github.com/user-attachments/assets/b2f2faca-0f7d-40da-aac1-da1b5b8ae135" />
Hình 1: Chi tiết yêu cầu trong View Results Tree
> <img width="1515" height="850" alt="image" src="https://github.com/user-attachments/assets/ecc2700b-6432-4e0c-ba9e-1a58dc4a8ccf" />
Hình 2: Bảng thống kê Aggregate Report

### 4. Phân tích số liệu
* **Tổng số yêu cầu (Samples):** 36,666
* **Tỷ lệ thành công:** 15.94%
* **Tỷ lệ thất bại (Error %):** 84.06%
* **Thời gian phản hồi trung bình (Average):** 214 ms
* **Thời gian phản hồi trung vị (Median):** 118 ms
* **Phân vị 90 (90th Percentile):** 464 ms
* **Thông lượng (Throughput):** ~601.6 yêu cầu/giây

### 5. Đánh giá và Kết luận
Dựa trên các chỉ số thu thập được, hệ thống máy chủ của `viblo.asia` không đáp ứng trọn vẹn mức tải giả lập khổng lồ (hơn 36,000 yêu cầu trong vòng 60 giây). Tỷ lệ lỗi trả về lên tới **84.06%**, chứng tỏ phần lớn các luồng truy cập đã bị từ chối. 

Tuy nhiên, việc thời gian phản hồi trung bình vẫn duy trì ở mức thấp (214 ms) đi kèm thông lượng cao cho thấy máy chủ không rơi vào trạng thái quá tải hay sập nguồn (crash). Thay vào đó, hệ thống đã chủ động kích hoạt các cơ chế bảo mật (như Rate Limiting hoặc Anti-DDoS) để chặn các yêu cầu bất thường xuất phát từ một địa chỉ IP duy nhất (IP chạy JMeter). Kết luận, trang web sở hữu cơ chế phòng vệ hệ thống rất tốt và hoạt động đúng thiết kế dưới áp lực tấn công ép tải.

---

## Phần 2: Kiểm Thử Hiệu Năng API (API Performance Testing)

### 1. Mục tiêu kiểm thử
* Sử dụng JMeter để tiến hành ép tải (Stress Test) một RESTful API cung cấp danh sách người dùng: `https://reqres.in/api/users?page=2`.
* Đánh giá tốc độ truy xuất và khả năng phản hồi dữ liệu định dạng JSON của hệ thống API Gateway.
* Xác định mức độ tin cậy và giới hạn chịu tải của API.

### 2. Cấu hình kịch bản (Test Plan)
* **Tham số Thread Group:**
    * **Number of Threads:** 300 (Gia tăng số lượng luồng nhằm tối đa hóa áp lực lên hệ thống).
    * **Ramp-up period:** 10 giây.
    * **Loop Count:** Infinite.
    * **Duration:** 60 giây.
* **Cấu hình HTTP Request:**
    * **Giao thức:** HTTPS
    * **Server Name:** `reqres.in`
    * **Path:** `/api/users?page=2`
    * **Phương thức:** GET
    * **Mã hóa:** UTF-8
* **Công cụ thu thập dữ liệu (Listeners):** View Results Tree, Aggregate Report.

### 3. Kết quả đo lường
> <img width="1507" height="850" alt="image" src="https://github.com/user-attachments/assets/ce0b8649-6edb-4b0b-8acf-f0385b4e3e46" />
Hình 3: Kết quả JSON trong View Results Tree
> <img width="1513" height="852" alt="image" src="https://github.com/user-attachments/assets/6e54c360-44a2-4590-a5a8-e1a5893a554e" />
Hình 4: Bảng thống kê Aggregate Report

### 4. Phân tích số liệu
* **Tổng số yêu cầu (Samples):** 83,314
* **Tỷ lệ thành công:** 0%
* **Tỷ lệ thất bại (Error %):** 100%
* **Thời gian phản hồi trung bình (Average):** 197 ms
* **Thời gian phản hồi trung vị (Median):** 189 ms
* **Phân vị 90 (90th Percentile):** 347 ms
* **Thông lượng (Throughput):** ~1,379.2 yêu cầu/giây

### 5. Đánh giá và Kết luận
Kịch bản kiểm thử API với 300 người dùng ảo trả về **tỷ lệ lỗi tuyệt đối (100%)**. Toàn bộ 83,314 yêu cầu gửi đến đều bị hệ thống từ chối. 

Phân tích sâu hơn cho thấy, nguyên nhân cốt lõi không nằm ở việc máy chủ bị quá tải (downtime). Khối lượng thông lượng sinh ra là quá lớn (hơn 1,379 request/giây), khiến API Gateway của `reqres.in` ngay lập tức nhận diện đây là hành vi spam/DDoS và ngắt kết nối chặn đứng mọi request. Minh chứng rõ rệt nhất là thời gian phản hồi trung bình chỉ ở mức **197 ms** — nghĩa là máy chủ đã từ chối yêu cầu ngay tại lớp bảo mật ngoài cùng mà chưa cần tốn tài nguyên để truy xuất cơ sở dữ liệu. Nhìn chung, hệ thống API này áp dụng quy định Rate Limit cực kỳ nghiêm ngặt và việc thực hiện Stress Test với cường độ cao như vậy là không khả thi nếu thiếu quyền whitelist từ quản trị viên.
