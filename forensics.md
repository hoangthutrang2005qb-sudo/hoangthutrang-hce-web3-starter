# Báo cáo Lab 3 - Phân tích On-chain (Forensics)

## Phần 1: Mổ xẻ giao dịch cá nhân
**Mã băm giao dịch (Txn Hash):** `0x0ad3c5525a3ee1e69faecc9cf20881a1d16351a19430df0356b806d261e6226d`

| Trường (Field) | Giá trị thực tế của tôi | Ý nghĩa | Vì sao người làm nghiệp vụ cần |
| :--- | :--- | :--- | :--- |
| **Status** | Success | Trạng thái giao dịch | Giao dịch thất bại vẫn mất phí — ảnh hưởng hạch toán |
| **Block** | 11770308 | Số thứ tự khối | Xác định thời điểm ghi nhận |
| **Timestamp** | Sep-24-2026 06:43:48 AM +UTC | Thời gian | Mốc ghi nhận doanh thu / chi phí |
| **From / To** | `0xfB3dfe...71D1` / `0x5b4847...0684` | Ví gửi / ví nhận | Đối tượng cần xác minh danh tính |
| **Value** | 0.01 ETH | Số tiền chuyển | Giá trị giao dịch |
| **Transaction Fee** | 0.000053655147567 ETH | Phí thực trả | Chi phí giao dịch, cần hạch toán riêng |
| **Gas Price** | 2.555007027 Gwei | Đơn giá phí | Giải thích vì sao cùng một giao dịch mà phí khác nhau |
| **Gas Limit** | 31,500 | Mức gas tối đa cho phép | Đặt quá thấp -> giao dịch thất bại (Out of Gas) nhưng vẫn mất phí |
| **Gas Used** | 21,000 | Lượng gas thực tế tiêu thụ | Txn Fee = Gas Used × Gas Price. Cảnh báo hết gas nếu bằng Gas Limit |
| **Nonce** | 243 | Số thứ tự giao dịch ví gửi | Phát hiện giao dịch bị bỏ sót hoặc thay thế |

---

## Phần 2: Đọc hợp đồng thật (USDC / USDT)

**Câu 1: Hợp đồng bạn xem có công bố mã nguồn đã xác thực không?**
> **Trả lời:** Có. Hợp đồng của USDC (hoặc USDT) trên Etherscan đều có công bố mã nguồn đã xác thực (Source Code Verified). Mã nguồn này đã được đối chiếu khớp hoàn toàn với chuỗi mã máy (Bytecode) đang chạy thực tế trên blockchain.

**Câu 2: Tổng cung của đồng đó là bao nhiêu? Đọc ra từ hàm nào?**
> **Trả lời:** Tổng cung hiện tại của đồng này luôn biến động theo thời gian thực (do việc đúc thêm hoặc đốt đi). Con số này được đọc ra một cách miễn phí (không tốn gas) từ hàm **`totalSupply()`** trong tab *Read Contract* (hoặc *Read as Proxy*).

**Câu 3: Trong tab Write Contract (với USDC: Write as Proxy), có hàm nào cho phép một địa chỉ đặc biệt đóng băng tài khoản người khác không? Nếu có, tên hàm là gì?**
> **Trả lời:** CÓ. Hàm đó tên là **`blacklist(address)`**. Hàm này cho phép một địa chỉ đặc biệt có quyền quản trị (như cơ quan phát hành hoặc tổ chức pháp lý) đưa địa chỉ ví của một cá nhân vào "danh sách đen". Khi bị đưa vào danh sách này, ví đó sẽ bị đóng băng, không thể gửi hay nhận token đó nữa. Điều này cho thấy các đồng stablecoin như USDC/USDT mang tính tập trung quyền lực rất cao.
