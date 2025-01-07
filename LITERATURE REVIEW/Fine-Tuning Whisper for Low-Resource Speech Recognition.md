# Fine-Tuning Whisper for Low-Resource Speech Recognition

Bài báo này trình bày một phương pháp mới để tinh chỉnh mô hình Whisper của OpenAI cho các ngôn ngữ có nguồn lực thấp, sử dụng tiếng Thụy Sĩ-Đức làm trường hợp nghiên cứu. Các tác giả đã phát triển một phương pháp tạo dữ liệu mới chuyển đổi dữ liệu cấp câu thành một kho ngữ liệu dạng dài. Dữ liệu dạng dài, có thể cải thiện hiệu suất của âm thanh dạng dài, thường khó thu thập và bị hạn chế bởi luật bản quyền. Phương pháp này thu hẹp khoảng cách bằng cách chuyển đổi dữ liệu cấp câu dễ tiếp cận hơn thành định dạng duy trì khả năng xử lý âm thanh dạng dài của mô hình và thực hiện phân đoạn mà không yêu cầu dữ liệu không phải cấp câu.

## Kết quả chính

- **Phương pháp tạo dữ liệu mới**: Các tác giả đã phát triển một quy trình tạo dữ liệu từ các câu đơn lẻ để tạo thành các đoạn âm thanh dài hơn. Quy trình này bao gồm các bước:
  - **Chỉnh sửa dấu thời gian (Timestamp Correction)**: Sử dụng Voice Activity Detection (VAD) để sửa dấu thời gian bắt đầu và kết thúc của các phân đoạn âm thanh.
  - **Chồng chéo tiếng ồn (Noise Overlapping)**: Sử dụng kỹ thuật chồng chéo ngẫu nhiên các khoảng lặng để cải thiện sự chuyển tiếp giữa các mẫu âm thanh.
  - **Duy trì người nói (Speaker Retention)**: Với các mẫu có thông tin người nói, có 50% khả năng giữ lại cùng một người nói trong các mẫu liên tiếp.
- **Cải thiện hiệu suất**: Phương pháp tạo dữ liệu này cải thiện hiệu suất trong một số ứng dụng thực tế và dẫn đến sự phát triển của một mô hình chuyển giọng nói thành văn bản (STT) mới đạt hiệu quả tốt nhất cho tiếng Thụy Sĩ-Đức. Mô hình mới đạt điểm BLEU cao hơn so với mô hình Whisper chưa được tinh chỉnh và các mô hình STT tiếng Thụy Sĩ-Đức trước đây.
- **Khả năng thích ứng**: Kết quả cũng chỉ ra rằng phương pháp này có thể thích ứng với các ngôn ngữ có nguồn lực thấp khác. Các tác giả cũng cung cấp hướng dẫn bằng văn bản và mã nguồn để tạo các mô hình Whisper được tinh chỉnh, duy trì khả năng phân đoạn và cho phép phiên âm các tệp âm thanh dài hơn chỉ bằng dữ liệu cấp câu với chất lượng cao.
- **Đánh giá**: Các tác giả đã đánh giá khả năng phân đoạn của Whisper sau khi tinh chỉnh và chứng minh tác động có lợi của việc tinh chỉnh đối với âm thanh dạng dài được tạo từ dữ liệu cấp câu. Họ cũng đánh giá tác động của lượng dữ liệu huấn luyện đến hiệu suất mô hình khi tinh chỉnh Whisper.

## Các đề mục chính trong bài báo

### 1. Giới thiệu (Introduction)
- Giới thiệu về tiếng Thụy Sĩ-Đức và những khó khăn trong việc phát triển hệ thống STT cho ngôn ngữ này.
- Nêu bật hiệu suất tốt đáng ngạc nhiên của Whisper đối với tiếng Thụy Sĩ-Đức, nhưng vẫn còn nhiều chỗ để cải thiện.
- Đề xuất mục tiêu nghiên cứu: tinh chỉnh Whisper cho các ngôn ngữ có nguồn lực thấp, đặc biệt là tiếng Thụy Sĩ-Đức, và đánh giá khả năng phân đoạn sau khi tinh chỉnh.
- Đặt ra các câu hỏi nghiên cứu chính.

