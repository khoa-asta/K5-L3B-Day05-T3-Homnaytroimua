# Prototype — T3 Du lịch · Nhóm Homnaytroimua

**Cách mở:** tải repo về, mở `index.html` bằng trình duyệt (Chrome, Edge). Một file HTML, không backend, không đăng nhập, không gọi AI thật, không gửi dữ liệu đi đâu.

**Ca khó:** khách xác nhận mơ hồ ("Ừ ok") và khách báo đổi nhầm, cùng ca hoàn vé "Thôi huỷ hết đi". Spec §3 Ca A và Ca B.

**Hai màn hình:**

- **Khách:** khung điện thoại, cuộc chat với trợ lý trong Đặt chỗ của tôi.
- **Nhân viên hỗ trợ:** bàn làm việc (hàng chờ + chi tiết ca) của người được báo khi trợ lý chuyển ca.
- Nút **"Xem song song"** trên thanh trên cùng hiện cả hai màn cạnh nhau.

**Bấm qua ca demo:**

1. Bấm "Ừ ok". Trợ lý không đổi vé mà hỏi lại bằng thẻ xác nhận.
2. Trên thẻ: chọn chuyến, tích hành khách, bấm "Xác nhận đổi".
3. Nhánh (a), vé chưa xuất: nhắn "Ơ sao lại 7h?? Mình bảo 9h30 mà!". Trợ lý giữ chỗ BM 205, khách bấm "Hoàn tác" rồi xác nhận lại.
4. Nhánh (b), vé đã xuất: bấm "Giả lập: vé mới đã xuất" rồi mới nhắn câu ở bước 3. Trợ lý giữ chỗ BM 205, chuyển nhân viên, hỏi "Nhầm ở đâu?". Bấm "Nhân viên" (hoặc "Xem song song") để xem nhân viên thấy gì.

**Thử ca lạ:** gõ vào ô nhắn tin. Ví dụ: bồi thường, đổi tên người bay, cho bé bay một mình, tin tự xưng nhân viên hãng, số thẻ, hoặc câu trợ lý không hiểu. Câu trả lời đi theo luật dựng sẵn, đúng mức tự chủ ở spec §4.

## Ảnh có đánh số

| Ảnh | Nội dung |
|---|---|
| [anh-1-khach.png](anh-1-khach.png) | Màn Khách, toàn bộ ca: "huỷ hết đi" → quay lại đổi → "Ừ ok" → xác nhận → vé xuất → "mình bảo 9h30 mà" → chuyển nhân viên |
| [anh-2-nhanvien.png](anh-2-nhanvien.png) | Màn Nhân viên: nhận ca đổi nhầm |
| [anh-3-the-xac-nhan.png](anh-3-the-xac-nhan.png) | Màn Khách: thẻ xác nhận đổi chuyến lúc còn bấm được (sau "Ừ ok") |

| Số | Ở đâu | Nguyên tắc | Mức tự chủ |
|---|---|---|---|
| 1 | Nhãn "Trợ lý AI" trên mọi tin; lời giới thiệu đầu cuộc chat nói rõ việc làm được, việc không làm | G1 (+ Luật 134/2025 Điều 11) | — |
| 2 | Hai dòng "Chắc / Chưa chắc" và dòng "Dựa vào" | G11 + PAIR Explainability | — |
| 3 | Sau "Ừ ok": "Mình chưa đổi vé đâu nhé…", hỏi chuyến nào, cho ai | G10 | — |
| 4 | Thanh giữ chỗ ghim trên đầu chat: đếm ngược 20:00, nút "Nhả", "+20 phút" | G8 | `giu_cho_chuyen_moi` · tự làm (đảo được) |
| 5 | Thẻ xác nhận: chọn chuyến (BM 217 bị khoá kèm lý do) + tích từng hành khách + dòng tóm tắt "dùng lần đổi miễn phí duy nhất"; nút chỉ bật khi đủ | — | `doi_chuyen_bay` · cần khách duyệt |
| 6 | Lệnh đổi đang xuất vé, có nút "Hoàn tác" và đếm ngược (nhánh a) | G9 | `doi_chuyen_bay` · hoàn tác trước khi xuất vé |
| 7 | Nhánh (b): giữ chỗ chuyến đúng + "Đã chuyển nhân viên · ưu tiên" | PAIR Errors + Graceful Failure | `chuyen_nhan_vien` · tự làm |
| 8 | "Nhầm ở đâu?": mình bấm nhầm / trợ lý hiểu sai ý / lý do khác | G15 | — |
| 9 | Bản nháp hoàn vé "CHƯA GỬI": chỉ có "Mở trang Hoàn tiền (bạn tự gửi)" và "Quay lại đổi chuyến", không có nút gửi | — | `gui_yeu_cau_hoan_ve` · soạn: tự làm · gửi: cấm AI |
| 10 | Màn nhân viên: tóm tắt do AI soạn, tin khách nguyên văn, nhật ký từng lần AI gọi tool (giờ, mức, ai bấm) | PAIR Errors + Graceful Failure (ai được báo) | — |

**Phần mock:** dữ liệu đặt chỗ (dữ liệu dựng của track); câu trả lời của trợ lý (luật dựng sẵn theo từ khoá, không phải AI thật); việc xuất vé (giả lập bằng đồng hồ 5 phút hoặc nút demo); trang Hoàn tiền (chỉ báo "đã mở", ngoài phạm vi); các nút của nhân viên.

## Lời gọi AI thật (bonus) — `ai-call/`

Script [`ai-call/ai_chon_tool.py`](ai-call/ai_chon_tool.py) gọi OpenAI thật cho quyết định chính: **đọc tin khách → chọn tool**. Sau đó **cổng mức tự chủ** (code, không phải model) áp spec §4: `doi_chuyen_bay` chỉ thành thẻ xác nhận, `gui_yeu_cau_hoan_ve` bị hạ xuống bản nháp, trẻ dưới 12 tuổi bay một mình → chuyển nhân viên, giữ chỗ chỉ khi chuyến đủ chỗ. Nếu cổng đã chặn, câu trả lời của model cũng bị thay để trợ lý không hứa việc chưa làm.

Chỉ dùng thư viện có sẵn của Python 3 (không cần `pip install`).

1. Copy `.env.example` (thư mục gốc repo) thành `.env`, dán key sau `OPENAI_API_KEY=`. `.env` đã nằm trong `.gitignore`.
2. Từ thư mục gốc repo: `python prototype/ai-call/ai_chon_tool.py --all` → chạy 7 ca mẫu (2 ca đắt nhất, các ca khó của track, 1 ca bình thường). Không có `--all` thì chỉ chạy 1 lời gọi cho ca "Ừ ok".
3. Thử ca lạ: `python prototype/ai-call/ai_chon_tool.py "tin nhắn của khách"`.
4. Mọi lần chạy đều ghi nối vào [`ai-call/ai-log.md`](ai-call/ai-log.md) (bảng: model chọn gì → cổng mức tự chủ để lại gì → trợ lý nói gì). `ai-call/ai-log.json` giữ bản đầy đủ của lần chạy gần nhất (lý do model chọn, số token).
