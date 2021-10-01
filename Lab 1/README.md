# Automating Everything with Code
![](https://previews.123rf.com/images/karpenkoilia/karpenkoilia1805/karpenkoilia180500009/102165920-vector-line-web-concept-for-programming-linear-web-banner-learn-to-code-.jpg)

GVHD: Nghi Hoàng Khoa
SVTH: Nguyễn Đạt Thịnh - MSSV: 19520982

## Câu 1, 2, 3
Đã làm tại lớp.

## Câu 4
Khi chạy 1 docker container, các file trong container này có thể được thêm, xóa, sửa trong quá trình hoạt động của container. Tuy nhiên, khi container bị xóa, các files này cũng sẽ bị xóa vì các file này trong container sẽ độc lập với máy host. Vậy nếu chúng ta muốn lưu lại các file này, chúng ta sẽ dùng **volumes** trong Docker.  

**Volumes** sẽ giúp ta *mount* một thư mục trên máy host vào 1 *mountpoint* nhất định trên container. Qua đó, các tương tác của container lên thư mục này cũng sẽ trở thành tương tác với thư mục trên máy host. Và vì chúng ta biết đường dẫn của thư mục này, nên chúng ta có thể dễ dàng tương tác trực tiếp với thư mục nào trên máy host.
Trên **docker-cli**, để chỉ ra mapping này, ta dùng option `-v path_of_dir_on_host:path_of_dir_on_container` khi `start` một container.

## Câu 5
### Bước 1: Tạo job mới

