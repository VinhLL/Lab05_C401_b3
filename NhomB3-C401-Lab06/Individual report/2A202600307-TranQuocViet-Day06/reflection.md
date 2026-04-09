# Individual Reflection — Trần Quốc Việt (2A202600307)

## 1. Role
Flow Designer + Data Architect. Phụ trách thiết kế luồng hoạt động của chatbot, xác định cấu trúc dữ liệu trao đổi giữa frontend–backend, và phân tích failure modes.

## 2. Đóng góp cụ thể
- Thiết kế 4 nhánh conversation chính: tra cứu bảo hành, chẩn đoán xe, tìm xưởng, đặt lịch. Luồng đặt lịch dùng mô hình **PENDING → CONFIRMED với countdown 5 phút** để tránh ghost booking.
- Xác định schema cho API response (`reply`, `tool_calls_log`, `booking`) và state management phía client (`selectedVehicleId`, `messages[]`, `pendingBookings{}`).
- Thiết kế logic phân loại trạng thái xe 3 mức (good/warning/error) dựa trên tổ hợp `error_count` và `battery_soh_percent` — để AI không phải tự suy luận lại mỗi lần.
- Xác định các failure modes chính và đề xuất mitigation (xem mục 3).

## 3. SPEC mạnh/yếu
**Mạnh nhất: failure modes** — nhóm phát hiện được một số edge case thực tế: booking hết hạn giữa chừng do mạng chậm, session corrupt sau reload, user quên chọn xe trước khi hỏi. Đều có mitigation cụ thể trong code.

**Yếu nhất:** race condition khi user bấm xác nhận đúng lúc countdown về 0 — client có thể gửi request với booking đã expired mà không biết. Phát hiện muộn, chưa fix kịp trước demo.

## 4. Điều học được
Trước dự án nghĩ schema là việc của backend. Làm xong mới thấy **frontend state và API contract phải thống nhất từ ngày đầu**. Ví dụ nhỏ: đưa `ttl_seconds` vào booking response thay vì hardcode 300s ở client — backend có thể đổi thời gian giữ chỗ mà không cần redeploy frontend. Một field nhỏ nhưng là product decision thực sự.

Bài học thứ hai: **lỗi nguy hiểm nhất là lỗi silent** — booking expired ở server nhưng UI vẫn báo thành công vì không validate response kỹ. Crash dễ debug hơn nhiều.

## 5. Nếu làm lại
Sẽ chốt API contract vào cuối ngày 1 trước khi ai viết code. Lần này thống nhất schema khá muộn nên `app.js` và `app_rewritten.js` không consistent — version rewritten bị mất cột `service_type` trong bảng lịch hẹn. Một file `api-contract.md` từ sớm là đủ để tránh chuyện này.

## 6. AI giúp gì / AI sai gì
**Giúp:** Claude brainstorm failure modes khá tốt — gợi ý race condition countdown vs confirm click mà nhóm chưa nghĩ ra. Cũng giúp validate logic 3 mức trạng thái xe bằng cách liệt kê đủ các tổ hợp có thể xảy ra.

**Sai/mislead:** Claude đề xuất dùng WebSocket để sync trạng thái booking real-time — về mặt kỹ thuật đúng nhưng hoàn toàn over-engineered cho hackathon. Bài học: AI không biết scope và deadline, mình phải tự biết dừng.
