# Individual reflection

## 1. Role
Backend/agent engineer + product/ROI. Phụ trách flow LangGraph `agent -> tools -> agent`, prompt/system message, clarification logic, các flow booking ngắn và phần ROI 3 kịch bản cho VinBot.

## 2. Đóng góp cụ thể
- Thiết kế và hoàn thiện flow LangGraph theo vòng `agent -> tools -> agent`, thêm routing rõ ràng sau mỗi lượt tool và recursion guard để tránh loop khi agent xử lý nhiều bước.
- Viết và siết `SYSTEM_PROMPT` cùng runtime context để agent chỉ trả lời trong scope warranty/diagnostics/booking, không over-promise policy và không xác nhận booking khi backend chưa trả về trạng thái hợp lệ.
- Xây clarification logic gồm 3 lớp: topic clarification khi user chỉ nói tên xe, booking clarification theo thứ tự location -> center -> date/time, và out-of-scope rejection cho các câu hỏi như giá bán, khuyến mãi, trả góp, thông số.
- Làm flow xác nhận booking ngắn kiểu `ok/xác nhận` và flow chọn slot ngắn kiểu `14h`, để user không phải nhập lại đầy đủ nhưng hệ thống vẫn map đúng slot vừa được đề xuất.
- Hoàn thiện flow reschedule để bám đúng booking cũ, giữ đúng `service_type`, không lấy nhầm slot cũ khi user đổi xưởng/đổi thời gian, và chỉ đổi lịch khi có `slot_id` thật.
- Viết phần ROI 3 kịch bản `conservative / realistic / optimistic`, gồm assumption, cost, benefit, net và kill criteria để nhóm có phần product reasoning rõ hơn chứ không chỉ có demo kỹ thuật.

## 3. SPEC mạnh/yếu
- Mạnh nhất: phần trust, failure mode và precision-first khá chặt. Nhóm xác định rõ các rủi ro nguy hiểm như trả sai policy, tự chọn slot cho user hoặc confirm ảo, rồi gắn luôn guardrail và metric tương ứng.
- Yếu nhất: ROI vẫn còn dựa trên mock data và assumption nội bộ. 3 kịch bản đã tách quy mô khá rõ, nhưng benefit như giảm hotline hay booking uplift vẫn chưa có baseline thật nên mới phù hợp mức hackathon hơn là quyết định rollout thật.

## 4. Đóng góp khác
- Bổ sung logic xử lý relative date/context để agent hiểu các cách nói như `mai`, `thứ 7 tuần này`, rồi đổi về ngày cụ thể trước khi đi vào booking flow.
- Làm fallback khi graph bị recursion/error và final reply extraction để backend luôn trả ra một câu trả lời cuối rõ ràng cho frontend thay vì dừng ở giữa tool chain.
- Siết lại các case test quan trọng cho clarification, slot selection, confirm booking, reschedule và persistence để giảm regression khi sửa logic agent.

## 5. Điều học được
Trước khi làm bài này phần khó nhất của AI agent là gọi đúng tool. Làm rồi mới thấy phần khó hơn là ép agent đi đúng trình tự: khi nào phải hỏi lại, khi nào được gọi tool, khi nào phải từ chối và khi nào phải dừng để không tự suy đoán thay user. Với bài toán policy và booking, một câu trả lời sai nhưng nghe rất tự tin nguy hiểm hơn một lượt clarify thêm. Vì vậy recursion guard, booking rule và precision-first thực ra là product decision chứ không chỉ là implementation detail.

## 6. Nếu làm lại
Log user correction và booking funnel sớm hơn. Hiện tại đã có `messages`, `tool_calls_log`, `booking`, nhưng nếu từ đầu tách riêng các case đổi xe, đổi location, đổi slot, reject/out-of-scope và booking expire thì phần learning signal lẫn ROI sẽ thuyết phục hơn nhiều. Benchmark latency sớm hơn để biết flow nào đang tốn quá nhiều turn không cần thiết.

## 7. AI giúp gì / AI sai gì
- **Giúp:** AI hỗ trợ brainstorm guardrail, wording cho system prompt và gợi ý thêm edge case hội thoại ngắn như `ok`, `14h`, `xe này`, giúp chuyển nhanh thành rule và test cụ thể.
- **Sai/mislead:** AI có xu hướng quá "helpful", dễ tự suy đoán location, slot hoặc trả lời ra ngoài scope nhưng vẫn nghe hợp lý. Nếu không khóa bằng prompt, routing và test thì agent sẽ trông mượt nhưng sai. Bài học là dùng AI rất tốt để nghĩ case, nhưng quyết định cuối vẫn phải dựa vào state machine, tool result và constraint rõ ràng.
