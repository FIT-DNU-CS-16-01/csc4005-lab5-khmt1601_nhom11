# Lab 5 Guide – Vision Transformer for Smart Campus Scene Classification

## 1. Mục tiêu thực hành

Sau lab này, sinh viên có thể:

1. chuẩn bị subset 5 lớp từ MIT Indoor Scenes 67;
2. fine-tune pretrained Vision Transformer cho scene classification;
3. phân biệt `head_only` và `finetune`;
4. đánh giá mô hình bằng accuracy, macro-F1 và confusion matrix;
5. sử dụng W&B để theo dõi thí nghiệm;
6. phân tích lỗi dựa trên kết quả thực nghiệm.

## 2. Ý tưởng mô hình

ViT xem ảnh như một chuỗi patch:

```text
image → patches → patch embeddings → transformer encoder → classification head
```

Trong lab này, chế độ mặc định là `head_only`:

```text
frozen ViT backbone + trainable classification head
```

Điều này giúp giảm thời gian train và giảm yêu cầu phần cứng.

## 3. Các bước thực hành

### Bước 1. Chuẩn bị dữ liệu

Tạo subset 5 lớp từ MIT Indoor Scenes 67.

### Bước 2. Chạy baseline

```bash
python -m src.train \
  --data_dir /path/to/mit_indoor_smartcampus_5 \
  --run_name vit_b16_head_only \
  --train_mode head_only \
  --epochs 10 \
  --batch_size 16 \
  --augment \
  --use_wandb
```

### Bước 3. Kiểm tra outputs

```text
outputs/vit_b16_head_only/
├── best_model.pt
├── metrics.json
├── history.csv
├── curves.png
├── confusion_matrix.png
├── class_to_idx.json
└── config.json
```

### Bước 4. Phân tích lỗi

Dựa vào confusion matrix, sinh viên cần trả lời:

- lớp nào dễ nhận diện nhất?
- lớp nào dễ bị nhầm?
- nhầm lẫn có hợp lý về mặt ngữ cảnh ảnh không?
- nếu cải thiện, nên tăng dữ liệu, augmentation hay fine-tune toàn bộ mô hình?

## 4. Các lỗi thường gặp

| Lỗi | Nguyên nhân | Cách xử lý |
|---|---|---|
| Không tìm thấy lớp | Sai tên folder | Kiểm tra tên folder trong MIT Indoor Scenes |
| Tải pretrained weights chậm | Lần đầu chạy cần internet | Chạy trước khi vào lớp hoặc dùng cache |
| OOM | Batch size quá lớn | Giảm `--batch_size` |
| Train chậm | Fine-tune toàn bộ ViT | Dùng `--train_mode head_only` |
| W&B không log | Chưa `wandb login` | Đăng nhập lại W&B |
