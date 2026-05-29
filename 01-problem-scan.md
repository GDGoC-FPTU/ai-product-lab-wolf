# Lab 02 — Problem Scan & Quick Cards (Cá nhân)

---

## 🔍 Phase 1 — SCAN

### Danh sách bài toán:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | **Xanh SM** | Lặp lại | So khớp và phân bổ lại cuốc xe khi khách hàng yêu cầu thay đổi điểm đến giữa chừng. |
| 2 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công các phản hồi khẩn cấp từ tài xế về sự cố sạc pin hoặc va chạm thực địa (mất 15-20 min/lượt). |
| 3 | **VinFast** | Lặp lại | So khớp hóa đơn sạc điện và đối chiếu số liệu trạm sạc đối tác hằng tuần. |
| 4 | **Vinhomes** | AI-upgrade | Hệ thống phân loại và route tự động các phản hồi/khiếu nại của cư dân trên App Vinhomes Resident (CSKH phản hồi rập khuôn, mất 12 tiếng). |
| 5 | **Vinmec** | Pain từ người khác | Bác sĩ mất quá nhiều thời gian viết tóm tắt hồ sơ xuất viện (mất 20-30 phút/bệnh nhân, bác sĩ phàn nàn vì quá tải). |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

### Card #1 — Xanh SM: Xử lý sự cố sạc pin thực địa

```
QUICK PROBLEM CARD #1

Bài toán: Tài xế Xanh SM báo cáo sự cố sạc pin / hết pin
giữa đường cần điều phối cứu hộ hoặc trạm sạc gần nhất.
Công ty thành viên: [x] Xanh SM (GSM)

Ai đang đau? Tài xế (chờ đợi), Điều phối viên (quá tải)

Workflow thủ công hiện tại (5 bước):
  1. Tài xế gọi tổng đài điều vận báo hết pin
  → 2. Điều phối viên tra cứu thủ công vị trí xe trên bản đồ
  → 3. Tra cứu thủ công các trạm sạc VinFast còn trụ trống
  → 4. Viết tin nhắn chỉ dẫn/đường đi gửi qua App tài xế
  → 5. Liên hệ đội xe cứu hộ nếu xe đã cạn kiệt pin

Bước nào tốn nhất? Bước 3-4 (⏱ 12 phút/lượt)
AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4
(Tự động hóa lấy vị trí -> Tra cứu trạm trống -> Draft tin)

Đo thành công bằng gì (Metric có số)?
Giảm thời gian xử lý sự cố từ 15 phút -> dưới 3 phút.

Quick Architecture: [x] LLM Feature
```

### Card #2 — Vinhomes: Phân loại & Điều hướng phản ánh cư dân

```
QUICK PROBLEM CARD #2

Bài toán: Phân loại tự động các khiếu nại cư dân gửi qua
App Vinhomes Resident đến đúng ban quản lý từng tòa nhà.
Công ty thành viên: [x] Vinhomes

Ai đang đau? Nhân viên CSKH Vinhomes (quá tải), Cư dân (chờ lâu)

Workflow thủ công hiện tại (4 bước):
  1. Cư dân gửi phản ánh qua App
  → 2. Nhân viên đọc thủ công nội dung từng phản ánh
  → 3. Nhân viên phân loại và chuyển đến ban quản lý tòa
  → 4. Ban quản lý xử lý và phản hồi lại cư dân

Bước nào tốn nhất? Bước 2-3 (⏱ 8 phút/lượt)
AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3
(AI đọc nội dung + phân loại + route tự động)

Đo thành công bằng gì (Metric có số)?
Giảm thời gian phân loại từ 8 phút -> dưới 30 giây.

Quick Architecture: [x] LLM Feature
```

### Card #3 — Vinmec: Soạn thảo tóm tắt hồ sơ xuất viện

```
QUICK PROBLEM CARD #3

Bài toán: Bác sĩ mất quá nhiều thời gian viết tóm tắt
hồ sơ xuất viện (Discharge Summary) cho bệnh nhân.
Công ty thành viên: [x] Vinmec

Ai đang đau? Bác sĩ điều trị (quá tải hành chính)

Workflow thủ công hiện tại (4 bước):
  1. Bác sĩ thu thập kết quả xét nghiệm, chẩn đoán
  → 2. Mở template và gõ thủ công từng phần
  → 3. Kiểm tra chính tả và thông tin lâm sàng
  → 4. Ký duyệt và lưu vào hồ sơ bệnh án điện tử

Bước nào tốn nhất? Bước 1-2 (⏱ 25 phút/lượt)
AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1-2
(AI trích xuất thông tin lâm sàng + draft bản tóm tắt)

Đo thành công bằng gì (Metric có số)?
Giảm thời gian soạn thảo từ 25 phút -> dưới 5 phút.

Quick Architecture: [x] LLM Feature
```
