# BÁO CÁO LAB 07: TÍNH CHI PHÍ VẬN HÀNH THỰC TẾ TRÊN BLOCKCHAIN

**Học phần:** ECO2432 - Công nghệ Web3 và Tài sản số  
**Chủ đề:** Phân tích hiệu quả kinh tế mô hình thẻ điểm dành cho Câu lạc bộ Sinh viên

---

## 1. Phương pháp và Thông số giả định

Để phân tích chi phí một cách khách quan, báo cáo xây dựng các thông số đầu vào tự tính như sau:

- **Bản chất giao dịch:** Thao tác cộng điểm cho sinh viên về bản chất là ghi dữ liệu trạng thái mới vào Smart Contract (cập nhật số dư). Tham chiếu cấu trúc phí EVM, một thao tác ghi biến mới tốn khoảng **20.000 gas**. 
- **Tần suất hoạt động:** Câu lạc bộ thực hiện **1.000 giao dịch** cộng điểm mỗi tháng.
- **Biến động thị trường (Giả định):** 
  - Đơn giá gas mạng Layer 1 (Ethereum) trung bình: **20 Gwei**.
  - Tỷ giá ETH hiện tại: **3.000 USD/ETH**.
  - Tỷ giá quy đổi tiền Việt: **25.000 VNĐ/USD**.
- **Công thức tính cơ bản:**
  - `Phí (ETH) = Lượng gas tiêu thụ × Đơn giá gas (Gwei) × 10^-9`
  - `Phí (USD) = Phí (ETH) × Giá ETH`

---

## 2. Bảng tính chi phí vận hành hàng tháng

Dưới đây là bảng so sánh chi phí vận hành nếu câu lạc bộ chạy ứng dụng trên mạng chính Ethereum (Layer 1) so với các giải pháp mở rộng mạng lưới (Layer 2) có mức phí rẻ hơn 100 lần.

| Hạng mục đánh giá | Mạng Ethereum (Layer 1) | Giải pháp Layer 2 (Optimism/Arbitrum) | Mức độ tối ưu |
| :--- | :--- | :--- | :--- |
| Gas tiêu thụ mỗi lượt | 20.000 gas | 20.000 gas | Không đổi |
| Đơn giá gas | 20 Gwei | 0.2 Gwei | Tiết kiệm 100 lần |
| Chi phí 1 lượt cộng điểm (ETH) | 0,0004 ETH | 0,000004 ETH | Giảm mạnh |
| Chi phí 1 lượt cộng điểm (USD) | 1,20 USD | 0,012 USD | ~ Giảm 99% |
| Chi phí quy đổi VNĐ / lượt | ~ 30.000 VNĐ | ~ 300 VNĐ | Rất rẻ |
| **Tổng chi phí 1 tháng (1.000 lượt)** | **1.200 USD** | **12 USD** | Tiết kiệm 1.188 USD |
| **Tổng chi phí 1 tháng (VNĐ)** | **~ 30.000.000 VNĐ** | **~ 300.000 VNĐ** | Phù hợp ngân sách |

---

## 3. Phân tích bài toán thực tế

**a. Chi phí hàng tháng trên Ethereum (Layer 1):**
Áp dụng công thức tính toán, chi phí vận hành ứng dụng trên Layer 1 sẽ lên tới **1.200 USD/tháng** (tương đương 30 triệu đồng). 

**b. Chi phí hàng tháng nếu chuyển sang Layer 2:**
Khi tận dụng các mạng Layer 2, chi phí vận hành giảm đi 100 lần, chỉ còn **12 USD/tháng** (khoảng 300 ngàn đồng).

**c. Phân bổ chi phí và sự chấp nhận của sinh viên:**
- **Ai trả phí?** Về nguyên tắc thiết kế Web3 cơ bản, người khởi tạo giao dịch (sinh viên) sẽ phải trả phí gas mạng lưới. Nếu tích hợp Account Abstraction (ERC-4337), Câu lạc bộ có thể thiết kế cơ chế trả thay (Paymaster).
- **Sinh viên có đồng ý không?**
  - Trả 30.000 VNĐ (Layer 1) chỉ để được "cộng điểm" thẻ CLB là điều **bất khả thi**. Sinh viên sẽ lập tức từ chối ứng dụng.
  - Trả 300 VNĐ (Layer 2) là con số cực nhỏ. Tuy nhiên, việc bắt buộc sinh viên phải biết mua và nạp tiền điện tử vào ví chỉ để làm phí gas sẽ tạo ra rào cản thao tác (UX) rất lớn. Do đó, hợp lý nhất là Câu lạc bộ tự gánh khoản phí bảo trì 300.000 VNĐ/tháng này.

**d. Đánh giá tính khả thi chung:**
Mô hình thẻ điểm blockchain **thất bại hoàn toàn trên Layer 1** do phí bảo trì hạ tầng vượt quá xa giá trị mang lại. Ngược lại, dự án **rất khả thi trên Layer 2**. Điều kiện tiên quyết để dự án thành công là ứng dụng phải che giấu đi sự phức tạp của blockchain (gasless experience) để CLB chi trả trọn gói 12 USD/tháng, mang lại trải nghiệm Web2 thông thường cho sinh viên.

---

## 4. Mở rộng cho đồ án nhóm (Hệ thống Bán Vé Sự Kiện Bằng NFT)

Dưới đây là phần ước tính chi phí cho ý tưởng dự án thực tế của nhóm:

- **Tên dự án:** NFT Event Ticketing (Hệ thống bán vé sự kiện chống phe vé).
- **Quy mô dự kiến:** Khoảng **500 giao dịch/tháng** (tương ứng với 500 lượt phát hành/chuyển nhượng vé).
- **Phân tích kỹ thuật:** Giao dịch phát hành NFT (chuẩn ERC-721) tiêu tốn nhiều tài nguyên hơn lưu biến đơn giản, ước tính cần trung bình **65.000 gas**.
- **Dự toán chi phí L1 (Ethereum):**
  - Chi phí 1 vé (USD) = 65.000 × 20 × $10^{-9}$ × 3.000 = 3,9 USD/vé.
  - Tổng quỹ vận hành hàng tháng = 3,9 × 500 = **1.950 USD/tháng**.
- **Dự toán chi phí L2 (Polygon/Base):**
  - Phí giao dịch giảm 100 lần, chỉ còn **0,039 USD/vé** (khoảng ~1.000 VNĐ).
  - Tổng quỹ vận hành hàng tháng = 1.950 / 100 = **19,5 USD/tháng** (~500.000 VNĐ).
- **Kết luận của nhóm:** Dự án bán vé sự kiện bằng NFT có lợi thế khác biệt so với thẻ điểm sinh viên. Người tham gia thường sẵn sàng chi trả thêm một khoản phụ phí nhỏ (~1.000 VNĐ) được cộng thẳng vào giá vé ban đầu, do đó sinh viên hoàn toàn có thể tự chịu khoản gas này mà không gây phản cảm. Nhóm quyết định mô hình kinh doanh này khả thi và sẽ triển khai trên mạng Layer 2.
