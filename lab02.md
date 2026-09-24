# Báo cáo Lab 2 - Thực hiện giao dịch Blockchain

## Bảng ghi nhận giao dịch

| Trường | Giao dịch thành công | Giao dịch thất bại (Tình huống A) |
| :--- | :--- | :--- |
| **Mã băm giao dịch** | `0x0ad3c5525a3ee1e69faecc9cf20881a1d16351a19430df0356b806d261e6226d` | Không có (Bị chặn ngay tại ví) |
| **Số tiền chuyển** | 0.01 Sepolia ETH | 0 Sepolia ETH |
| **Phí giao dịch thực trả**| *(Ghi số phí lấy từ Etherscan)* | 0 ETH |
| **Trạng thái** | Confirmed (Thành công) | Rejected (Bị từ chối bởi MetaMask) |
| **Nguyên nhân (nếu thất bại)** | Không có | Địa chỉ người nhận sai định dạng (Lỗi "Địa chỉ không hợp lệ") |

---

## Trả lời câu hỏi
**Nếu bạn chuyển nhầm cho người lạ, có lấy lại được không? Vì sao?**

**Trả lời:** 
Không thể tự lấy lại được số tiền đã chuyển nhầm. Lý do là vì hệ thống blockchain mang tính phi tập trung và bất biến (không thể sửa đổi hay đảo ngược giao dịch). Sẽ không có bất kỳ bên thứ ba hay tổ chức trung gian nào (như ngân hàng) có quyền can thiệp để hủy giao dịch hay "hoàn tiền" cho bạn. Cách duy nhất để lấy lại là người nhận tự nguyện chuyển trả lại tiền.
