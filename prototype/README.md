# Prototype — T3 Du lịch · Nhóm Homnaytroimua

**Cách mở:** tải repo về, mở `index.html` bằng trình duyệt (Chrome, Edge). Một file HTML, không backend, không đăng nhập, không gọi AI thật, không gửi dữ liệu đi đâu.

**Ca khó:** khách xác nhận mơ hồ ("Ừ ok") và khách báo đổi nhầm, cùng ca hoàn vé "Thôi huỷ hết đi". Spec §3 Ca A và Ca B.

**Hai màn hình:**

- **Màn 1 · Khách:** cuộc chat với trợ lý trong Đặt chỗ của tôi.
- **Màn 2 · Nhân viên hỗ trợ:** màn của người được báo khi trợ lý chuyển ca.

**Bấm qua ca demo:**

1. Bấm "Ừ ok". Trợ lý không đổi vé mà hỏi lại bằng thẻ xác nhận.
2. Trên thẻ: chọn chuyến, tích hành khách, bấm "Xác nhận đổi".
3. Nhánh (a), vé chưa xuất: nhắn "Ơ sao lại 7h?? Mình bảo 9h30 mà!". Trợ lý giữ chỗ BM 205, khách bấm "Hoàn tác" rồi xác nhận lại.
4. Nhánh (b), vé đã xuất: bấm "Giả lập: vé mới đã xuất ngay" rồi mới nhắn câu ở bước 3. Trợ lý giữ chỗ BM 205, chuyển nhân viên, hỏi "Nhầm ở đâu?". Mở Màn 2 để xem nhân viên thấy gì.

**Thử ca lạ:** gõ vào ô nhắn tin. Ví dụ: bồi thường, đổi tên người bay, cho bé bay một mình, tin tự xưng nhân viên hãng, số thẻ, hoặc câu trợ lý không hiểu. Câu trả lời đi theo luật dựng sẵn, đúng mức tự chủ ở spec §4.

## Ảnh có đánh số

| Ảnh | Nội dung |
|---|---|
| [anh-1-khach.png](anh-1-khach.png) | Màn 1, toàn bộ ca: "huỷ hết đi" → quay lại đổi → "Ừ ok" → xác nhận → vé xuất → "mình bảo 9h30 mà" → chuyển nhân viên |
| [anh-2-nhanvien.png](anh-2-nhanvien.png) | Màn 2: nhân viên nhận ca đổi nhầm |
| [anh-3-the-xac-nhan.png](anh-3-the-xac-nhan.png) | Màn 1: thẻ xác nhận đổi chuyến lúc còn bấm được (sau "Ừ ok") |

| Số | Ở đâu | Nguyên tắc | Mức tự chủ |
|---|---|---|---|
| 1 | Nhãn "Trợ lý AI" trên mọi tin; lời giới thiệu đầu cuộc chat nói rõ việc làm được, việc không làm | G1 (+ Luật 134/2025 Điều 11) | — |
| 2 | Khung "Mình chắc / Mình chưa chắc" và dòng "Dựa vào" | G11 + PAIR Explainability | — |
| 3 | Sau "Ừ ok": "Mình chưa đổi vé đâu nhé…", hỏi chuyến nào, cho ai | G10 | — |
| 4 | Khung giữ chỗ: đếm ngược 20:00, "Nhả chỗ", "Giữ thêm 20 phút" | G8 | `giu_cho_chuyen_moi` · tự làm (đảo được) |
| 5 | Thẻ xác nhận: chọn chuyến (BM 217 bị khoá kèm lý do) + tích từng hành khách + dòng tóm tắt "dùng lần đổi miễn phí duy nhất"; nút chỉ bật khi đủ | — | `doi_chuyen_bay` · cần khách duyệt |
| 6 | Lệnh đổi đang xuất vé, có nút "Hoàn tác" và đếm ngược (nhánh a) | G9 | `doi_chuyen_bay` · hoàn tác trước khi xuất vé |
| 7 | Nhánh (b): giữ chỗ chuyến đúng + "Đã chuyển nhân viên · ưu tiên" | PAIR Errors + Graceful Failure | `chuyen_nhan_vien` · tự làm |
| 8 | "Nhầm ở đâu?": mình bấm nhầm / trợ lý hiểu sai ý / lý do khác | G15 | — |
| 9 | Bản nháp hoàn vé "CHƯA GỬI": chỉ có "Mở trang Hoàn tiền (bạn tự gửi)" và "Quay lại đổi chuyến", không có nút gửi | — | `gui_yeu_cau_hoan_ve` · soạn: tự làm · gửi: cấm AI |
| 10 | Màn nhân viên: tóm tắt do AI soạn, tin khách nguyên văn, nhật ký từng lần AI gọi tool (giờ, mức, ai bấm) | PAIR Errors + Graceful Failure (ai được báo) | — |

**Phần mock:** dữ liệu đặt chỗ (dữ liệu dựng của track); câu trả lời của trợ lý (luật dựng sẵn theo từ khoá, không phải AI thật); việc xuất vé (giả lập bằng đồng hồ 5 phút hoặc nút demo); trang Hoàn tiền (chỉ báo "đã mở", ngoài phạm vi); các nút của nhân viên.
