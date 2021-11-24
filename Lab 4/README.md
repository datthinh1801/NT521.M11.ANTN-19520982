# Format string
## Câu 1
### Question
Điền giới thiệu về các hàm trên bảng.

### Answer
| Hàm | Giới thiệu |
|---|---|
| `printf` | Tạo một chuỗi từ định dạng và các đối số được truyền vào và in chuỗi đó ra stdout. |
| `fprintf` | Tạo một chuỗi từ định dạng và các đối số được truyền vào và in chuỗi đó ra một output stream.|
| `vprintf` | Tạo một chuỗi từ định dạng và các đối số được truyền vào thông qua 1 tham số `va_list` và in chuỗi đó ra stdout.|
| `vfprintf` | Tạo một chuỗi từ định dạng và các đối số được truyền vào thông qua 1 tham số `va_list` và in chuỗi đó ra một output stream.|
| `sprintf` | Tạo một chuỗi từ định dạng và đối số cho trước và xuất chuỗi đó ra một mảng ký tự.|
| `snprintf` | Tạo một chuỗi từ định dạng và đối số cho trước và xuất chuỗi đó ra một mảng ký tự với giới hạn số lượng ký tự được xuất ra.|
| `vsprintf` | Tạo một chuỗi từ định dạng và các đối số được truyền vào thông qua 1 tham số `va_list` và xuất chuỗi đó ra một mảng ký tự.|
| `vsnprintf` | Tạo một chuỗi từ định dạng và các đối số được truyền vào thông qua 1 tham số `va_list` và xuất chuỗi đó ra một mảng ký tự với giới hạn số lượng ký tự được xuất ra.|
| `setproctitle` | Đặt tên cho tiến trình sử dụng format string để tạo chuỗi tên. |
| `syslog` | Tạo log message để truyền vào system logger. Log message được tạo từ format string. |
| `err`, `verr`, `warn`, `vwarn` | Hiển thị error message. Error message được tạo từ format string. |
