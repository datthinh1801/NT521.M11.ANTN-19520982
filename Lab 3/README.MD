# Stack Overflow
![image](https://user-images.githubusercontent.com/44528004/139584092-a281940f-7887-4f85-84d4-ee5eed701eb9.png)

## Câu 1
### Question
Tại sao hàm `malloc(0xf)` lại trả về một kích thước `chunk 0x20 == 32`.

### Answer
Theo [trang này](https://web.eecs.utk.edu/~huangj/cs360/360/notes/Malloc1/lecture.html), ta biết được là `malloc()` sẽ cấp vùng nhớ có độ lớn là bội của 8. Vậy `malloc(0xf)` sẽ cấp `0x10` bytes. Bên cạnh đó, theo [trang này](https://www.blackhat.com/presentations/bh-usa-07/Ferguson/Presentation/bh-usa-07-ferguson.pdf), khi một chunk được cấp cho user, sẽ có 16 byte phía trước chunk đó trên heap chứa thông tin metadata cho chunk được cấp. Do đó, `malloc(0xf)` sẽ cấp một vùng nhớ có kích thước `0x20` bytes.  

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


## Câu 3
> Đã làm tại lớp.
