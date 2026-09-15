# Mini annotation guideline — Ngày 3 (tracking)

> File này ghi lại các quyết định annotation đã dùng trong quá trình làm bài. Các rule cơ bản giữ theo task specification của lab; ba ca mơ hồ bên dưới là những tình huống thực tế đã gặp khi gán `clip_01`.

Nhóm / tên: `Khúc Việt Anh — Cá nhân`  
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung: bbox chỉ lấy **phần vehicle đang nhìn thấy**. Gương chiếu hậu nhìn thấy rõ được tính là một phần của xe; không lấy ánh đèn hắt ra, bóng đổ hoặc nền đường.

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật sử dụng | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | Giữ nguyên ID; dùng ngưỡng mặc định của lab cho occlusion ngắn: dưới 25 frame. Bật `Occluded` ở đoạn bị che nếu phù hợp. | Vẫn là cùng một xe, chỉ bị che tạm thời. |
| Xe bị che lâu hơn ngưỡng trên | Không ghi nhận một ca thực tế để đặt rule riêng; không tự thêm quyết định ngoài guideline của lab. | Tránh đặt rule không dựa trên tình huống đã gặp. |
| Xe rời khung hình rồi quay lại | Theo mặc định lab: **track mới**. | Xe đã rời sequence nhìn thấy; không giữ identity bằng suy đoán. |
| Hai xe cắt nhau / chồng lên nhau | Giữ **hai track và hai ID riêng**; không gộp bbox. Xe phía sau bị che thì giữ ID và bật `Occluded` nếu phù hợp. | Hai object vật lý khác nhau dù bbox có thể chồng lên nhau. |

## 3. Luật bbox

| Tình huống | Luật sử dụng |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được**, không đoán phần bị che |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; không đặt ngưỡng pixel riêng nếu chưa có rule của lab |
| Xe đang đỗ, không di chuyển | vẫn gán nếu xác định rõ là `vehicle`; giữ ID trong thời gian còn nhìn thấy |
| Keyframe đặt dày ở đâu | đặt dày quanh entry/exit, occlusion, crossing và lúc xe đổi hướng/kích thước nhanh; đoạn chuyển động ổn định có thể thưa hơn |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

> Frame dưới đây ghi theo **MOT frame** (frame đầu = 1).

### Ca 1
- Clip / frame / ID: `clip_01 / MOT frame 111 / ID 4`
- Tình huống: Xe buýt bị hàng rào phía trước che phần thân dưới.
- Quyết định: Giữ nguyên ID 4, bbox theo phần xe nhìn thấy và dùng trạng thái `Occluded` khi hàng rào che xe.
- Lý do: Xe vẫn hiện diện và nhận dạng liên tục; đây là partial occlusion chứ không phải `Outside`.

### Ca 2
- Clip / frame / ID: `clip_01 / MOT frame 100 / ID 4 và ID 5`
- Tình huống: Hai xe chồng/cắt lên nhau trong hình.
- Quyết định: Giữ hai Rectangle Track riêng với hai ID riêng; không gộp thành một box. Object bị che giữ identity riêng.
- Lý do: Overlap trên ảnh không làm hai xe trở thành cùng một object.

### Ca 3
- Clip / frame / ID: `clip_01 / MOT frame 90 / ID 4`
- Tình huống: Xe buýt có gương nhô ra và ánh đèn/độ chói xung quanh khiến biên bbox khó xác định.
- Quyết định: Lấy phần xe và gương thực sự nhìn thấy; không lấy vùng ánh đèn hắt, bóng hoặc nền đường.
- Lý do: Bbox phải ôm phần vật thể nhìn thấy, không ôm hiệu ứng ánh sáng/môi trường.

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

- Không rework annotation sau khi mở teaching reference: `annotations/clip_01/gt.txt` hiện có cùng SHA-256 với snapshot pre-gold (`0c8087d46a281c0f3e8053a23aba76caa40caa581fd305edac1405caf7a3162e`).
- Bổ sung guideline rõ hơn cho ba tình huống đã gặp: partial occlusion bởi hàng rào, hai xe overlap, và bbox xe buýt có gương/ánh đèn.
- Không có peer reviewer vì bài làm cá nhân; không tạo finding kiểm chéo giả. Nếu Lab Coach yêu cầu peer review dù làm cá nhân, cần reviewer thật bổ sung `reports/review_partner.md`.
