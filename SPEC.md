# SPEC - Phân tích dòng tiền ví Ethereum (90 ngày)

## 1. Mục đích

Công cụ dòng lệnh hỗ trợ chuyên viên phân tích tài chính/thẩm định tài sản số theo dõi, tổng hợp và trực quan hóa toàn bộ dòng tiền ETH vào/ra cùng biến động số dư của một địa chỉ ví cụ thể trong vòng 90 ngày gần nhất.

## 2. Đầu vào

- **Địa chỉ ví (`address`):** Dạng chuỗi ký tự (String) độ dài 42 ký tự, bắt đầu bằng tiền tố `0x`, tuân thủ định dạng địa chỉ Ethereum (hỗ trợ cả chữ hoa, chữ thường và checksum EIP-55).
- **Khóa API Etherscan (`ETHERSCAN_API_KEY`):** Chuỗi ký tự, bắt buộc đọc từ biến môi trường (Environment Variable), không hardcode trong mã nguồn.
- **Khoảng thời gian phân tích (`days`):** Số nguyên dương, mặc định là 90 ngày tính từ thời điểm thực thi (`timestamp` hiện tại trừ đi `90 * 86400` giây).

## 3. Quy tắc nghiệp vụ

- **R1 (Dòng tiền vào):** Giao dịch có trường `to` trùng khớp (không phân biệt hoa thường) với địa chỉ ví đang xét được phân loại là dòng tiền **VÀO** (`IN`).
- **R2 (Dòng tiền ra):** Giao dịch có trường `from` trùng khớp (không phân biệt hoa thường) với địa chỉ ví đang xét được phân loại là dòng tiền **RA** (`OUT`).
- **R3 (Chi phí thực tế của dòng tiền ra):** Với giao dịch đi ra thành công (`isError == "0"`), số tiền thực trừ khỏi ví = `value + (gasUsed * gasPrice)`.
- **R4 (Xử lý giao dịch lỗi):** Giao dịch đi ra nhưng thất bại (`isError == "1"`), giá trị chuyển (`value`) không bị trừ, nhưng phí giao dịch `(gasUsed * gasPrice)` vẫn bị mạng lưới trừ khỏi ví và phải được tính vào dòng tiền **RA**.
- **R5 (Quy đổi đơn vị):** Mọi giá trị số tiền (`value`, `fee`) trả về từ API ở đơn vị `wei` phải được chia cho $10^{18}$ để chuyển đổi sang đơn vị `ETH` trước khi tính toán lũy kế và hiển thị (làm tròn hiển thị 6 chữ số thập phân).
- **R6 (Thứ tự xử lý):** Toàn bộ giao dịch phải được sắp xếp theo thời gian tăng dần (`timeStamp` tăng dần) trước khi tính số dư lũy kế.
- **R7 (Tự chuyển cho chính mình - Self-transfer):** Nếu giao dịch có `from == to == address`, giá trị `value` không làm thay đổi số dư ví; giao dịch được ghi nhận là dòng tiền **RA** với số tiền bằng đúng phí giao dịch `gasUsed * gasPrice`.

## 4. Đầu ra

- **Bảng dữ liệu giao dịch chi tiết:** Hiển thị dạng bảng (Console table hoặc CSV) gồm các cột:
  1. `Thời gian`: Định dạng `YYYY-MM-DD HH:mm:ss` (UTC hoặc giờ địa phương).
  2. `Mã giao dịch (TxHash)`: Rút gọn 10 ký tự đầu/cuối.
  3. `Loại`: `VÀO` hoặc `RA`.
  4. `Số tiền (ETH)`: Giá trị ETH chuyển giao.
  5. `Phí mạng (ETH)`: Phí gas thực tế phải trả (đối với dòng tiền vào, phí hiển thị là `0.0` vì do người gửi trả).
  6. `Số dư thay đổi ròng lũy kế (ETH)`: Biến động tích lũy qua từng giao dịch trong kỳ.
- **Biểu đồ đường (Line chart):** Trục hoành ($X$) là mốc thời gian, trục tung ($Y$) là số dư thay đổi lũy kế (ETH), có lưới tọa độ và đánh dấu các điểm giao dịch lớn.
- **Báo cáo tổng hợp (Summary Metrics):**
  - Tổng dòng tiền vào (Total Inflow): `X.XXXXXX ETH`
  - Tổng dòng tiền ra (Total Outflow - gồm cả phí gas): `Y.YYYYYY ETH`
  - Chênh lệch dòng tiền ròng trong kỳ (Net Flow): `(Total Inflow - Total Outflow) ETH`
  - Tổng phí gas đã tiêu tốn trong kỳ: `Z.ZZZZZZ ETH`

## 5. Trường hợp ngoại lệ

- **E1 (Ví không có giao dịch):** Nếu API trả về danh sách rỗng (hoặc không có giao dịch nào trong 90 ngày qua), in thông báo rõ ràng: `"Vi khong co giao dich trong ky"` và kết thúc bình thường, không báo lỗi runtime (exit code 0).
- **E2 (Lỗi API / Khóa API không hợp lệ):** Nếu API trả về `status == "0"` kèm thông báo lỗi (ví dụ: `Invalid API Key`, `No transactions found`), in mã lỗi/thông báo chi tiết từ API và dừng chương trình (exit code 1).
- **E3 (Địa chỉ ví sai định dạng):** Nếu địa chỉ đầu vào không đủ 42 ký tự, không bắt đầu bằng `0x` hoặc chứa ký tự không phải hex, in thông báo lỗi định dạng `"Dia chi vi khong hop le"` và dừng ngay trước khi gọi API.
- **E4 (Phân trang khi vượt quá 10.000 giao dịch):** API Etherscan giới hạn tối đa 10.000 bản ghi mỗi lượt gọi. Nếu số lượng giao dịch đạt ngưỡng, hệ thống phải tự động lặp phân trang (`page`, `offset`) hoặc dùng `startblock` để lấy trọn vẹn dữ liệu trong 90 ngày.
- **E5 (Giới hạn tần suất gọi API - Rate Limit):** Nếu gặp lỗi `Max rate limit reached` (vượt 5 calls/giây ở gói Free), hệ thống phải tự động `sleep` 1 giây và thử lại tối đa 3 lần.

