

## 1. Role

Backend API contributor + tester.
Phụ trách thiết kế, triển khai và test các API chính của hệ thống.

---

## 2. Đóng góp cụ thể

* Thiết kế và triển khai các endpoint:

  * `/api/vehicles`
  * `/api/vehicles/{vehicle_id}`
  * `/api/chat`
  * `/api/booking/confirm`
  * `/api/bookings`
  * `/api/booking/{id}`

* Xây dựng **request/response model** đảm bảo rõ ràng và dễ sử dụng

* Chuẩn hóa **format response backend** (status, message, data) để frontend và agent dùng ổn định

* Triển khai **error handling HTTP**:

  * Xử lý các lỗi phổ biến (400, 404, 500)
  * Trả về message rõ ràng, nhất quán

* Cấu hình **CORS** để frontend có thể gọi API an toàn

* Sử dụng **lifespan** để quản lý vòng đời ứng dụng (init resource, cleanup)

* Viết **integration test cho API**:

  * Test end-to-end các endpoint chính
  * Kiểm tra request/response đúng format
  * Validate các case lỗi (invalid input, missing data, not found)

---

## 3. SPEC mạnh/yếu

* **Mạnh nhất:** hệ thống API có cấu trúc rõ ràng, response được chuẩn hóa giúp frontend tích hợp dễ dàng
* **Yếu nhất:** chưa có nhiều test cho edge cases phức tạp (race condition, concurrent booking)

---

## 4. Đóng góp khác

* Hỗ trợ debug khi frontend gặp lỗi gọi API
* Phối hợp với team để đảm bảo contract giữa frontend – backend nhất quán

---

## 5. Điều học được

* Hiểu rõ hơn về cách thiết kế API theo hướng **production-ready**
* Nhận ra việc chuẩn hóa response quan trọng không kém logic xử lý
* Integration test giúp phát hiện lỗi sớm hơn so với test thủ công

---

## 6. Nếu làm lại

* Viết test song song với lúc phát triển API (test-driven hoặc test sớm hơn)
* Bổ sung test cho các tình huống concurrent (đặt lịch cùng lúc)
* Tách module rõ hơn để dễ maintain và scale

---

## 7. AI giúp gì / AI sai gì

* **Giúp:**

  * Gợi ý cấu trúc API và format response chuẩn
  * Hỗ trợ viết nhanh test cases

* **Sai/mislead:**

  * Một số gợi ý over-engineering (thêm nhiều layer không cần thiết cho scope hackathon)

→ Bài học: cần chọn lọc và điều chỉnh giải pháp AI cho phù hợp scope thực tế.

---
