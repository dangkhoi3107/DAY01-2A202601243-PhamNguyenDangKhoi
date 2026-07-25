# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> *Khi temperature tăng, câu trả lời có xu hướng đa dạng và sáng tạo hơn. Ở mức 0.0, phản hồi khá ổn định và trực tiếp, còn mức 1.5 cho kết quả khó đoán hơn, đôi khi dài hoặc kém chính xác.*

### Câu 1.2 — Chọn temperature cho sản phẩm
> *Tôi sẽ chọn temperature khoảng 0.2 cho chatbot hỗ trợ khách hàng. Mức này giúp câu trả lời nhất quán, rõ ràng và hạn chế việc model tự thêm thông tin không cần thiết.*

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> *Tổng lượng đầu ra là khoảng 10,5 triệu token mỗi ngày. Nếu chỉ tính token đầu ra, GPT4o tốn khoảng 105 USD/ngày, còn GPT4o-mini khoảng 6,3 USD/ngày, tức GPT4o đắt hơn gần 17 lần. GPT4o phù hợp với yêu cầu phức tạp, cần suy luận và độ chính xác cao; còn mini phù hợp cho FAQ, phân loại hoặc trả lời các câu hỏi đơn giản.*

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> *Model giáo viên tiểu học dùng câu ngắn, từ dễ hiểu và thường đưa ra ví dụ gần gũi như một cuốn sổ được chia sẻ. Model chuyên gia tài chính dài và sử dụng các thuật ngữ như sổ cái phân tán, cơ chế đồng thuận hoặc tài sản số. Điều này cho thấy system prompt có thể điều chỉnh cách diễn đạt, mức độ chi tiết và đối tượng mà model hướng đến.*

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> *Trong lần thử của tôi, đoạn văn gần 100 từ cho khoảng 160 token khi dùng count_tokens, còn cách ước lượng số từ /0.75 cho khoảng 133 token. Hai kết quả chênh nhau gần 20%. Tiếng Việt có dấu và nhiều từ gồm nhiều âm tiết tách nhau bằng khoảng trắng nên tokenizer có thể chia nội dung thành nhiều token hơn.*

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> *Streaming phù hợp nhất với chatbot hoặc các câu trả lời dài vì người dùng có thể đọc nội dung ngay khi model đang tạo, thay vì phải chờ toàn bộ kết quả. Non-streaming phù hợp hơn với phản hồi ngắn, xử lý theo batch hoặc khi chương trình cần nhận đầy đủ kết quả trước khi kiểm tra và lưu dữ liệu.*

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> *Exponential backoff tăng dần thời gian chờ sau mỗi lần thất bại, nhờ đó API có thời gian phục hồi và client không gửi yêu cầu liên tục. Nếu hàng nghìn client đều retry sau đúng một giây, chúng có thể gửi lại cùng lúc và tiếp tục làm hệ thống quá tải. Có thể thêm một khoảng chờ ngẫu nhiên để giảm tình trạng này.*

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> *Tôi chọn persona là một trợ giảng thân thiện của khóa học AI. System prompt của tôi là: "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt." Tôi dùng từ “thân thiện” để phản hồi dễ tiếp cận hơn với người mới học. Yêu cầu “ngắn gọn bằng tiếng Việt” giúp câu trả lời dễ đọc, đúng ngôn ngữ người dùng và không có quá nhiều thông tin dư thừa.*

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> *Hạn chế lớn nhất của trợ lý là chỉ lưu ba lượt hội thoại gần nhất nên có thể quên những thông tin đã trao đổi từ trước. Tôi có thể cải thiện bằng cách tóm tắt các tin nhắn cũ trước khi cắt history, sau đó lưu phần tóm tắt vào một biến hoặc cơ sở dữ liệu. Ở mỗi lần gọi API, phần tóm tắt này sẽ được gửi kèm system prompt để model vẫn giữ được ngữ cảnh quan trọng.*

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
