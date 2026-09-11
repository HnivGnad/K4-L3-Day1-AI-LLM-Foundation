# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> temperature càng cao, câu trả lời càng “ngẫu nhiên” và sáng tạo, nhưng càng dễ mất độ chắc chắn và tính kiểm soát.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature = 0.2 hoặc 0.3 cho chatbot hỗ trợ khách hàng, vì chatbot này cần trả lời an toàn, rõ ràng, nhất quán và ít “bịa” thông tin. Với hỗ trợ khách hàng, câu trả lời phải đúng ngữ cảnh, không mơ hồ, và không làm sai lệch thông tin chính thức. Temperature thấp giúp model chọn câu trả lời có kiểm soát hơn, còn tăng temperature sẽ làm chatbot dễ sáng tạo nhưng cũng dễ sai hoặc lạc đề.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Với workload 10.000 người dùng/ngày, mỗi người 3 lượt gọi, mỗi lượt 350 token đầu ra, tổng output token mỗi ngày là 10.500.000 token. Theo bảng giá, GPT-4o output là 0.010 USD/1K token và GPT-4o-mini output là 0.0006 USD/1K token. Tỷ lệ giá là 0.010 / 0.0006 = 16.67, nên GPT-4o đắt hơn GPT-4o-mini khoảng 16,7 lần. Một trường hợp nên dùng GPT-4o là khi cần trả lời chuyên sâu, multi-step hoặc phân tích nghiệp vụ phức tạp; còn GPT-4o-mini phù hợp với triage nhanh, tóm tắt, FAQ và phản hồi đơn giản hàng loạt.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Với system prompt “giáo viên tiểu học”, phản hồi sẽ ngắn, dễ hiểu và có ví dụ quen thuộc. Với system prompt “chuyên gia tài chính”, phản hồi sẽ dài hơn, dùng thuật ngữ kỹ thuật và tập trung vào cơ chế phân tán, xác thực và hợp đồng thông minh. System prompt thay đổi cách model định hình vai trò, nên cùng một câu hỏi sẽ có phản hồi khác nhau về độ dài, từ vựng và ví dụ.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Hai con số chênh nhau khoảng 5%. Sự khác biệt này xuất hiện vì tiktoken đếm theo token mã hóa BPE, không phải đếm từ theo cách thủ công, và tiếng Việt có nhiều dấu, từ ghép, chữ cái có dấu Unicode nên thường tốn nhiều token hơn tiếng Anh cùng độ dài.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi người dùng cần thấy phản hồi ngay lập tức, ví dụ chatbot tương tác, trợ lý viết nội dung, hay hỏi đáp có nhiều bước. Với streaming, model trả từng chunk, UI hiển thị chữ theo kiểu real-time, tăng cảm giác nhanh và tương tác. Non-streaming phù hợp hơn khi cần toàn bộ câu trả lời trước khi hiển thị, ví dụ báo cáo, tóm tắt, email, hay khi hệ thống cần kiểm tra chất lượng hoặc xử lý response hoàn chỉnh trước khi xuất ra.



### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff làm tăng thời gian chờ theo cấp số nhân sau mỗi lần thất bại, ví dụ 0.1s, 0.2s, 0.4s, 0.8s… nên giảm áp lực lên API khi hệ thống đang quá tải. Nếu hàng nghìn client cùng retry với delay cố định giống nhau, họ sẽ đồng loạt gửi lại request cùng lúc, gây ra “thứ tự định thời” và làm nút API càng bị tắc hơn, dễ dẫn đến hiện tượng thảm họa đồng loạt.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Tôi chọn persona là “trợ lý học tập tiếng Việt thân thiện, trả lời ngắn gọn, rõ ràng và đúng ngữ cảnh”. System prompt của tôi sẽ là: “Bạn là trợ lý học tập tiếng Việt thân thiện, trả lời ngắn gọn, rõ ràng và luôn dùng tiếng Việt. Hãy giải thích bằng ví dụ ngắn, ưu tiên tính đúng và dễ hiểu.” Tôi yêu cầu “trả lời ngắn gọn” để tránh câu trả lời quá dài và tập trung vào ý chính; tôi cũng chỉ định “dùng tiếng Việt” để đảm bảo phản hồi phù hợp với người học Việt Nam. Đôi khi tôi thêm “ưu tiên tính đúng và dễ hiểu” vì trợ lý học tập cần vừa chính xác vừa thân thiện.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất của trợ lý là không có bộ nhớ dài hạn, nên nó chỉ nhớ 3 lượt gần nhất trong history và quên ngữ cảnh cũ. Tôi đề xuất thêm một “memory store” lưu trữ thông tin người dùng theo thời gian, ví dụ lưu vào file JSON hoặc DB như SQLite, rồi mỗi lần hội thoại mới sẽ lấy lại ngữ cảnh quan trọng và cập nhật lịch sử. Cách triển khai là thêm một memory dict hoặc bảng memory gồm chủ đề, ý chính và thời gian, rồi ghép các thông tin đó vào messages trước khi gọi API.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
