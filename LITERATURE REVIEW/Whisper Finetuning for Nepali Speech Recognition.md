# Whisper Finetuning for Nepali Speech Recognition

Tài liệu này tập trung vào việc **tinh chỉnh mô hình Whisper của OpenAI** cho nhận dạng giọng nói tiếng Nepal, một ngôn ngữ ít tài nguyên. Nghiên cứu này nhấn mạnh tầm quan trọng của **dữ liệu chất lượng**, **đa dạng** và **kỹ thuật tăng cường dữ liệu** trong việc cải thiện độ chính xác của các hệ thống nhận dạng giọng nói (ASR) cho các ngôn ngữ ít được chú ý.

## Dữ liệu và Chuẩn bị

- **Bộ dữ liệu**: Nghiên cứu sử dụng kết hợp các bộ dữ liệu ASR công khai như **Google Fleurs**, **Mozilla Common Voice**, **OpenSLR**, cùng với một bộ dữ liệu tùy chỉnh được thu âm.
  - **Bộ dữ liệu tùy chỉnh** bao gồm các bản ghi đọc và các bài giảng từ nhiều nguồn công khai và tự thu, đa dạng về độ tuổi, giới tính, cảm xúc và môi trường âm thanh.
  - **Vốn từ vựng** của bộ dữ liệu tùy chỉnh lớn hơn so với các bộ dữ liệu khác.
  - **Tổng cộng** có **33,97 giờ** dữ liệu âm thanh thô.
  - **Sau khi xử lý trước** (loại bỏ khoảng lặng, phân đoạn âm thanh thành các đoạn 30 giây), bộ dữ liệu tùy chỉnh giảm xuống còn **13,58 giờ**.
  - **Tăng cường dữ liệu** bằng cách thêm nhiễu trắng 8000Hz, mở rộng bộ dữ liệu tùy chỉnh lên **27,17 giờ**, và tổng cộng tất cả các bộ dữ liệu lên **42,9 giờ**.

- **Phân chia dữ liệu**: Dữ liệu được chia thành **80%** cho huấn luyện và **20%** cho đánh giá.

- **Tiền xử lý âm thanh**: Các clip âm thanh được lấy mẫu lại ở tần số **16kHz**, trích xuất đặc trưng và mã hóa token.

## Quy trình Tinh Chỉnh

- **Mô hình**: Sử dụng kiến trúc **Whisper** và đào tạo toàn bộ mô hình cơ sở thay vì sử dụng mô hình được đào tạo trước.

- **Các giai đoạn**: Quy trình gồm **chuẩn bị dữ liệu**, **xử lý dữ liệu**, **huấn luyện mô hình** và **suy luận**.

- **Đánh giá**: Đánh giá hiệu suất bằng cách so sánh **tỷ lệ lỗi từ (WER)**.
  - Các mô hình được tinh chỉnh trên các bộ dữ liệu riêng lẻ, kết hợp và tăng cường dữ liệu.
  - So sánh với các mô hình **Whisper gốc của OpenAI** được đào tạo trên bộ dữ liệu **Fleurs**.

## Kết quả Chính

- **Bộ dữ liệu tùy chỉnh**: 
  - Bộ dữ liệu tùy chỉnh cho thấy sự cải thiện đáng kể về **WER** so với các bộ dữ liệu công khai khác, do tính đa dạng và toàn diện của nó.
  - Việc **kết hợp các bộ dữ liệu** khác nhau (Fleurs, Common Voice, SLR) giúp cải thiện **WER**, với sự kết hợp toàn bộ (**all_combined**) đạt được mức giảm **WER** đáng kể.
  - Việc tinh chỉnh trên bộ dữ liệu tùy chỉnh đã giảm **WER** đáng kể ở các mô hình nhỏ (**36,2%**) và vừa (**23,8%**) so với mô hình **Whisper gốc**.

- **Tăng cường dữ liệu**: 
  - Việc thêm **nhiễu trắng** vào dữ liệu âm thanh giúp cải thiện hiệu suất mô hình, mặc dù mức giảm **WER** chỉ là khoảng **4%**.

- **So sánh với Whisper**: 
  - Các mô hình đã tinh chỉnh vượt trội hơn các mô hình **Whisper gốc** trên tất cả các kích thước mô hình (**tiny**, **base**, **small**, **medium**).
  - Mô hình gốc của **Whisper** tạo ra các bản phiên âm bằng chữ Latin cho các mô hình **tiny** và **base**, trong khi các mô hình tinh chỉnh tạo ra các dự đoán bằng chữ **Devanagari**.

## Tài nguyên Huấn luyện

- Các thí nghiệm được thực hiện trên **CPU Intel i9-10900 @ 3.70GHz** với **64GB DDR4 RAM** và **GPU NVIDIA GeForce RTX 3090 24GB**.

## Kết luận

- Nghiên cứu này nhấn mạnh tầm quan trọng của **chất lượng bộ dữ liệu**, **sự đa dạng**, **khả năng tương thích với đầu vào mô hình** và các **chiến lược tăng cường dữ liệu** trong việc cải thiện hiệu suất của các mô hình chuyển đổi giọng nói thành văn bản, đặc biệt là cho các ngôn ngữ ít tài nguyên.
- Việc **tinh chỉnh mô hình Whisper** với dữ liệu tùy chỉnh và kết hợp với các bộ dữ liệu khác, cùng với **tăng cường dữ liệu**, đã giúp giảm đáng kể **WER**.
- Các clip âm thanh dài hơn (**5-30 giây**) trong bộ dữ liệu tùy chỉnh phù hợp hơn với đầu vào của **Whisper**, giúp mô hình thu thập thông tin ngữ cảnh tốt hơn.
- Nghiên cứu này mở ra hướng cho việc cải thiện **ASR** cho các ngôn ngữ ít tài nguyên khác.