## Parameter-Efficient Fine-Tuning (PEFT)

### Tổng quan về PEFT

PEFT là một tập hợp các kỹ thuật nhằm fine-tune mô hình LLM với số lượng tham số tối thiểu. PEFT bao gồm nhiều phương pháp khác nhau:

1. **Adapter Methods**
   - Thêm các layer nhỏ (adapters) vào giữa các layer của mô hình
   - Chỉ huấn luyện adapters, giữ nguyên tham số gốc
   - Bottleneck architecture giúp giảm số tham số cần huấn luyện

2. **Prefix Tuning**
   - Thêm các tham số có thể học được vào đầu chuỗi đầu vào
   - Tối ưu cho các tác vụ NLP cụ thể
   - Hiệu quả với các mô hình encoder-decoder

3. **Prompt Tuning**
   - Học các soft prompts để tối ưu hóa đầu vào
   - Nhẹ hơn prefix tuning
   - Phù hợp với fine-tuning task-specific

4. **LoRA và QLoRA**
   - Sử dụng ma trận thứ hạng thấp
   - Tiết kiệm bộ nhớ và tài nguyên tính toán
   - Dễ dàng tích hợp và triển khai

### Triển khai PEFT với Hugging Face

```python
from peft import (
    get_peft_config,
    PeftModel,
    PeftConfig,
    get_peft_model,
    PromptTuningConfig,
    PromptTuningInit,
)

# Prefix Tuning Configuration
config = PrefixTuningConfig(
    task_type="CAUSAL_LM",
    num_virtual_tokens=20,
    prefix_projection=True,
)

# Prompt Tuning Configuration
config = PromptTuningConfig(
    task_type="CAUSAL_LM",
    num_virtual_tokens=8,
    prompt_tuning_init=PromptTuningInit.TEXT,
    prompt_tuning_init_text="Classify if text is positive or negative:",
)

# Get PEFT model
peft_model = get_peft_model(model, config)
```

## Tích hợp QLoRA với PEFT

### Kiến trúc kết hợp

```python
from peft import prepare_model_for_kbit_training
from transformers import AutoModelForCausalLM, BitsAndBytesConfig

# Cấu hình lượng tử hóa 4-bit
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.float16,
    bnb_4bit_use_double_quant=True,
)

# Tải mô hình với lượng tử hóa
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-13b-hf",
    quantization_config=bnb_config,
    device_map="auto",
)

# Chuẩn bị mô hình cho QLoRA
model = prepare_model_for_kbit_training(model)

# Cấu hình LoRA
config = LoraConfig(
    r=8,
    lora_alpha=16,
    target_modules=[
        "q_proj",
        "v_proj",
        "k_proj",
        "o_proj",
        "gate_proj",
        "up_proj",
        "down_proj",
    ],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM",
)

# Tạo mô hình PEFT với QLoRA
model = get_peft_model(model, config)
```

### Tối ưu hóa bộ nhớ

1. **Gradient Checkpointing**
```python
model.gradient_checkpointing_enable()
model.enable_input_require_grads()
```

2. **Paged Optimizer Configuration**
```python
from torch.optim import AdamW
from transformers import get_scheduler

optimizer = AdamW(
    model.parameters(),
    lr=learning_rate,
    betas=(0.9, 0.999),
    eps=1e-8,
    weight_decay=0.001,
)

# Gradient accumulation
accumulation_steps = 4
```

### Huấn luyện và đánh giá

```python
# Training loop với gradient accumulation
for batch in dataloader:
    with torch.cuda.amp.autocast():
        loss = model(batch).loss
        loss = loss / accumulation_steps
    
    loss.backward()
    
    if (step + 1) % accumulation_steps == 0:
        optimizer.step()
        optimizer.zero_grad()

# Đánh giá
model.eval()
with torch.no_grad():
    eval_loss = model(eval_batch).loss
```

### Lợi ích của tích hợp

1. **Hiệu quả bộ nhớ**
   - QLoRA giảm kích thước mô hình qua lượng tử hóa
   - PEFT giảm số lượng tham số cần huấn luyện
   - Kết hợp cho phép fine-tune mô hình lớn trên GPU thông thường

2. **Chất lượng mô hình**
   - Duy trì chất lượng gần với full fine-tuning
   - Giảm thiểu catastrophic forgetting
   - Tăng tính ổn định trong huấn luyện

3. **Tính linh hoạt**
   - Dễ dàng chuyển đổi giữa các phương pháp PEFT
   - Hỗ trợ nhiều kiến trúc mô hình khác nhau
   - Tích hợp được với các framework hiện đại