### 2. Các nghiên cứu liên quan (Related Work)
- Tổng quan về các nghiên cứu trước đây về dịch giọng nói cho tiếng Thụy Sĩ-Đức.
- Đề cập đến các mô hình nền tảng phổ biến như XLS-R.
- Nêu các nghiên cứu về tinh chỉnh Whisper, nhưng chỉ ra rằng rất ít nghiên cứu tập trung vào việc tinh chỉnh Whisper để phiên âm các âm thanh dài hơn hoặc đánh giá khả năng phân đoạn sau khi tinh chỉnh.

### 3. Phương pháp (Approach)
- **Tạo dữ liệu (Data Generation)**: Mô tả chi tiết phương pháp tạo dữ liệu dạng dài từ dữ liệu cấp câu, bao gồm các bước chỉnh sửa dấu thời gian, chồng chéo tiếng ồn và duy trì người nói.
- **Chi tiết huấn luyện (Training Details)**: Mô tả các tham số huấn luyện, bao gồm việc sử dụng Whisper Large-v2 làm mô hình khởi tạo, các kỹ thuật tối ưu hóa và lịch trình học.
- **Dữ liệu huấn luyện, xác thực và kiểm tra (Train, Validation and Test Data)**: Liệt kê các tập dữ liệu được sử dụng cho huấn luyện, xác thực và kiểm tra, bao gồm cả dữ liệu do chính tác giả tạo ra và dữ liệu công khai.

### 4. Thí nghiệm (Experiments)
- **Đánh giá (Evaluation)**: Mô tả các phương pháp đánh giá, bao gồm các chỉ số WER, BLEU và SubER.
- **Kết quả cơ bản (Base Results)**: So sánh hiệu suất của các mô hình Whisper Large khác nhau (v2 và v3) trên các tập dữ liệu khác nhau.
- **Quên phân đoạn và giảm hiệu suất ngoài phân phối (Segmentation Forgetting & Out-of-Distribution Performance Degradation)**: Đánh giá tác động của việc tinh chỉnh mà không có dấu thời gian lên khả năng phân đoạn và hiệu suất trên dữ liệu ngoài phân phối.
- **Khắc phục những thiếu sót (Overcome Shortcomings)**: Trình bày cách cải thiện mô hình bằng cách bổ sung dữ liệu huấn luyện từ các phân phối mục tiêu và dữ liệu đa ngôn ngữ để giảm "quên ngôn ngữ".
- **So sánh phương ngữ (Dialect Comparison)**: So sánh hiệu suất của mô hình trên các phương ngữ khác nhau của tiếng Thụy Sĩ-Đức.

### 5. Kết luận (Conclusions)
- Tóm tắt các kết quả chính và trả lời các câu hỏi nghiên cứu.
- Nhấn mạnh tầm quan trọng của việc tạo hoặc thu thập các tập dữ liệu đánh giá mô phỏng môi trường triển khai dự kiến.
- Đề xuất hướng nghiên cứu trong tương lai.
- Đề cập đến hiện tượng "ảo giác" của Whisper Large-v2.

### 6. Hướng nghiên cứu tiếp theo (Future Work)
- Đề xuất các hướng nghiên cứu tiếp theo, bao gồm kết hợp các phương pháp tạo dữ liệu và tránh quên thảm khốc, cũng như đa dạng hóa các nguồn dữ liệu.

### 7. Tài liệu tham khảo (References)
- Liệt kê các tài liệu tham khảo được sử dụng trong bài báo.

## Tóm tắt

Bài báo này cung cấp một phương pháp hiệu quả để tinh chỉnh mô hình Whisper cho các ngôn ngữ có nguồn lực thấp, đặc biệt là tiếng Thụy Sĩ-Đức. Phương pháp tạo dữ liệu mới giúp duy trì khả năng phân đoạn và cải thiện hiệu suất trên dữ liệu dạng dài. Các tác giả cũng cung cấp mã nguồn để cộng đồng có thể sử dụng và phát triển thêm.