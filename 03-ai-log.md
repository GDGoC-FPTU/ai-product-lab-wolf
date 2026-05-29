# Lab 02 — AI Log & Reflection

**Họ và tên:** [Họ và tên của bạn]
**MSSV:** [MSSV của bạn]

---

## AI đã giúp gì?

Trong buổi Lab này, tôi đã sử dụng AI (Gemini) như một thought-partner trong nhiều giai đoạn:

1. **Brainstorm ý tưởng bài toán:** Tôi đã dùng AI để mở rộng danh sách các pain point vận hành của Vingroup. AI đề xuất các bài toán thực tế như tối ưu hóa điều vận Xanh SM, phân loại khiếu nại Vinhomes, và soạn thảo tóm tắt xuất viện Vinmec.
2. **Viết System Prompt:** AI hỗ trợ tôi viết các chỉ thị ranh giới an toàn (operational boundaries) cho prompt prototype, bao gồm quy tắc [DRAFT_ONLY] và xử lý pin < 5%.
3. **Tìm điểm yếu trong bảo vệ ranh giới:** AI đã giúp tôi thiết kế các adversarial test cases để stress-test hệ thống prompt.
4. **Sửa lỗi code Python:** Khi gặp lỗi import thư viện genai, AI đã hướng dẫn tôi cách cài đặt và sử dụng đúng SDK.

## AI đã sai gì?

AI đã đưa ra một số gợi ý sai lệch:

1. **Hallucination về quy trình:** AI gợi ý rằng Xanh SM có sẵn API real-time cho tất cả trạm sạc VinFast với độ trễ dưới 100ms. Trên thực tế, hệ thống trạm sạc của VinFast hiện tại vẫn đang trong quá trình xây dựng API đồng bộ, và nhiều trạm sạc chưa có dữ liệu trụ trống theo thời gian thực.
2. **Đề xuất giải pháp quá phức tạp:** AI ban đầu đề xuất sử dụng Multi-Agent architecture (Planning Agent + Execution Agent + Validation Agent) trong khi bài toán thực tế chỉ cần một LLM Feature đơn giản với một prompt duy nhất.
3. **Viết prompt bypass được ranh giới:** Khi tôi yêu cầu AI tự tấn công prompt của chính nó, AI đã viết một prompt injection bypass thành công bằng cách giả dạng "tài xế đang gặp nguy hiểm" và yêu cầu bỏ qua [DRAFT_ONLY].

## Tôi đã sửa đổi ra sao?

Để khắc phục các vấn đề trên, tôi đã:

1. **Kiểm tra chéo thông tin:** Tôi đã xác minh lại qua kiến thức thực tế về hệ thống VinFast và điều chỉnh kỳ vọng về dữ liệu đầu vào cho phù hợp. Tôi cập nhật giả định API thành "một số trạm có API, một số cần nhập tay".
2. **Đơn giản hóa kiến trúc:** Tôi quyết định chỉ dùng LLM Feature thay vì Multi-Agent, vì bài toán là deterministic (vị trí + trạm trống -> soạn tin). Rule-based routing cho các trường hợp đơn giản hóa.
3. **Tăng cường ranh giới an toàn:** Tôi đã bổ sung thêm các quy tắc bảo vệ:
   - Kiểm tra từ khóa "khẩn cấp" / "nguy hiểm" - nếu có, tự động gắn tag [URGENT] và yêu cầu Dispatch xử lý ngay.
   - Thêm quy tắc: AI không được phép thay đổi format output dù người dùng có yêu cầu thế nào.
   - Luôn giữ thẻ [DRAFT_ONLY] bất kể yêu cầu từ người dùng.
