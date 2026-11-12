# Bài làm: Context Window và hiện tượng Truncation

## Trả lời thắc mắc của nhân viên

AI không "quên" theo kiểu trí nhớ ngắn hạn của con người. Phiên chat có một **bộ nhớ làm việc giới hạn** (Context Window). Khi bộ nhớ đầy, thông tin cũ nhất bị đẩy ra khỏi bộ nhớ, và Lượt 1 chính là thông tin cũ nhất.

---

## Phần 1: Hình tượng "Chiếc cốc nước"

| Hình ảnh ẩn dụ | Ý nghĩa kỹ thuật |
|----------------|------------------|
| **Chiếc cốc** | Context Window (dung lượng RAM giới hạn, tính bằng token) |
| **Nước** | Token của toàn bộ cuộc hội thoại (câu hỏi của khách + câu trả lời của AI) |
| **Rót thêm nước vào cốc đầy** | Thêm token mới khi Context Window đã đầy |
| **Nước tràn ra** | Truncation: dữ liệu cũ nhất bị cắt bỏ |

**Áp dụng vào tình huống của MartX:**

1. **Lượt 1**: khách báo tên "Trần Văn B", đơn #MTX-9988. Đây là lượng nước đầu tiên, nằm ở **đáy cốc**.
2. **Lượt 2-41**: tư vấn rau củ, AI trả lời dông dài, dồn khoảng 32.000 token. Nước dâng liên tục đến miệng cốc.
3. **Lượt 42**: cốc đã đầy 100%. Mỗi token mới rót vào buộc phải đẩy bớt nước cũ ở đáy ra ngoài. Nên **Lượt 1 (tên và mã đơn) bị cắt gọt (Truncation)**.
4. **Hậu quả**: AI không còn thấy thông tin Lượt 1, nên hỏi lại mã đơn và gọi nhầm khách là "chị Mai" (suy đoán, bịa ra tên).

> **Lưu ý quan trọng:** AI không có ký ức bền vững. Nó chỉ "nhìn thấy" những gì đang nằm trong cốc ở thời điểm trả lời. Thông tin đã bị đẩy ra khỏi cốc thì với AI là **không tồn tại**.

---

## Phần 2: Hai phương án quản lý RAM (tránh tràn cốc)

### Phương án 1: Thao tác người dùng (nhân viên CSKH)

- **Chia nhỏ phiên chat**: mỗi chủ đề hoặc mỗi khách hàng mở một cuộc chat mới, không kéo 45 lượt trong một phiên.
- **Nhắc lại thông tin cốt lõi (re-anchor)**: định kỳ dán lại dữ liệu quan trọng ở đầu prompt, ví dụ: *"Khách: Trần Văn B, đơn #MTX-9988, MartX Q1. Câu hỏi: đơn có được miễn phí hoàn tiền không?"*
- **Yêu cầu AI trả lời ngắn gọn** để giảm lượng token tiêu hao mỗi lượt.
- **Tóm tắt rồi mở chat mới**: nhờ AI tóm tắt ý chính sau khoảng 15-20 lượt, rồi dán bản tóm tắt vào phiên mới.

### Phương án 2: Thiết kế hệ thống

- **Tách thông tin cố định ra khỏi lịch sử hội thoại**: lưu tên, mã đơn trong hệ thống CRM/cơ sở dữ liệu, rồi tự động chèn vào đầu mỗi prompt (system prompt) để không bao giờ bị cắt.
- **Tự động tóm tắt hội thoại (Summarization)**: hệ thống nén các lượt cũ thành bản tóm tắt ngắn, chỉ giữ nguyên văn các lượt gần nhất.
- **Truy xuất theo nhu cầu (RAG)**: tra cứu đơn hàng và chính sách từ cơ sở dữ liệu khi cần, thay vì nhét hết vào cuộc chat.
- **Cảnh báo ngưỡng**: hiển thị cảnh báo hoặc tự mở phiên mới khi dung lượng đạt khoảng 70-80%.

---

