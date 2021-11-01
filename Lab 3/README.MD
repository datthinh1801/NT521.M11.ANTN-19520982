# Stack Overflow
![image](https://user-images.githubusercontent.com/44528004/139584092-a281940f-7887-4f85-84d4-ee5eed701eb9.png)

## Câu 1
### Question
Tại sao hàm `malloc(0xf)` lại trả về một kích thước `chunk 0x20 == 32`.

### Answer
Theo [trang này](https://web.eecs.utk.edu/~huangj/cs360/360/notes/Malloc1/lecture.html), ta biết được là `malloc()` sẽ cấp vùng nhớ có độ lớn là bội của 8. Vậy `malloc(0xf)` sẽ cấp `0x10` bytes. Bên cạnh đó, theo [trang này](https://www.blackhat.com/presentations/bh-usa-07/Ferguson/Presentation/bh-usa-07-ferguson.pdf), khi một chunk được cấp cho user, sẽ có 16 byte phía trước chunk đó trên heap chứa thông tin metadata cho chunk được cấp. Do đó, `malloc(0xf)` sẽ cấp một vùng nhớ có kích thước `0x10 + 0x10 = 0x20` bytes.  

```
    chunk-> +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
            |             Size of previous chunk, if unallocated (P clear)  |
            +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
            |             Size of chunk, in bytes                     |A|M|P|
      mem-> +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
            |             User data starts here...                          .
            .                                                               .
            .             (malloc_usable_size() bytes)                      .
            .                                                               |
nextchunk-> +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

## Câu 2
### Question
Sinh viên tự tìm hiểu và giải thích ngắn gọn về **procedure linkage table** và **global offset table** trong ELF Linux.

### Answer
GOT là một cấu trúc dữ liệu được lưu tại *data segment* của tiến trình. Nếu một chương trình có sử dụng các hàm được định nghĩa trong các shared libraries, khi chương trình được load vào RAM, absolute address của các hàm này sẽ được resolve bởi dynamic linker và gán vào GOT. Do đó khi các hàm này được gọi, tiến trình sẽ nhảy tới entry tương ứng của hàm đó trên GOT để nhảy đến địa chỉ thực sự của hàm trên RAM và thực thi hàm.  

Tuy nhiên, nếu chương trình có sử dụng nhiều hàm được định nghĩa trong các shared libraries sẽ khiến cho dynamic linker phải resolve nhiều địa chỉ tại load time. Do đó, PLT được tạo ra và sử dụng chung với GOT nhằm thực hiện cơ chế ***lazy binding***. PLT cũng là một cấu trúc dữ liệu và được lưu tại *code segment* của tiến trình. Mỗi entry trên PLT sẽ gồm các instruction giúp thực hiện việc dynamic linking tại runtime.  

***Lazy binding*** hoạt động như sau:  
Ở lần đầu tiên hàm được gọi (hàm này được định nghĩa trong shared library):
- Tiến trình sẽ nhảy đến PLT entry tương ứng của hàm và thực thi instruction đầu tiên.
- Instruction đầu tiên sẽ nhảy đến GOT entry của hàm đó. Tuy nhiên, GOT entry lúc này chưa có absolute address của hàm mà trỏ ngược lại instruction thứ 2 của PLT entry.
- Instruction thứ 2 trở đi sẽ thực hiện việc setup các tham số để dynamic linker có thể sử dụng trong quá trình resolve absolute address cho hàm. Sau khi setup, dynamic linker sẽ dược gọi để thực hiện việc resolve.
- Sau khi resolve, absolute address của hàm sẽ được gán vào GOT entry tương ứng.
- Cuối cùng, tiến trình sẽ thực thi hàm tại địa chỉ vừa mới được gán vào GOT entry.

Ở các lần gọi sau:  
- Ở các lần gọi hàm sau, tiến trình vẫn sẽ nhảy đến PLT entry của hàm đó để thực hiện instruction đầu tiên và instruction này sẽ nhảy đến GOT entry tương ứng (tương tự như lần đầu tiên gọi hàm).
- Tuy nhiên ở lần này, absolute address của hàm đã được resolved và lưu vào GOT entry trước đó, nên tiến trình sẽ nhảy đến absolute address này và thực thi hàm mà không cần phải setup và resolve như lần gọi đầu tiên.

Ảnh minh họa:  

![image](https://user-images.githubusercontent.com/44528004/139620925-4c8ac6f7-dee1-4f60-bbd2-2e29708c41ff.png)

Việc sử dụng PLT và GOT để thực hiện ***lazy binding*** giúp tăng hiệu năng của chương trình vì dynamic linker không cần phải resolve tất cả absolute address tại load time mà thay vào đó, chỉ có các hàm thật sự được sử dụng *ít nhất một lần* mới cần được resolve.

## Câu 3
> Đã làm tại lớp.
