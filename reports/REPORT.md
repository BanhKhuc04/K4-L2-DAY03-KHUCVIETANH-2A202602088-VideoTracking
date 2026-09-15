# Báo cáo Ngày 3 — Tracking Annotation

Họ tên / nhóm: `Khúc Việt Anh / Cá nhân`  
Ngày: `15/09/2026`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT |
| Thời gian gán `clip_02` (warm-up) | 20 phút |
| Thời gian gán `clip_01` | 60 phút |
| Số track đã vẽ trong `clip_01` | 8 |
| Số keyframe trung bình mỗi track | Không ghi nhận; file MOT 1.1 không lưu metadata keyframe |

Ba tình huống khó nhất khi gán clip này, và cách xử lý:

1. **Xe bị che khuất bởi hàng rào hoặc xe khác:** giữ nguyên ID khi xe vẫn là cùng một đối tượng, bbox chỉ ôm phần nhìn thấy được và bật `Occluded` ở frame bắt đầu bị che nếu phù hợp.
2. **Hai xe chồng/cắt lên nhau:** vẫn tạo hai track riêng và giữ hai ID riêng; không gộp thành một bbox. Xe bị che phía sau được đánh dấu `Occluded` nếu còn xác định được.
3. **Bbox xe buýt, gương và ánh đèn:** bbox ôm phần xe thực sự nhìn thấy; gương chiếu hậu nhìn thấy rõ được tính là một phần của xe, nhưng không lấy vùng ánh đèn hắt ra, bóng đổ hoặc nền đường.

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `0c8087d46a281c0f3e8053a23aba76caa40caa581fd305edac1405caf7a3162e` |
| Thời điểm khóa | `2026-09-15 17:24:34 +07` (manifest UTC: `2026-09-15T10:24:34.861264+00:00`) |
| Số row / frame / track trước khi mở reference | 623 row / 190 frame / 8 track |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | 0.8172 | 0.7970 | 0.8399 | 0.8913 | 0.9532 | 0.9023 | 0.8805 | 53 | 3 | 0 |
| Sau rework (không sửa, giữ nguyên pre-gold) | 0.8172 | 0.7970 | 0.8399 | 0.8913 | 0.9532 | 0.9023 | 0.8805 | 53 | 3 | 0 |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có**.

Bản `annotations/clip_01/gt.txt` hiện có cùng SHA-256 với snapshot pre-gold, vì vậy không có chỉnh sửa annotation sau khi mở reference/model. Bản pre-gold đồng thời là bản cuối đang nộp.

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| Không rework | — | — | Không sửa; bản pre-gold đã qua cổng và được giữ nguyên |
| — | — | — | — |
| — | — | — | — |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `3.13.15 / 8.4.145 / 2.11.0+cu128 / 0.5.13` |
| weights / hai tracker | `yolo26n.pt` / `bytetrack.yaml` / `configs/trackers/botsort-reid.yaml` |
| conf / IoU / imgsz / classes | `0.25 / 0.7 / 960 / [2, 5, 7]` |
| device | `0` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.8172 | 0.7970 | 0.8399 | 0.8913 | 0.9532 | 0.9023 | 0.8805 | 53 | 3 | 0 |
| ByteTrack control vs gold | 0.7085 | 0.6487 | 0.7761 | 0.8463 | 0.8746 | 0.7487 | 0.8226 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.7635 | 0.7110 | 0.8204 | 0.8721 | 0.9001 | 0.7923 | 0.8595 | 91 | 26 | 2 |
| ReID vs bạn | 0.7691 | 0.7133 | 0.8293 | 0.9126 | 0.8723 | 0.7448 | 0.9065 | 86 | 71 | 2 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

MOTA của tôi là **0.9023**, thấp hơn IDF1 **0.9532**. Bản annotation có `IDSW = 0`, nên identity được giữ khá ổn. Nếu một hệ thống có MOTA cao nhưng IDF1 thấp thì có thể nó vẫn phát hiện được nhiều object và không có quá nhiều FP/FN, nhưng identity của cùng một xe lại bị gãy hoặc gán nhầm qua các frame. MOTA tính lỗi theo FP, FN và số lần ID switch, nên một lần đổi ID chỉ đóng góp như một lỗi switch; IDF1 nhìn trực tiếp mức độ nhất quán của identity trên toàn bộ các detection nên nhạy hơn với lỗi ID kéo dài.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

BoT-SORT + ReID tốt hơn ByteTrack ở **IDF1: 0.9001 > 0.8746** và **AssA: 0.8204 > 0.7761**, còn **IDSW đều bằng 2**. Một ví dụ là quanh **MOT frame 59**, ByteTrack đổi GT track 4 từ prediction track 14 sang 15. Trong diagnostics của ReID không có ID switch cho GT track 4, nên ở sequence này treatment giữ identity tốt hơn. Tuy vậy ReID vẫn có hai ID switch khác, tại frame 87 (GT track 5) và frame 113 (GT track 6), nên nó không loại bỏ hoàn toàn lỗi association. Đây là so sánh hệ thống ByteTrack với BoT-SORT + ReID, **không phải causal ablation chỉ riêng ReID**, vì hai tracker implementation khác nhau.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

Từ ByteTrack sang BoT-SORT + ReID, **DetA tăng từ 0.6487 lên 0.7110**, **FN giảm mạnh từ 54 xuống 26**, nhưng **FP tăng nhẹ từ 88 lên 91**. Như vậy treatment bao phủ object tốt hơn, đặc biệt giảm bỏ sót, nhưng vẫn sinh thêm một ít detection/track thừa. Lỗi còn lại là hỗn hợp: FP/FN phản ánh coverage/detection-tracking, còn `IDSW = 2` và các fragmented track cho thấy association vẫn chưa hoàn hảo. Vì detector input được giữ cố định, khác biệt giữa hai hàng không nên được diễn giải là detector model đã thay đổi.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

Tại **MOT frame 87, GT track 5**, ReID có ID switch từ prediction track **17 → 18**. Trong annotation của tôi, kết quả so với gold có **IDSW = 0** và track 5 không bị tách identity ở điểm này. Vì vậy đây là một ví dụ annotation giữ đúng một ID cho xe, còn ReID đổi ID.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

Diagnostics của ReID ghi **prediction track 7** là `ghost_pred_track`, xuất hiện từ **frame 16 đến 116** và không khớp track tham chiếu nào. Vì vậy ở trường hợp này evidence nghiêng về việc model tạo track thừa, không phải annotation cần sửa theo model. Đây cũng là lý do model chỉ được dùng để chẩn đoán chứ không dùng làm nhãn chuẩn.

## 6. Nếu phải gán thêm 10 clip nữa

Tôi sẽ bổ sung `GUIDELINE_MINI.md` rõ hơn ở ba chỗ đã gây phân vân: (1) khi vật thể bị hàng rào/xe khác che thì giữ ID và bbox chỉ theo phần nhìn thấy, (2) hai xe chồng nhau vẫn phải là hai track/ID riêng, và (3) bbox xe gồm các bộ phận nhìn thấy như gương nhưng không lấy ánh đèn hắt, bóng đổ hoặc phần bị che. Tôi cũng sẽ đặt keyframe dày hơn quanh entry/exit, occlusion, crossing và lúc xe đổi hướng/kích thước nhanh; đồng thời ghi lại ngay frame + ID của mọi ca mơ hồ để self-QC hoặc reviewer có evidence cụ thể, thay vì nhớ lại sau cùng.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md` (file này)

`TEAM.md`: **N/A — làm cá nhân**.
