# GUIDELINE_MINI — Luật gán keypoint (Day 4)

Người gán: Phạm Văn Phong · Bộ 20 ảnh train, chuẩn COCO-17.
Mục đích: thống nhất cách xử lý các khớp khó nhìn, để nhãn nhất quán và chấm OKS công bằng.

## Quy ước cờ visibility (áp dụng chung)

- **v = 2** — khớp nhìn thấy rõ. Không đánh cờ.
- **v = 1** — khớp *nằm trong khung hình* nhưng bị che (sau lưng, sau quần áo, sau người khác). **Vẫn đặt chấm ở vị trí ước lượng**, đánh cờ Occluded (`q`).
- **v = 0** — khớp *nằm ngoài mép ảnh*, bị cắt cụt khỏi khung. Không đặt chấm, đánh cờ Outside (`o`).
- **Không bao giờ dùng trạng thái Hidden (`h`)** — nó không được lưu và xuất ra sai.
- Trái/phải tính **theo cơ thể người trong ảnh**, không theo hướng người xem.

## Luật cho HÔNG (hip)

Hông gần như **không nhìn thấy trực tiếp trên bất kỳ người mặc quần áo nào** — nó là điểm giải phẫu ước lượng, không phải điểm nhìn bằng mắt.

- Luật: hông của người đứng/ngồi mà thân còn trong khung → **luôn v = 1**, đặt chấm ở vị trí ước lượng (khoảng giao giữa thân và đùi, hai bên đối xứng qua trục cột sống).
- Chỉ để **v = 0** khi phần hông thật sự bị cắt ra ngoài mép ảnh (ví dụ ảnh chỉ lấy nửa người trên).
- Không để trống hông chỉ vì "không nhìn thấy" — che khuất là v = 1, không phải v = 0.

## Luật cho CHE TAI (ear occlusion)

Tai hay bị tóc, mũ, hoặc góc đầu che.

- Tai bị **tóc/mũ che một phần hoặc toàn phần** nhưng vị trí vẫn nằm trong khung → **v = 1**, đặt chấm ở chỗ ước lượng tai nằm dưới tóc.
- Người **quay nghiêng/quay lưng** làm một tai khuất hẳn sang phía bên kia đầu nhưng vẫn trong khung ảnh → **v = 1** (ước lượng theo cấu trúc đầu), **không** để v = 0.
- Chỉ **v = 0** khi tai nằm ngoài mép ảnh (đầu bị cắt ở cạnh khung).

## Luật cho CA Ở MÉP ẢNH (image edge)

- Khớp nằm **ngoài** mép ảnh → **v = 0**, không đặt chấm. Đây là trường hợp v = 0 hợp lệ duy nhất.
- Khớp **sát mép nhưng vẫn trong khung** → gán bình thường (v = 2 nếu thấy rõ, v = 1 nếu bị che).
- Người bị **cắt cụt ở eo/đùi** (ảnh nửa người): các khớp chân (đầu gối, cổ chân) ở dưới mép → v = 0. Các khớp thân trên vẫn gán đủ.

## Ba tình huống mơ hồ và cách quyết (kèm lý do)

**Tình huống 1 — Tay giấu sau lưng.**
Người đứng chống tay ra sau, cổ tay/khuỷu tay khuất hẳn sau thân.
→ Quyết: **v = 1**, đặt chấm ở vị trí ước lượng sau hông.
→ Lý do: khớp vẫn trong khung, chỉ bị thân che. Để v = 0 sẽ xoá khớp khỏi bảng điểm OKS và làm model học thiếu.

**Tình huống 2 — Hai người đứng sát, chân người này che chân người kia.**
→ Quyết: làm xong hẳn một người rồi mới sang người kế; khớp bị người bên cạnh che vẫn **v = 1** đặt ước lượng, và luôn kiểm bằng đường nối xem chấm có "nhảy" sang cơ thể người bên cạnh không (lỗi nhầm người).
→ Lý do: nhầm người là lỗi nặng, phải tách bạch từng cơ thể; che bởi người khác vẫn là che (v = 1), không phải ra ngoài khung.

**Tình huống 3 — Người quay mặt về phía máy, tay trái của họ xuất hiện bên phải ảnh.**
→ Quyết: vẫn ghi **left_wrist** cho tay trái của họ, dù nó nằm ở nửa phải khung hình.
→ Lý do: trái/phải tính theo cơ thể người, không theo bức ảnh. Mẹo kiểm: tự tưởng tượng đứng vào chỗ người đó rồi giơ tay trái. Đây là lỗi đảo trái/phải — loại nguy hiểm nhất vì augmentation lật ảnh sẽ dạy sai gấp đôi.
