# Visibility report

- Thư mục nhãn: `dataset/labels/train`
- 20 ảnh, 29 skeleton, trung bình 13.17 khớp có v > 0 mỗi người
- Tổng: v=2 264 | v=1 118 | v=0 111

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 19 | 7 | 3 | 24% |
| 1 | left_eye | 18 | 6 | 5 | 21% |
| 2 | right_eye | 18 | 8 | 3 | 28% |
| 3 | left_ear | 10 | 11 | 8 | 38% |
| 4 | right_ear | 14 | 12 | 3 | 41% |
| 5 | left_shoulder | 18 | 8 | 3 | 28% |
| 6 | right_shoulder | 23 | 5 | 1 | 17% |
| 7 | left_elbow | 19 | 5 | 5 | 17% |
| 8 | right_elbow | 16 | 9 | 4 | 31% |
| 9 | left_wrist | 14 | 6 | 9 | 21% |
| 10 | right_wrist | 16 | 3 | 10 | 10% |
| 11 | left_hip | 18 | 7 | 4 | 24% |
| 12 | right_hip | 18 | 6 | 5 | 21% |
| 13 | left_knee | 13 | 5 | 11 | 17% |
| 14 | right_knee | 13 | 6 | 10 | 21% |
| 15 | left_ankle | 10 | 6 | 13 | 21% |
| 16 | right_ankle | 7 | 8 | 14 | 28% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
