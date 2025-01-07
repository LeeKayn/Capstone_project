# LoRA & QLoRA trong Fine-tuning LLMs

Bài viết này trình bày chi tiết về hai kỹ thuật **parameter-efficient fine-tuning (PEFT)** cho các mô hình ngôn ngữ lớn (LLM): **Low-Rank Adaptation (LoRA)** và **Quantized Low-Rank Adaptation (Q-LoRA)**. Các phương pháp này đã tạo ra bước đột phá trong việc fine-tuning các mô hình LLM với chi phí tính toán thấp.

## Giới thiệu

- Các mô hình ngôn ngữ lớn (LLMs) hiện đại như GPT-3, LLaMA, và BLOOM có hàng tỷ tham số, làm cho việc fine-tuning truyền thống trở nên tốn kém và khó tiếp cận.
- Full fine-tuning đòi hỏi cập nhật toàn bộ tham số của mô hình, dẫn đến nhu cầu lớn về bộ nhớ GPU và tài nguyên tính toán.
- **LoRA** và **Q-LoRA** thuộc nhóm các kỹ thuật PEFT (Parameter-Efficient Fine-Tuning), cho phép tinh chỉnh mô hình với chi phí thấp hơn đáng kể.

## LoRA (Low-Rank Adaptation)

### Nguyên lý hoạt động

- **LoRA** áp dụng giả thuyết rằng các cập nhật trong quá trình fine-tuning có thể được biểu diễn thông qua các ma trận có thứ hạng thấp.
- Thay vì cập nhật trực tiếp ma trận trọng số gốc W ∈ ℝᵈ×ᵏ, LoRA học ma trận cập nhật ΔW = BA, trong đó:
  - B ∈ ℝᵈ×ʳ và A ∈ ℝʳ×ᵏ
  - r là thứ hạng của ma trận (rank), thường r ≪ min(d,k)
  - Các tham số gốc của mô hình được giữ nguyên (frozen)

### Ưu điểm kỹ thuật

- **Hiệu quả về bộ nhớ**: Với r = 8, số lượng tham số huấn luyện giảm xuống còn khoảng 0.1%-1% so với full fine-tuning.
- **Khả năng cộng gộp**: Có thể kết hợp nhiều bộ trọng số LoRA để tạo ra các phiên bản mô hình khác nhau.
- **Không tăng độ trễ suy luận**: Ma trận ΔW có thể được hợp nhất với W sau khi huấn luyện.
- **Tính module hóa**: Có thể chọn lọc áp dụng LoRA cho một số layer hoặc thành phần cụ thể của mô hình.

## Q-LoRA (Quantized Low-Rank Adaptation)

### Cải tiến chính

- **Lượng tử hóa 4-bit NormalFloat (NF4)**:
  - Phân phối các khoảng lượng tử theo phân phối chuẩn
  - Tối ưu cho trọng số của neural network
  - Giảm lỗi lượng tử hóa so với các phương pháp truyền thống

- **Paged Optimizers**:
  - Quản lý bộ nhớ thông minh giữa CPU-GPU
  - Cho phép fine-tuning mô hình 65B tham số trên GPU 24GB
  - Sử dụng kỹ thuật gradient checkpointing và offloading

### Cải tiến kỹ thuật

- **Double Quantization**:
  - Lượng tử hóa cả constants của quá trình lượng tử hóa
  - Giảm thêm 0.37 bit/tham số
  - Không ảnh hưởng đến chất lượng mô hình

- **Prefetch**:
  - Tối ưu hóa việc di chuyển dữ liệu giữa các thiết bị
  - Giảm overhead của việc paging

## Triển khai thực tế

### Huấn luyện

```python
from peft import LoraConfig, get_peft_model

config = LoraConfig(
    r=8,                     # rank
    lora_alpha=16,          # scaling factor
    target_modules=["q", "v"],  # layers to apply LoRA
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(base_model, config)
```

### Merge và Inference

```python
# Merge LoRA weights with base model
merged_model = model.merge_and_unload()

# Save merged model
merged_model.save_pretrained("path/to/save")
```

## Các trường hợp sử dụng tối ưu

- **Instruction Tuning**: Điều chỉnh mô hình để tuân theo hướng dẫn cụ thể
- **Domain Adaptation**: Thích ứng với lĩnh vực chuyên biệt như y tế, luật
- **Multilingual Adaptation**: Mở rộng khả năng ngôn ngữ của mô hình
- **Style Transfer**: Điều chỉnh phong cách viết hoặc giọng điệu

## Thực tiễn tốt nhất

- **Chọn rank phù hợp**: r=8 thường là điểm cân bằng tốt giữa hiệu suất và chi phí
- **Target modules**: Tập trung vào các layer quan trọng như attention và MLP
- **Kiểm soát alpha**: alpha/r quyết định mức độ ảnh hưởng của LoRA
- **Regularization**: Sử dụng dropout và weight decay để tránh overfitting

## Kết luận

- LoRA và Q-LoRA đã làm thay đổi cách tiếp cận fine-tuning LLMs, mang lại khả năng tiếp cận rộng rãi hơn cho cộng đồng.
- Các kỹ thuật này không chỉ giảm chi phí tính toán mà còn duy trì được chất lượng mô hình, mở ra nhiều khả năng ứng dụng mới.
- Sự phát triển liên tục của các kỹ thuật PEFT hứa hẹn sẽ tiếp tục cải thiện hiệu quả và khả năng tiếp cận của AI.