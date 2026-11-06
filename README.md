# Bài 9: Xây Dựng Prompt 3 Thành Phần Giải Mã Thông Báo Lỗi Trạm Sạc Cùng Trợ Lý AI


## Phần 1: Thiết kế Prompt 3 Thành Phần

### 1. Vì sao câu hỏi cụt ngủn khiến AI bị ảo giác (Hallucination)?
- **Thiếu dữ kiện:** Không cung cấp hệ điều hành (Windows 11), dịch vụ chạy nền (`ControllerDaemon`), hay mã lỗi (`UnauthorizedAccessException`).
- **Gây hiểu lầm:** Từ khóa chung chung *"trạm sạc"* khiến AI phỏng đoán theo hướng hỏng hóc phần cứng (tháo vỏ, nối dây điện) thay vì xử lý lỗi phần mềm.

---

### 2. Prompt 3 thành phần chuẩn
```plaintext
> **[Context]**  
> Tôi đang trực vận hành hệ thống Kiosk nạp năng lượng thông minh VoltCharge trên Windows 11. Ứng dụng điều khiển `ControllerDaemon` vừa bị dừng đột ngột khi đang nạp điện cho xe.
>
> **[Raw Error]**  
> `[TERMINAL ERROR LOG - VOLTCHARGE CHARGER #04]`  
> `2026-09-21 21:15:30 [FATAL] ControllerDaemon failed to start.`  
> `System.UnauthorizedAccessException: Access to the path 'C:\Program Files\VoltCharge\data\session.log' is denied.`  
> `   at System.IO.FileStream.ValidateFileHandle(SafeFileHandle handle)`  
> `   at VoltCharge.Core.SessionManager.Initialize()`  
> `InnerException: None. Process terminated with exit code 5 (0x00000005).`  
> `Suggestion: Run the application with elevated administrator privileges or verify NTFS file permissions.`
>
> **[Instruction]**  
> 1. Đóng vai kỹ sư hệ thống, giải thích nguyên nhân gốc rễ của lỗi trên bằng tiếng Việt đơn giản cho kỹ thuật viên mới.  
> 2. Đưa ra 3 bước xử lý cụ thể trên Windows 11 (Run as Administrator & phân quyền NTFS) để trạm sạc hoạt động lại bình thường.
```
---

## Phần 2: Giải mã Lỗi & Khắc phục

### 3. Giải thích lỗi bình dân
- **Bản dịch:** Lỗi truy cập trái phép — Ứng dụng bị từ chối truy cập file `session.log` (Exit code 5).
- **Nguyên nhân:** Ứng dụng đang chạy bằng quyền tài khoản thường, cố ghi dữ liệu vào thư mục bảo vệ của hệ thống (`C:\Program Files\...`). Windows chặn hành vi ghi này lại để bảo đảm an toàn, dẫn đến tiến trình bị tắt ngang.

---

### 4. 3 bước xử lý trên Windows 11
1. **Khắc phục nhanh:** Chuột phải vào file thực thi của ứng dụng (`VoltCharge.exe`) $\rightarrow$ Chọn **Run as administrator**.
2. **Phân quyền ghi NTFS:** Chuột phải thư mục `C:\Program Files\VoltCharge\data` $\rightarrow$ **Properties** $\rightarrow$ Thẻ **Security** $\rightarrow$ **Edit...** $\rightarrow$ Chọn nhóm `Users` $\rightarrow$ Tích chọn **Modify** và **Write** $\rightarrow$ Nhấn **OK**.
3. **Cố định quyền chạy:** Chuột phải file `VoltCharge.exe` $\rightarrow$ **Properties** $\rightarrow$ Thẻ **Compatibility** $\rightarrow$ Tích vào ô **Run this program as an administrator** $\rightarrow$ **Apply**.

---

### 5. Xử lý bẫy an ninh (API_KEY)
- **Cách xử lý:** Xóa hoặc ẩn chuỗi mã bí mật trước khi gửi, đổi thành dạng: `API_KEY: [REDACTED]` hoặc `API_KEY: ******`.
- **Lý do:** Dữ liệu chat có thể bị lưu trên máy chủ bên thứ ba hoặc rò rỉ trong nhật ký hệ thống. Lộ API Key cho phép kẻ xấu chiếm quyền điều khiển trạm sạc, trục lợi điện năng hoặc can thiệp cơ sở dữ liệu.
