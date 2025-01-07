# Wav2vec2.0 vs. Whisper: Forensic Speech Recognition

Bài báo này nghiên cứu về việc ứng dụng các hệ thống nhận dạng giọng nói (ASR) và phát hiện từ khóa (KWS) tiên tiến trong lĩnh vực pháp y, đặc biệt là trong bối cảnh khai thác trẻ em trực tuyến. Mục tiêu chính là cung cấp cho các cơ quan thực thi pháp luật (LEA) một nền tảng hỗ trợ AI để phân tích dữ liệu âm thanh từ các nguồn trực tuyến một cách kịp thời và hiệu quả. Bài báo tập trung vào việc so sánh hai kiến trúc mạng nơ-ron hiện đại nhất: Wav2vec2.0 (mô hình tự giám sát) và Whisper (mô hình giám sát đầy đủ). Đồng thời, bài báo cũng khám phá việc sử dụng học liên kết (federated learning - FL) để bảo vệ quyền riêng tư của dữ liệu LEA.

## Tóm tắt chi tiết nội dung bài báo:

### Giới thiệu:
- Sự gia tăng của tài liệu khai thác trẻ em trực tuyến là một thách thức lớn đối với các LEA châu Âu.
- Các LEA cần một nền tảng AI để xử lý dữ liệu đa phương tiện, đặc biệt là âm thanh, một cách nhanh chóng.
- Hai ứng dụng cốt lõi là ASR (chuyển đổi giọng nói thành văn bản) và KWS (phát hiện từ khóa cụ thể).
- Bài báo so sánh Wav2vec2.0 và Whisper, đồng thời đánh giá FL để đảm bảo tính riêng tư của dữ liệu.

### Các phương pháp:
- **Wav2vec2.0:**
  - Là mô hình tự giám sát, sử dụng các lớp convolutional và transformer để mã hóa sóng âm thanh thành biểu diễn tiềm ẩn.
  - Mô hình được đào tạo trước trên một lượng lớn dữ liệu không nhãn và sau đó được tinh chỉnh với dữ liệu có nhãn.
  - Sử dụng mô hình Wav2Vec2-XLS-R-300M, được đào tạo trên 436 nghìn giờ dữ liệu đa ngôn ngữ.
- **Whisper:**
  - Là mô hình giám sát đầy đủ, sử dụng kiến trúc transformer encoder-decoder.
  - Được đào tạo trên 680 nghìn giờ dữ liệu âm thanh có nhãn từ nhiều nguồn khác nhau.
  - Sử dụng mô hình "Whisper-large" với 1550 triệu tham số.
- **Học liên kết (FL):**
  - Sử dụng FL để đào tạo mô hình trên dữ liệu từ các LEA khác nhau mà không cần chia sẻ trực tiếp dữ liệu.
  - Chỉ các cập nhật mô hình cục bộ được truyền đến máy chủ trung tâm để tổng hợp và cập nhật mô hình toàn cục.

### Dữ liệu:
- Sử dụng nhiều bộ dữ liệu công khai cho 7 ngôn ngữ Indo-European: Anh, Đức, Pháp, Tây Ban Nha, Ý, Bồ Đào Nha và Ba Lan.
- Bộ dữ liệu Common Voice được sử dụng để tinh chỉnh mô hình Wav2vec2.0.
- Tạo bộ dữ liệu GRACE trong miền bằng cách chọn các mẫu âm thanh chứa các từ khóa liên quan đến lạm dụng trẻ em.
  - Các mẫu âm thanh được thêm nhiễu, vang và mã hóa ogg-vorbis để mô phỏng điều kiện thực tế.
- Danh sách từ khóa được xác định từ các tài liệu mở liên quan đến lạm dụng trẻ em.

### Kết quả:
- **Nhận dạng giọng nói (ASR):**
  - Whisper thường đạt kết quả tốt hơn Wav2vec2.0, đặc biệt trong các điều kiện âm thanh bất lợi.
  - Whisper đạt tỷ lệ lỗi từ (WER) từ 11.3% đến 24.9% tùy theo ngôn ngữ, trong khi Wav2vec2.0 từ 13.1% đến 34.8%.
  - Whisper có khả năng duy trì độ chính xác cao hơn trong môi trường nhiều nhiễu.
  - Mô hình đạt kết quả tốt nhất so với các nghiên cứu khác trên một số bộ dữ liệu, đặc biệt là MTEDx, MediaSpeech và CORAA.
- **Phát hiện từ khóa (KWS):**
  - Whisper cũng cho kết quả phát hiện từ khóa tốt hơn, đặc biệt là trong điều kiện nhiễu.
  - Tỷ lệ phát hiện dương tính thật (TPR) của Whisper từ 81.5% đến 98.4%, trong khi Wav2vec2.0 từ 82.9% đến 94.9%.
  - Sự khác biệt giữa hai mô hình tăng lên đáng kể trong bộ dữ liệu GRACE có nhiễu.
- **Học liên kết (FL):**
  - Mô hình ASR được đào tạo bằng FL có hiệu suất tương đương với các mô hình được đào tạo tập trung.
  - FL cho phép đào tạo mô hình chung mà không cần chia sẻ dữ liệu, đảm bảo tính riêng tư.

### Thảo luận:
- Whisper là một hệ thống ASR chính xác hơn, đặc biệt trong điều kiện âm thanh không kiểm soát.
- Cả Wav2vec2.0 và Whisper đều có khả năng phát hiện từ khóa tốt, nhưng Whisper vượt trội hơn trong môi trường nhiễu.
- FL là một phương pháp đầy hứa hẹn để đào tạo mô hình ASR mạnh mẽ trong bối cảnh dữ liệu riêng tư.

### Kết luận:
- Bài báo đã đánh giá khả năng của Wav2vec2.0 và Whisper trong việc nhận dạng giọng nói và phát hiện từ khóa trong bối cảnh pháp y.
- Whisper tỏ ra là mô hình chính xác hơn, đặc biệt trong điều kiện âm thanh khó khăn.
- FL là một giải pháp tiềm năng để đào tạo mô hình ASR chung mà vẫn đảm bảo tính riêng tư của dữ liệu LEA.
- Nghiên cứu này có thể được mở rộng sang các ứng dụng pháp y khác và kết hợp với các phương pháp xử lý giọng nói khác.

## So sánh hai mô hình:

| Tính năng           | Wav2vec2.0                                         | Whisper                                             |
|---------------------|----------------------------------------------------|-----------------------------------------------------|
| **Loại mô hình**    | Tự giám sát                                        | Giám sát đầy đủ                                     |
| **Kiến trúc**       | Convolutional và transformer                       | Transformer encoder-decoder                        |
| **Dữ liệu đào tạo** | Lượng lớn dữ liệu không nhãn, sau đó tinh chỉnh với dữ liệu có nhãn | 680 nghìn giờ dữ liệu âm thanh có nhãn từ nhiều nguồn khác nhau |
| **Hiệu suất ASR**   | Kém hơn Whisper trong điều kiện nhiễu             | Tốt hơn trong hầu hết các trường hợp, đặc biệt là trong điều kiện nhiễu |
| **Hiệu suất KWS**   | Kém hơn Whisper trong điều kiện nhiễu             | Tốt hơn, đặc biệt trong điều kiện nhiễu             |
| **Tính riêng tư**   | Có thể kết hợp với FL                              | Có thể kết hợp với FL                               |