## 6. Ngoài phạm vi

- Không phân tích các giao dịch token (ERC-20, ERC-721, ERC-1155). Chỉ xét dòng tiền ETH gốc (Native ETH).
- Không phân tích giao dịch nội bộ (Internal Transactions từ hợp đồng thông minh).
- Không quy đổi giá trị ETH sang tiền pháp định (VND, USD).
- Không xây dựng giao diện Web/Mobile phức tạp (chỉ chạy dòng lệnh CLI và xuất biểu đồ hình ảnh/cửa sổ popup).

---

# PHỤ LỤC: BIÊN BẢN KIỂM TRA CHÉO (CROSS-CHECK)

> **Nhóm thực hiện kiểm tra chéo:** Nhóm phản biện BA  
> **Tài liệu thẩm định:** Bản đặc tả ban đầu tại Bước 2  
> **Kết luận:** Phát hiện **4 điểm mơ hồ / rủi ro nghiệp vụ** cần chuẩn hóa:

### 1. Điểm mơ hồ 1: Khái niệm "Số dư lũy kế" khi không có số dư ban đầu
- **Vấn đề trong bản cũ:** Đề bài yêu cầu tính "số dư lũy kế" và "số dư cuối kỳ" trong 90 ngày gần nhất. Tuy nhiên, nếu một ví đã tồn tại 3 năm, trước ngày thứ 90 ví đang có sẵn 50 ETH. Trong 90 ngày gần nhất, ví chỉ chuyển ra 10 ETH. Nếu chỉ cộng dồn từ giao dịch trong 90 ngày mà không biết số dư tại mốc 90 ngày trước, biểu đồ sẽ bắt đầu từ 0 và rơi xuống âm (-10 ETH).
- **Giải pháp làm rõ:** Cần xác định rõ đây là **"Biến động ròng lũy kế trong kỳ (Cumulative Net Flow)"** hay là **"Số dư thực tế tại từng thời điểm"**. Nếu muốn số dư thực tế, hệ thống phải bổ sung 1 lệnh gọi API truy vấn số dư ví tại block bắt đầu của kỳ 90 ngày làm số dư ban đầu ($Balance_{start}$).

### 2. Điểm mơ hồ 2: Giao dịch tự chuyển cho chính mình (`from == to`)
- **Vấn đề trong bản cũ:** R1 quy định `to == address` là tiền vào; R2 quy định `from == address` là tiền ra. Khi người dùng tự gửi ETH cho chính ví của mình (self-transfer):
  - Hệ thống nếu áp dụng đồng thời R1 và R2 sẽ cộng `value` vào tổng tiền vào, đồng thời trừ `value + fee` ở tổng tiền ra.
  - Hậu quả: Thổi phồng doanh số dòng tiền vào và ra của ví (chỉ số tài chính bị sai lệch), dù thực tế tài sản chỉ giảm đi một khoản bằng đúng tiền phí gas.
- **Giải pháp làm rõ:** Bổ sung quy tắc R7: Trường hợp `from == to`, số tiền chuyển `value` được bù trừ triệt tiêu, chỉ ghi nhận một dòng tiền ra duy nhất có giá trị bằng `fee`.

### 3. Điểm mơ hồ 3: Hiển thị phí giao dịch ở cột dòng tiền vào
- **Vấn đề trong bản cũ:** Đầu ra yêu cầu bảng gồm: `thời gian, loại (vào/ra), số tiền ETH, phí, số dư lũy kế`. Tuy nhiên, với giao dịch nhận tiền (`IN`), phí giao dịch do người gửi thanh toán, ví nhận không bị trừ tiền phí này.
- **Giải pháp làm rõ:** Phải quy định rõ: với dòng tiền vào (`IN`), cột `Phí` hiển thị là `0.0` (hoặc để trống/ghi chú "người gửi chịu"), tránh để người dùng hiểu nhầm rằng ví nhận bị khấu trừ phí đó vào số dư.

### 4. Điểm mơ hồ 4: Giao dịch nội bộ (Internal Transactions)
- **Vấn đề trong bản cũ:** Etherscan chia làm 2 API riêng: `txlist` (giao dịch thông thường) và `txlistinternal` (giao dịch sinh ra từ tương tác hợp đồng thông minh như rút ETH từ Uniswap, claim staking, nhận ETH từ multisig). Nếu đặc tả không làm rõ, lập trình viên chỉ gọi `txlist` sẽ bỏ sót toàn bộ các khoản nhận ETH từ hợp đồng.
- **Giải pháp làm rõ:** Đưa nội dung "Không xử lý giao dịch nội bộ (Internal Transactions)" vào rõ ràng tại **Mục 6 (Ngoài phạm vi)** để thống nhất phạm vi thực hiện cho lập trình viên.
