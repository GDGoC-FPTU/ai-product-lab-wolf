# Lab 02 — Deep-Dive Report (Nhóm)

**Tên nhóm:** AI Product Lab - Wolf

**Thành viên:**
1. [Họ và tên] - [MSSV]
2. [Họ và tên] - [MSSV]
3. [Họ và tên] - [MSSV]

---

## 🗳️ Quyết định lựa chọn bài toán

**Bài toán được chọn:** Xanh SM Xử lý sự cố sạc pin thực địa

### Lý do lựa chọn:
- **Tác động thực tế cao:** Mỗi ngày có ~80 sự cố pin tại Hà Nội, gây lãng phí 20 giờ làm việc/ngày.
- **Ranh giới rõ ràng:** Bài toán có thể được kiểm soát chặt chẽ qua HITL (Human-in-the-loop).
- **AI Fit phù hợp:** LLM Feature đủ giải quyết mà không cần Agent phức tạp.

### Lý do loại bỏ các thẻ khác:
- **Card #2 (Vinhomes CSKH):** Rủi ro sai sót thông tin liên quan đến phí quản lý, tranh chấp căn hộ.
- **Card #3 (Vinmec Discharge Summary):** Nhạy cảm y tế, cần nhiều dữ liệu huấn luyện hơn.

---

## 🏗️ Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow

```
Quy trình xử lý sự cố hết pin thực địa hiện tại:

┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ Tra cứu định │     │ Tra cứu trạm │     │ Soạn văn bản │
│ gọi sự cố    │ ──→ │ vị GPS xe   │ ──→ │ sạc VinFast  │ ──→ │ hướng dẫn    │
│              │     │              │     │ còn trụ trống│     │ gửi tài xế   │
│ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │
│ ⏱ 2 phút     │     │ ⏱ 2 phút     │     │ ⏱ 5 phút 🔴  │     │ ⏱ 5 phút 🔴  │
│ In: Điện thoại│     │ In: Biển số  │     │ In: Vị trí GPS│     │ In: Raw data │
│ Out: Log sự cố│     │ Out: Toạ độ  │     │ Out: Địa chỉ │     │ Out: SMS     │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Gọi xe cứu   │
                                                               │ hộ (nếu cần) │
                                                               │ Ai: Dispatch │
                                                               │ ⏱ 1 phút     │
                                                               └──────────────┘

🔴 = Bottlenecks
⏱ Tổng thời gian xử lý thủ công: 15 phút/lượt.
```

### 3.2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Xanh SM. |
| **2. Current Workflow** | Khi tài xế báo hết pin, điều phối viên tra cứu vị trí định vị trên bản đồ nội bộ, mở Dashboard trạm sạc VinFast để tìm trụ sạc trống gần nhất, viết tin nhắn chỉ dẫn/định vị gửi qua App tài xế, và gọi cứu hộ nếu pin dưới 5%. 5 bước, hoàn toàn thủ công, mất 15 phút/lượt. |
| **3. Bottleneck** | Bước 3 & 4 (mất 10 phút): Tra cứu thủ công trụ sạc trống phù hợp với dòng xe (VF5/VFe34/VF8) và soạn thảo tin nhắn hướng dẫn đường đi chi tiết bằng Tiếng Việt thân thiện. |
| **4. Business Impact** | Mỗi ngày có ~80 sự cố pin thực địa tại Hà Nội. Gây lãng phí 20 giờ làm việc/ngày của team điều vận. Tăng thời gian chờ đợi của tài xế, dẫn đến rò rỉ doanh thu ~15% do xe không thể đón khách. |
| **5. Success Metric** | 1. Giảm tổng thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút (Efficiency).<br>2. Tỉ lệ hướng dẫn đúng địa điểm và đúng loại trụ sạc phù hợp đạt 98% (Quality). |
| **6. Operational Boundary** | AI được phép truy xuất API định vị xe, API trạm sạc VinFast trống, tự động soạn thảo tin nhắn hướng dẫn dạng nháp (draft). **CẤM:** AI không được tự động gửi tin đi mà không có điều phối viên phê duyệt (Bắt buộc HITL); không được đề xuất trạm sạc không phù hợp với loại cổng sạc của xe. |

### 3.3. Future-State Flow & AI Fit

**AI Fit:** LLM Feature (không cần Agent tự trị vì quy trình có cấu trúc cố định, rủi ro khi điều phối sai trạm sạc có thể khiến xe cạn kiệt pin giữa đường).

```
Quy trình tương lai (Future-State):

┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ 🔵 Auto-pull │     │ 🔵 AI draft  │     │ 🟢 Dispatch  │
│ gọi sự cố    │ ──→ │ vị trí &     │ ──→ │ SMS chỉ dẫn  │ ──→ │ click duyệt  │
│              │     │ trạm sạc trống│     │ & chỉ đường  │     │ & gửi tài xế │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ↩️ Fallback:
                                                               Nếu AI draft lỗi,
                                                               Dispatcher tự viết
                                                               tay lại như cũ.
```

**Chú thích:**
- 🔵 **AI Step:** LLM tự động lấy vị trí GPS, tra cứu trạm sạc trống, soạn thảo tin nhắn hướng dẫn. Nếu pin < 5% và trạm gần nhất > 5km -> tự động đề xuất điều xe cứu hộ pin di động.
- 🟢 **Human Step (HITL):** Điều phối viên kiểm tra và click phê duyệt trước khi gửi.
- ↩️ **Fallback:** Nếu AI trả về lỗi hoặc không tự tin, điều phối viên tự viết tay như quy trình cũ.

---

## 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist

1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? (Có dữ liệu GPS và trạm sạc VinFast có sẵn)
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? (Có, tất cả tin nhắn đều qua duyệt)
3. [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? (Có, đội ngũ điều vận đã phản ánh mong muốn tự động hóa)

### Quyết định cuối cùng

[x] **GO (Bắt đầu xây dựng Prototype)**

**Justification:** Bài toán có scope hẹp, rõ ràng, dữ liệu đầu vào có sẵn (GPS + API trạm sạc). LLM Feature là giải pháp đơn giản, chi phí thấp (Gemini 2.5 Flash ~$0.15/1K request), ranh giới an toàn được kiểm soát chặt chẽ qua HITL. Rủi ro thấp vì luôn có fallback manual. Metric đo lường cụ thể (giảm từ 15 phút xuống < 3 phút), khả thi trong vòng 2-4 tuần phát triển prototype.
