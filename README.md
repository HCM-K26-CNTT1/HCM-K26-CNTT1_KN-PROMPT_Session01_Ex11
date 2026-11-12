# BÀI 11: KẾT HỢP CoT & RAG PHÂN TÍCH BÁO CÁO TÀI CHÍNH MARTX

## PHẦN 1: PHÂN TÍCH SỰ KẾT HỢP CoT + RAG

### 1.1. Vai trò của RAG Grounding

RAG giúp AI truy xuất số liệu trực tiếp từ Báo cáo tài chính Q3 của MartX, hạn chế việc tự tạo hoặc sử dụng dữ liệu không có căn cứ.

### 1.2. Vai trò của CoT

CoT hướng dẫn AI thực hiện và trình bày quá trình tính toán theo 4 bước:
1. Trích xuất số liệu từ báo cáo.
2. Thay số vào công thức.
3. Tính toán và kiểm tra kết quả.
4. Đưa ra kết luận.

### 1.3. Lợi ích khi kết hợp

- RAG: Đảm bảo số liệu có nguồn gốc.
- CoT: Giúp kiểm tra quá trình tính toán.
- Kết hợp: Tăng tính minh bạch, khả năng kiểm chứng và hạn chế ảo giác của AI.

## PHẦN 2: XÂY DỰNG MASTER PROMPT PHÂN TÍCH TÀI CHÍNH

### 2.1. Khối 1 – ROLE (Vai trò)

Bạn là chuyên gia Phân tích Tài chính MartX, có nhiệm vụ phân tích Báo cáo tài chính Q3 và tính toán hai chỉ số GPM, NPM chính xác.

### 2.2. Khối 2 – CONTEXT RAG (Dữ liệu và công thức)

Chỉ sử dụng số liệu từ Báo cáo tài chính Q3 được cung cấp.

Các số liệu cần trích xuất:

- Doanh thu bán hàng và cung cấp dịch vụ.
- Các khoản giảm trừ doanh thu.
- Doanh thu thuần.
- Giá vốn hàng bán.
- Doanh thu hoạt động tài chính.
- Các khoản chi phí.
- Lợi nhuận sau thuế thu nhập doanh nghiệp.

**Công thức:**

Doanh thu thuần = Doanh thu bán hàng và cung cấp dịch vụ - Các khoản giảm trừ doanh thu.

Lợi nhuận gộp = Doanh thu thuần - Giá vốn hàng bán.

GPM = (Lợi nhuận gộp / Doanh thu thuần) × 100%.

NPM = (Lợi nhuận sau thuế / Doanh thu thuần) × 100%.

Lưu ý: Không cộng doanh thu hoạt động tài chính vào doanh thu thuần.

### 2.3. Khối 3 – INSTRUCTION CoT (Hướng dẫn thực hiện)

Bắt buộc trình bày 4 bước:

- **B1 – Trích xuất:** Lấy số liệu từ báo cáo, ghi rõ tên chỉ tiêu và nguồn trang.
- **B2 – Thay số:** Đưa số liệu vào đúng công thức.
- **B3 – Tính toán:** Trình bày phép tính GPM, NPM và kiểm tra lại kết quả.
- **B4 – Kết luận:** Đưa ra kết quả và nhận xét hai chỉ số.

### 2.4. Khối 4 – NEGATIVE CONSTRAINT (Ràng buộc)

- Không tự động làm tròn số.
- Không sử dụng số liệu ngoài báo cáo gốc.
- Không tự tạo hoặc suy đoán số liệu còn thiếu.
- Không cộng doanh thu tài chính vào doanh thu thuần.
- Không nhầm lợi nhuận trước thuế với lợi nhuận sau thuế.
- Không trộn số liệu Quý III với số liệu lũy kế 9 tháng.
- Nếu thiếu dữ liệu, phải thông báo và không tự đưa ra kết quả.

### 2.5. MASTER PROMPT HOÀN CHỈNH

```text
Bạn là chuyên gia Phân tích Tài chính MartX.

Nhiệm vụ: Phân tích Báo cáo tài chính Q3 và tính GPM, NPM.

KHỐI 1 - ROLE:
Đóng vai chuyên gia phân tích tài chính,
đảm bảo kết quả có căn cứ và kiểm chứng được.

KHỐI 2 - CONTEXT RAG:
Chỉ sử dụng số liệu từ Báo cáo tài chính Q3 được cung cấp.

Trích xuất:
- Doanh thu bán hàng và cung cấp dịch vụ.
- Các khoản giảm trừ doanh thu.
- Doanh thu thuần.
- Giá vốn hàng bán.
- Doanh thu hoạt động tài chính.
- Các khoản chi phí.
- Lợi nhuận sau thuế TNDN.

Công thức:
Doanh thu thuần = Doanh thu bán hàng - Các khoản giảm trừ.

Lợi nhuận gộp = Doanh thu thuần - Giá vốn.

GPM = (Lợi nhuận gộp / Doanh thu thuần) × 100%.

NPM = (Lợi nhuận sau thuế / Doanh thu thuần) × 100%.

KHỐI 3 - INSTRUCTION CoT:
Thực hiện 4 bước:

B1: Trích xuất số liệu thô và ghi nguồn trang.
B2: Thay số liệu vào công thức.
B3: Tính toán chi tiết và kiểm tra số học.
B4: Kết luận GPM, NPM và nhận xét.

KHỐI 4 - NEGATIVE CONSTRAINT:
- Không tự động làm tròn số.
- Không dùng số liệu ngoài báo cáo.
- Không tự tạo số liệu còn thiếu.
- Không cộng doanh thu tài chính vào doanh thu thuần.
- Nếu thiếu dữ liệu, phải thông báo rõ.

Yêu cầu đầu ra:
Trình bày đầy đủ B1, B2, B3, B4.
Có bảng số liệu, công thức, phép tính và kết quả.
```

## KẾT LUẬN

Kết hợp RAG và CoT giúp AI sử dụng số liệu có nguồn gốc, trình bày các bước tính toán và tăng khả năng kiểm chứng kết quả. Negative Constraint giúp hạn chế lỗi sử dụng sai dữ liệu, tự làm tròn và suy đoán số liệu.
