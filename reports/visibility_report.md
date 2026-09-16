# Visibility report

- Thư mục nhãn: `dataset/labels/train`
- 20 ảnh, 28 skeleton, trung bình 13.36 khớp có v > 0 mỗi người
- Tổng: v=2 260 | v=1 114 | v=0 102

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 19 | 7 | 2 | 25% |
| 1 | left_eye | 18 | 6 | 4 | 21% |
| 2 | right_eye | 18 | 8 | 2 | 29% |
| 3 | left_ear | 10 | 11 | 7 | 39% |
| 4 | right_ear | 13 | 12 | 3 | 43% |
| 5 | left_shoulder | 18 | 8 | 2 | 29% |
| 6 | right_shoulder | 22 | 5 | 1 | 18% |
| 7 | left_elbow | 19 | 5 | 4 | 18% |
| 8 | right_elbow | 16 | 9 | 3 | 32% |
| 9 | left_wrist | 14 | 6 | 8 | 21% |
| 10 | right_wrist | 16 | 3 | 9 | 11% |
| 11 | left_hip | 17 | 7 | 4 | 25% |
| 12 | right_hip | 17 | 6 | 5 | 21% |
| 13 | left_knee | 13 | 4 | 11 | 14% |
| 14 | right_knee | 13 | 5 | 10 | 18% |
| 15 | left_ankle | 10 | 5 | 13 | 18% |
| 16 | right_ankle | 7 | 7 | 14 | 25% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
