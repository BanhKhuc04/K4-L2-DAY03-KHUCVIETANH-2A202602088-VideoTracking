# Peer review — Day 3

> **Lưu ý trung thực:** bài này được thực hiện cá nhân và người làm không thực hiện kiểm chéo với một học viên khác. File được tạo để giữ đúng đường dẫn artifact bắt buộc, nhưng không tạo finding/reviewer giả. Nếu Lab Coach yêu cầu reviewer khác người làm, phần này cần được một reviewer thật điền trước khi nộp để lấy đủ điểm kiểm chéo.

| Trường | Giá trị |
| --- | --- |
| Author | Khúc Việt Anh |
| Reviewer | Khúc Việt Anh — self-review, làm cá nhân; không có peer reviewer |
| Pair ID | N/A |
| CVAT version | Không ghi nhận |
| Thời điểm review | 15/09/2026; không ghi nhận giờ cụ thể |

## Danh sách finding

Không có finding từ **peer reviewer** vì không thực hiện kiểm chéo. Không điền lỗi giả từ gold/model vào phần này.

| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | --- | --- | --- | --- |
| 1 | N/A | N/A | N/A | N/A | Không có peer reviewer | Cần reviewer thật nếu rubric bắt buộc kiểm chéo | needs-review |
| 2 | N/A | N/A | N/A | N/A | Không tạo finding giả | — | needs-review |
| 3 | N/A | N/A | N/A | N/A | Không tạo finding giả | — | needs-review |

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | N/A | Không có peer reviewer; bản MOT cuối có 8 track nhưng đây không phải peer evidence |
| Một xe giữ một ID; không reuse ID cho xe khác | N/A | Không có peer reviewer |
| Occlusion ngắn giữ ID; crossing không đổi ID | N/A | Không có peer reviewer |
| Entry/exit đúng; không box treo sau khi xe rời khung | N/A | Không có peer reviewer |
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | N/A | Không có peer reviewer |
| Frame giữa hai keyframe không bị interpolation drift | N/A | Không có peer reviewer |
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | N/A | Validator hiện tại: `clip_01` và `clip_02` đều 0 lỗi định dạng; đây là self-check, không phải peer review |
| Mọi finding có cách sửa và closure do tác giả điền | N/A | Không có peer finding |

## Self-QC attestation của reviewer

Người làm xác nhận đã thực hiện **3 lượt tự kiểm**, nhưng không có log frame–ID độc lập được cung cấp cho từng lượt. Vì vậy không tự dựng evidence chi tiết.

| Lượt | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence |
| --- | --- | --- |
| 1 — identity/timeline | NEEDS-REVIEW | Đã tự kiểm; không có log frame–ID riêng |
| 2 — endpoint/scope | NEEDS-REVIEW | Đã tự kiểm; không có log frame–ID riêng |
| 3 — geometry/interpolation | NEEDS-REVIEW | Đã tự kiểm; không có log frame–ID riêng |

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: `N/A — không có peer reviewer; không tạo finding giả.`
2. Một finding tác giả đóng là `not-a-defect`, kèm lý do (nếu có): `N/A`.
3. Một rule cần Lab Coach làm rõ (nếu có): `Khi học viên làm cá nhân, reports/review_partner.md có bắt buộc một reviewer khác người làm hay self-review được chấp nhận?`
