# NHẬT KÝ LÀM VIỆC VỚI AI - Lab 04: Thẩm định rủi ro hợp đồng

## Lần 1

**Prompt:**
Bạn là chuyên viên thẩm định rủi ro tài sản số.
Dưới đây là mã nguồn một hợp đồng token. Hãy liệt kê mọi quyền đặc biệt mà
chủ sở hữu hợp đồng có thể thực hiện, và với mỗi quyền, nêu rõ:
- Tên hàm và số dòng
- Người nắm giữ token chịu rủi ro gì
Chỉ trả lời dựa trên mã nguồn tôi cung cấp. Nếu không tìm thấy, nói là không tìm thấy.
*[Dán toàn bộ mã nguồn ClubTokens.sol]*

**AI trả về:**
AI đã phân tích và liệt kê chi tiết các quyền cùng rủi ro:
- **ClubTokenA:** Có rủi ro xả hàng (Pre-mine) ở hàm `constructor` (dòng 8) do người triển khai giữ 100% cung.
- **ClubTokenB:** Có quyền `mint` (dòng 18) gây lạm phát và rủi ro xả hàng ở `constructor` (dòng 14).
- **ClubTokenC:** Có quyền `setRestricted` (dòng 30) có thể đóng băng tài khoản và rủi ro xả hàng ở `constructor` (dòng 26).

**Đánh giá:** Dùng được (Rất tốt).

**Chỗ sai:** Không có. AI đã phân tích chính xác, không bỏ sót rủi ro nào (kể cả rủi ro ẩn như đúc sẵn token) và lấy đúng số dòng.

**Cách sửa:** Không cần sửa. Bê nguyên kết quả AI phân tích để điền thẳng vào Bảng kết luận trong `lab04.md`.

**Ai phát hiện:** Không áp dụng.

---

## Phần So Sánh (Yêu cầu riêng của Lab 04)

- **Đọc thủ công tìm ra gì:** Khi đọc thủ công, sinh viên dễ dàng nhận thấy ClubTokenB có hàm `mint` (dòng 18) và ClubTokenC có hàm `setRestricted` (dòng 30). Tuy nhiên, rất dễ bỏ qua hoặc không coi hàm `constructor` là một "quyền đặc biệt".
- **AI tìm thêm được gì:** AI không chỉ tìm ra các quyền rõ ràng (mint, blacklist) mà còn "chỉ điểm" thêm được một rủi ro cực kỳ nguy hiểm ẩn sau hàm `constructor` (dòng 8, 14, 26) của cả 3 hợp đồng: rủi ro xả hàng (Pre-mine/Rug pull) do người tạo nắm giữ 100% tổng cung.
- **AI có nói sai chỗ nào không:** KHÔNG. AI tuân thủ tuyệt đối yêu cầu "chỉ trả lời dựa trên mã nguồn", không hề bịa đặt hay tự suy diễn thêm các quyền kế thừa (như `transferOwnership`). AI trả về số dòng và rủi ro chính xác hoàn toàn khớp với `lab04.md`.

---

# NHẬT KÝ LÀM VIỆC VỚI AI - Lab 06: Lập trình công cụ phân tích dòng tiền ví Ethereum

## Lần 1

**Prompt:**
```text
Đọc tệp SPEC.md trong dự án và viết chương trình Python thực hiện đúng đặc tả đó.
Tuân thủ các quy ước trong AGENTS.md.
Trước khi viết mã, tóm tắt lại cách bạn hiểu yêu cầu để tôi xác nhận.
```

**AI trả về:**
- AI tóm tắt ngắn gọn các mục tiêu và tiến hành sinh mã nguồn ban đầu của file `wallet_analyzer.py`.
- Mã nguồn lấy dữ liệu từ API Etherscan, tính toán dòng tiền vào/ra và xuất bảng số liệu.

**Đánh giá:** Dùng được một phần, nhưng còn chứa lỗi logic nghiệp vụ và lỗi tích hợp API khi đối chiếu với danh mục kiểm tra 6 điểm bắt buộc.

