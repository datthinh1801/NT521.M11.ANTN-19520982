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
![](https://github.com/datthinh1801/NT521.M11.ANTN-19520982/blob/main/Lab%201/Create%20a%20new%20job%20for%20test.png)

### Bước 2, 3: Cấu hình Jenkins test job.
![](https://github.com/datthinh1801/NT521.M11.ANTN-19520982/blob/main/Lab%201/jenkins%20test%20job%20configuration.png)

Trong docker, các container là độc lập với nhau (isolated) do đó để các container có thể giao tiếp với nhau, chúng cần kết nối qua network. Khi 1 container được chạy, mặc định nó sẽ được kết nối vào 1 *virtual bridge network* và được cấp 1 địa chỉ IP. Trong trường hợp này, container `samplerunning` luôn được gán IP là `172.17.0.3` một cách may mắn nên để **TestAppJob** có thể `curl` `samplerunning`, ta cần `curl` địa chỉ `172.17.0.3` tại port `5050` là được.  
Trong trường hợp ta muốn gán 1 địa chỉ tĩnh cho container, khi start container, ta thêm option `--ip <ip address>` thì container này sẽ được gán địa chỉ IP mà ta mong muốn. Trong bài lab này, vì chỉ có container `jenkins` và `samplerunning` nên ta không cần làm vậy.

### Bước 4: Yêu cầu Jenkins chạy lại job BuildAppJob
![](https://github.com/datthinh1801/NT521.M11.ANTN-19520982/blob/main/Lab%201/build%20BuildAppJob.png)

### Bước 5: Kiểm tra 2 job
![](https://github.com/datthinh1801/NT521.M11.ANTN-19520982/blob/main/Lab%201/build%20results.png)  

![](https://github.com/datthinh1801/NT521.M11.ANTN-19520982/blob/main/Lab%201/test%20result.png)
> Cả 2 job đều thành công.
