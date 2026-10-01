# K4-DAY13-NguyenCongKhai-2A202602243 — Báo cáo PointPillars

Báo cáo thực hành **Day 13: PointPillars Inference & QC** của nhóm **KPM (Phòng C305)**.repo private do LC thu, kiểm tra formative.

## Thành viên

| Họ tên | MSSV | Vai trò |
| --- | --- | --- |
| Lý Hồng Phúc | 2A202602221 | Vận hành lượt A, xem hình học lượt B, kiểm cấu hình lượt C |
| Nguyễn Công Khải | 2A202602243 | Kiểm cấu hình lượt A, vận hành lượt B, xem hình học lượt C |
| Tạ Văn Mạnh Đức | 2A202602235 | Xem hình học lượt A, kiểm cấu hình lượt B, vận hành lượt C |

## Nội dung chính

### Thực hành inference
- Chạy **3 lượt inference** (A/B/C) trên cùng file PCD (`data/demo.pcd`) với các tham số khác nhau:
  - **Lượt A**: delta=0, pillar=0.16 → 1 hộp (vehicles)
  - **Lượt B**: delta=+1.73, pillar=0.16 → 13 hộp (10 vehicles, 2 pedestrian, 1 two-wheels)
  - **Lượt C**: delta=+1.73, pillar=0.32 → 6 hộp (toàn pedestrian)
- Model: PointPillars KITTI pretrained (`epoch_160.pth`)
- Docker image: `day13-pointpillars:student`
- Hệ máy: Windows 11 + Docker Desktop (linux/amd64)

### Phân tích kết quả
- **A→B**: Dịch z input +1.73m → model phát hiện thêm 12 đối tượng → chứng minh dịch z ở input ảnh hưởng voxel hóa, không phải hậu xử lý output
- **B→C**: Tăng pillar XY từ 0.16→0.32 → mất hết vehicles/two-wheels, chỉ còn pedestrian → pillar thô hơn có thể giảm recall
- ROI front-window, score threshold=0.3
- z_ground=0.075m (ước lượng từ dữ liệu)

### Kiểm soát QC
- **case-correct**: baseline (0/13 hộp lệch) — không lỗi
- **case-batch-z**: 13/13 hộp lệch -1.805m → nghi lỗi pipeline/transform
- **case-one-box-z**: 1/13 hộp lệch -1.805m → lỗi cục bộ, kiểm riêng

### Cấu trúc thư mục
```
report/
└── K4-DAY13-KPM/
    ├── PRE-LABEL-REPORT.md   # Báo cáo chi tiết
    ├── TEAMMATES.md          # Thành viên & vai trò
    └── (các file output: run-A/B/C/, qc-cases/)
```

## File quan trọng
- `report/K4-DAY13-KPM/PRE-LABEL-REPORT.md` — Báo cáo đầy đủ
- `report/K4-DAY13-KPM/TEAMMATES.md` — Thông tin thành viên
- `run-A/B/C/` — Output inference (JSON, PNG, CSV)
- `qc-cases/` — Test cases kiểm soát chất lượng

## Lưu ý
- Toàn bộ kết quả chạy thật trên máy nhóm (executed-by-group)
- Không import các prediction vào CVAT
- Chưa có ảnh camera để đối chiếu class (chỉ dùng LiDAR)