**Chỗ sai:**
1. **Lỗi 1 (Thuộc Điểm 4 Checklist - Giao dịch thất bại):** Khi duyệt qua danh sách giao dịch, AI viết lệnh `if isError == '1': continue` nhằm bỏ qua giao dịch lỗi. Điều này vi phạm quy tắc **R4** trong `SPEC.md`: giao dịch gửi đi bị thất bại dù không chuyển được giá trị `value`, nhưng phí mạng lưới `gasUsed * gasPrice` vẫn bị trừ khỏi ví và bắt buộc phải tính vào dòng tiền **RA**. Việc bỏ qua làm sai lệch số dư lũy kế thực tế.
2. **Lỗi 2 (Thuộc Điểm 6 Checklist - Phiên bản API Etherscan):** AI sử dụng endpoint cũ `https://api.etherscan.io/api` của Etherscan v1. Do dữ liệu huấn luyện của mô hình cũ hơn, AI không cập nhật được rằng Etherscan đã chuyển sang giao diện API hợp nhất đa chuỗi (Etherscan API v2) với endpoint `https://api.etherscan.io/v2/api` và tham số `chainid=1`. Việc dùng endpoint cũ có rủi ro bị ngừng hỗ trợ.
3. **Lỗi 3 (Thuộc Điểm 3 Checklist - Phân trang):** AI chỉ gọi API một lần với tham số `offset=10000` mà không cài đặt vòng lặp phân trang (`page`, `offset`), vi phạm ngoại lệ **E4** trong `SPEC.md` khi gặp các ví có trên 10.000 giao dịch.

**Cách sửa:**
- Sửa hàm `xu_ly_dong_tien`: Kiểm tra nếu `isError == "1"` và `from_addr == target_address`, đặt `amount = 0.0` nhưng vẫn cộng phí gas `fee_eth` vào `total_outflow` và trừ vào biến động lũy kế `cumulative_net_flow`.
- Cập nhật URL kết nối sang endpoint Etherscan v2 (`https://api.etherscan.io/v2/api?chainid=1`), đồng thời thiết lập cơ chế tự động fallback về v1 nếu cần.
- Bổ sung vòng lặp phân trang `while True` với tham số `page` tăng dần cho đến khi số lượng giao dịch nhận về nhỏ hơn `offset`.
- Đảm bảo toàn bộ chú thích trong mã viết bằng tiếng Việt không dấu theo quy ước `AGENTS.md`.

**Ai phát hiện:** Sinh viên (thông qua đối chiếu với bảng Danh mục kiểm tra bắt buộc 6 điểm tại Bước 2 bài Lab 06: Điểm 4 về giao dịch thất bại, Điểm 6 về phiên bản API Etherscan, và Điểm 3 về phân trang).

---

## Lần 2

**Prompt:**
```text
Chương trình bạn vừa sinh còn các lỗi sau đối chiếu theo AGENTS.md và SPEC.md:
1. Giao dịch thất bại (isError == "1") vẫn bị trừ phí gas, phải tính vào dòng tiền RA (Quy tắc R4).
2. Endpoint API Etherscan cần cập nhật hỗ trợ v2 đa chuỗi (chainid=1).
3. Thiếu vòng lặp phân trang khi ví có trên 10.000 giao dịch (Ngoại lệ E4).
4. Bổ sung tính năng vẽ biểu đồ số dư và lưu file balance_chart.png.
5. Toàn bộ chú thích trong mã phải viết bằng tiếng Việt không dấu.
Hãy viết lại mã nguồn hoàn chỉnh.
```

**AI trả về:**
Mã nguồn hoàn chỉnh `wallet_analyzer.py` đã khắc phục toàn bộ các lỗi:
- Hạch toán chính xác phí gas của giao dịch thất bại vào dòng tiền ra.
- Hỗ trợ endpoint Etherscan v2 kết hợp cơ chế fallback an toàn.
- Cài đặt vòng lặp phân trang đầy đủ.
- Tích hợp vẽ biểu đồ đường trực quan bằng thư viện `matplotlib` lưu ra file `balance_chart.png`.
- Chú thích 100% bằng tiếng Việt không dấu.

**Đánh giá:** Rất tốt, đáp ứng hoàn hảo toàn bộ 6/6 điểm trong bảng checklist kiểm tra.

**Chỗ sai:** Không có.

**Cách sửa:** Không cần sửa. Mã nguồn sẵn sàng chạy thực tế.

**Ai phát hiện:** Sinh viên kiểm thử và nghiệm thu mã nguồn.

