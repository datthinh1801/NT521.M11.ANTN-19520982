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

## Câu 2
### Question
Dùng GDB để debug và nhập giá trị `%3$x`.

### Answer
- Khi tiến trình chạy đến bước `printf(s)`, stack sẽ như sau:  

![image](https://user-images.githubusercontent.com/44528004/143236001-c1c91a66-a374-4262-a79d-3b016fdbe6d2.png)

- Kết quả chạy là:  

![image](https://user-images.githubusercontent.com/44528004/143236049-03b9b3a5-e6cc-451b-8740-806a14286986.png)

- Từ đây ta thấy được rằng `3$` sẽ tương ứng với đối số thứ 3 được truyền vào `printf`. Tuy nhiên, do chúng ta không truyền vào đối số nào cả nên tiến trình sẽ lấy giá trị cách đó 12 bytes ở phía trên của stack. Dựa vào stack bên trên, `0xffffd064` có giá trị `0xffffd070` là đối số 1, `0xffffd068` có giá trị `0xf7fc9410` là đối số 2, và `0xffffd06c` có giá trị `0x0804918d` là đối số 3.
- Vậy nếu `s = %08x.%08x.%08x` thì chương trình sẽ in ra `ffffd070.f7fc9410.0804918d`.
- Và nếu `s = %3$x` thì chương trình sẽ in ra giá trị của đối số thứ 3 là `0804918d`.
