# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Phạm Văn Phong  Nhóm: làm cá nhân  Ngày: 16/09/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 264 / 118 / 111 |
| Thời gian trung bình mỗi ảnh | ~4 phút/ảnh |

(Số trên là nhãn bản cuối sau rework — đúng bản đã chấm gold và đưa lên Colab. Cần chạy lại `tools/visibility_report.py` để `reports/visibility_report.md` khớp con số này.)

Ba khớp có `%v=1` cao nhất (các khớp hay bị che nhất): right_ear, left_ear, right_elbow — xác nhận số % chính xác từ `visibility_report.md` sau khi tạo lại.

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Có. Các khớp `%v=1` cao nhất đều là tai và khuỷu tay — đúng là những khớp hay bị che nhất khi gán. Tai thường bị **tóc và góc nghiêng của đầu** che nên phải ước lượng vị trí dưới tóc; đây là loại "bị che khuất", không phải "khó xác định giải phẫu". Khuỷu tay bị **thân người hoặc tay áo** che khi tay gập. Khác với hông — hông có `%v=1` vừa phải nhưng khó ở chỗ *xác định vị trí giải phẫu* dù không có vật gì che, nên bản chất khó của nó khác với tai/khuỷu tay.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | — (không lưu số lần chạy đầu) | 0.8515 |
| OKS@0.50 | — | 1.000 |
| OKS@0.75 | — | 0.7931 |
| Lỗi `dao_trai_phai` | — | 0 |
| Lỗi `nham_nguoi` | — | 2 |
| Lỗi `xoa_khop_bi_che` | — | 26 |

**Tôi đã sửa gì / trạng thái rework** (theo `outputs/eval_vs_gold.json`):

- Không còn lỗi đảo trái/phải: `dao_trai_phai = 0` trên toàn bộ 20 ảnh.
- Ghép đủ người: gold 29, ghép được 29, không thiếu không thừa.
- Các lỗi còn lại đã rà theo danh sách script: 2 nhầm người (train_16 người #1: left_hip, right_hip), 26 xoá khớp bị che, 14 thiếu khớp gold có. Do giới hạn thời gian, tôi chốt ở mức hiện tại — OKS trung bình 0.8515, đạt mức "Đạt" (≥0.75 và OKS@0.75 ≥0.70).

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó?

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh (`dao_trai_phai = 0`).

## 3. Kiểm chéo

Làm cá nhân — không có bạn cùng nhóm nên không thực hiện kiểm chéo. Mục này để trống.

Luật tự rút ra và đã ghi vào `GUIDELINE_MINI.md`:

- Tai bị tóc che nhưng đầu vẫn trong khung → v=1 và đặt chấm ước lượng; chỉ để v=0 khi đầu bị cắt khỏi mép ảnh. (Rút ra từ nhóm khớp tai có `%v=1` cao nhất trong bài.)

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.845 | 0.845 | 0.000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | 0.000 |
| box_mAP50-95 | 0.8119 | 0.8041 | −0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu?** Tăng nhẹ +0.0055 (0.6853 → 0.6908). 20 ảnh dạy model đặc thù pose và kiểu che khuất của bộ "cabin" (có mặt nạ che riêng tư), giúp định vị khớp nhích lên một chút. Nhưng nó làm hỏng khả năng phát hiện *hộp người*: `box_mAP50-95` giảm −0.0078 và `box_mAP50` giảm −0.0185 — model quên bớt phân bố rộng mà COCO đã dạy. Đây là dấu hiệu điển hình của việc fine-tune trên quá ít dữ liệu.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu?** `box_mAP50-95` (0.8041) cao hơn hẳn `pose_mAP50-95` (0.6908), chênh ~0.11. Model tìm **người** dễ hơn tìm **khớp**: vẽ một hộp quanh cả người dễ hơn nhiều so với định vị chính xác 17 khớp, nhất là các khớp bị che.

3. **Một ảnh model đoán sai — gọi tên lỗi theo 4 loại:** Ở train_13 (3 người đứng sát nhau), OKS giữa model và nhãn của tôi rơi xuống 0.46 — thấp nhất cả bộ. Đây là lỗi **nhầm người**: khi nhiều người chen sát, model kéo khớp của người này sang cơ thể người bên cạnh. Cùng kiểu đó, ở train_03 model dự đoán 4 người trong khi ảnh chỉ có 2 (model bịa thêm người). Kết luận: model yếu nhất ở cảnh đông người/chen sát, đúng như phần nhìn tận mắt ở mục 5.

4. **Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng?** train_13, OKS 0.46 (thấp nhất bảng mục 6). Ở ảnh này gold đã chỉ ra nhãn của tôi **thiếu khớp gold nhìn rõ** (left_shoulder, right_elbow, right_wrist — tôi để v=0 trong khi gold có v=2), nên với các khớp đó model gần đúng hơn tôi. Phần còn lại của độ lệch đến từ cảnh đông người khiến cả model lẫn người gán đều khó. Tức là: một phần do tôi bỏ sót khớp (gold xác nhận), một phần do ảnh khó.

5. **Ảnh bạn gán tệ nhất có cũng là ảnh model đoán tệ nhất không?** Có — **train_06**. Nhãn của tôi so với gold ở train_06 là OKS 0.673 (gần thấp nhất), và OKS model-vs-tôi cũng chỉ 0.651 (gần thấp nhất). Một ảnh vừa khó cho tôi vừa khó cho model → chứng tỏ bản thân bức ảnh khó (tai và hông bị che nặng), chứ không phải riêng nhãn của tôi sai hay riêng model sai.

## 5. Một rule evidence bạn đã dùng

Ở ảnh train_11 người #1, tôi phải quyết định trạng thái cho **right_wrist**. Cổ tay phải bị thân người che khuất khi tay đưa ra sau, nhưng toàn bộ người vẫn nằm gọn trong khung hình. Căn cứ thị giác: cẳng tay và khuỷu tay phía trên vẫn thấy được và hướng ra sau lưng, nên cổ tay chắc chắn nằm trong khung chứ không ra ngoài mép ảnh. Vì khớp còn trong khung nhưng bị thân che, đúng ra phải chọn **v=1** và đặt chấm ở vị trí ước lượng sau hông (để v=0 sẽ xoá khớp khỏi bảng điểm OKS). Đây chính là loại quyết định mà bản chấm gold đánh dấu là "xoá khớp bị che" khi tôi để v=0 — bài học rút ra cho lần gán sau.
