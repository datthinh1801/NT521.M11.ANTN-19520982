# Integrating Security and Automation
<p align="center">
  <img src="https://user-images.githubusercontent.com/44528004/137605878-b29d7567-bac2-43e5-8bc9-9c7662f153a7.png">
</p>
> Sinh viên thực hiện:  
> Nguyễn Đạt Thịnh
> 19520982
> ANTN2019  

## Câu 1
> Đã làm tại lớp  

## Câu 2
### Các thông tin cơ bản
VCG (VisualCodeGrepper) là một công cụ phân tích code tĩnh nhằm tìm ra lỗ hổng bảo mật. VCG hỗ trợ các ngôn ngữ như C/C++, Java, C#, VB, PL/SQL và COBOL.  

Cách chế đệ scan của VCG bao gồm:
- Comments Only: Chỉ quét comments để tìm các keywords cho thấy đoạn code chưa hoàn thiện như *ToDo*, *FixMe*.  
- Code Only: Chỉ quét code (bao gồm cả các hàm không an toàn - *dangerous functions*) để tìm các lỗ hổng bảo mật.  
- Dangerous Functions Only: Chỉ quét để tìm các hàm không an toàn.  
- Code, Dangerous Functions & Comments: Bao gồm cả 3 tính năng trên.  

### Demo sử dụng
Cửa sổ khởi động của VCG như sau:  
![image](https://user-images.githubusercontent.com/44528004/137694281-1f84b2c7-0d56-4799-96ab-0a6ed5c40e95.png)  

Để quét mã nguồn, chúng ta cần tab *Settings* và chọn ngôn ngữ mà ta đang muốn quét. Ở đây, mình sẽ demo trên một java project (https://github.com/MaartenSmeets/spring-boot-demo) nên mình sẽ chọn ngôn ngữ là Java.  

![image](https://user-images.githubusercontent.com/44528004/137694633-886a9296-f395-4fd1-b99e-2fe9a67daca6.png)  

Tiếp theo, ta sẽ chọn file cần quét. Ở đây ta có thể thêm từng file hoặc thêm cả một thư mục:  
![image](https://user-images.githubusercontent.com/44528004/137694710-600aac8e-779e-4467-ba10-0811f51d8a95.png)  

Ở đây, mình sẽ chọn thêm cả thư mục.  
![image](https://user-images.githubusercontent.com/44528004/137694948-caaa74da-edac-4ef5-8485-4fcb9cd42ce5.png)

Tiếp theo, ta sẽ tiến hành chọn scan option:  
![image](https://user-images.githubusercontent.com/44528004/137695052-9f5ce872-e1b6-4f1a-ab89-6c9fa60e6df7.png)  

Ở đây mình sẽ chọn *Full Scan*.

Sau khi chạy xong, ta có kết quả sau:  
![image](https://user-images.githubusercontent.com/44528004/137695170-495d020b-60d9-4d4d-91fe-f19866128555.png)  

Ta có thể xem code breakdown tại phần *Visual Code/Comment Breakdown* tại tab *Scan* và xem kết quả quét tại tab *Results* của giao diện chính.  

Code breakdown:  
![image](https://user-images.githubusercontent.com/44528004/137695394-58a9f760-99a5-49fa-9068-316dd4c44ac2.png)

Ở cửa số chính, ta có thể thấy chương trình này có một vấn đề tìm ẩn ở dòng 7, file `spring-boot-demo-master\spring-boot-demo-master\src\main\java\nl\amis\smeetsm\demoservice\DemoserviceApplication.java` với tên lỗi là **Public Class Not Declared as Final**.


## Câu 3
Giới thiệu về công cụ quét mã nguồn Fortify SCA.  
> Vì ta cần mua license thì mới có thể tải và sử dụng công cụ nên phần này em chỉ giới thiệu về công cụ.  
> Khi tìm pricing của Fortify SCA trên google, ta có kết quả sau![image](https://user-images.githubusercontent.com/44528004/137696678-83ddab1d-9864-418f-a00c-8f0eb52a7519.png).  
> Khi tìm các products được free trials trên [MicroFocus](https://www.microfocus.com/en-us/products?trial=true), ta có kết quả sau:  
> ![image](https://user-images.githubusercontent.com/44528004/137696928-275dc50a-cae8-4a8d-b4e2-4955aa582a8c.png)  

*Fortify SCA là gì?*  
Fortify SCA là công cụ quét mã nguồn, giúp phát hiện các lỗ hổng bảo mật trực tiếp trong quá trình phát triển dự án. Việc này góp phần tăng khả năng phát hiện lỗ hổng sớm và làm giảm chi phí và nhân công phát sinh khi vá lỗ hổng.  

*Fortify SCA hoạt động như thế nào?*  
Fortify SCA phát hiện lỗ hổng bằng cách đọc mã nguồn, biên dịch mã nguồn để tạo thành một cấu trúc trung gian (intermediate structure) giúp cho việc phân tích bảo mật dễ dàng hơn. Với cấu trúc trung gian này, các engine phân tích có trong Fortify SCA sẽ sử dụng các *secure coding rules* để kiểm tra xem mã nguồn có vi phạm *secure coding practices* nào hay không. Fortify SCA cũng cung cấp *rules builder* giúp ta tự cấu hình rules cho project của mình.  

*Hệ sinh thái của Fortify SCA*  
- Integrated Development Environment (IDE): Eclips, Visual Studio, IntelliJ.  
- CI/CD Tools: Jenkins, Bamboo, Visual Studio, Gradle, Make, Azure DevOps, GitHub, GitLab, Maven, MSBuild.  
- Issue Trackers: Bugzilla, Jira, ALM Octane  .
- Open Source Security Managment: Sonatype, Snyk, WhiteSource, BlackDuck.  
- Code Repositories: GitHub, Bitbucket.  

*Các lợi ích khi sử dụng Fortify SCA*  
- Quét nhanh và chính xác.  
  - Fortify SCA có khả năng phát hiện lỗ hổng trong source, binary và byte code.  
  - Hỗ trợ phát hiện 815 loại lỗ hổng khác nhau trên 27 ngôn ngữ lập trình và trên hơn 1 triệu APIs.  
  - True positive rate đạt 100% (đánh giá bởi OWASP).  
- Tự động hóa trong chu trình CI/CD.  
- Giảm thiểu thời gian và chi phí để phát triển phần mềm an toàn do các lỗ hổng đều được phát hiện sớm trong quá trình phát triển.


## Câu 4
> Đã làm tại lớp  

## Câu 5
> Đã làm tại lớp  

## Câu 6
> Đã làm tại lớp  

## Câu 7
Khi thực hiện serialization, các object sẽ được convert sang một định dạng nào đó mà khi deserialize, trạng thái của chúng không thay đổi.  
Trong PHP, object khi được serialized sẽ được lưu dưới dạng string. Ví dụ:
```
O:4:"User":2:{s:4:"name";s:5:"thinh";s:10:"isLoggedIn";b:1;}
```
> với `0:4` ở phần đầu biểu hiện serialized data này là một object `0` với tên có `4` ký tự).  

Trong Java, object khi được serialized sẽ được lưu dưới dạng byte và cũng lưu theo một định dạng nhất định. Cụ thể, các class kế thừa từ `java.io.Serializable` đều có thể được serialize và định dạng sau khi serialize sẽ bắt đầu bằng `ac ed 00 05` ở dạng hexa.  
```
└─$ cat normalObj.serial | hexdump
0000000 edac 0500 7273 0700 7556 6e6c 624f 1c6a
0000010 3ae9 a207 a218 024e 0100 004c 6303 646d
0000020 0074 4c12 616a 6176 6c2f 6e61 2f67 7453
0000030 6972 676e 783b 7470 0300 7770 0064     
000003d
```
> Ở little endian, các byte này sẽ được biểu diễn là `edac 0500`.  

Nếu ta decode các byte này theo đúng thứ tự `ac ed 00 05`, ta sẽ có chuỗi bit sau:
```
10101100111011010000000000000101
```

Chuỗi bit này sẽ tương ứng với các ký tự sau khi biểu diễn ở base64:
```
101011 - r
001110 - O
110100 - 0
000000 - A
000001 - B
01
```

Vậy bất kỳ khi nào ta thấy một chuỗi base64 bắt đầu với `rO0AB` ta có thể nghĩ ngay đến việc chuỗi này là một chuỗi serialize của một object nào đó trong Java. Tự đó ta có thể khai thác từ điểm này nếu chương trình không kiểm soát chặt chẽ việc serialize và deserialize.

## Câu 8
### Description
This lab uses a serialization-based session mechanism and is vulnerable to privilege escalation as a result. To solve the lab, edit the serialized object in the session cookie to exploit this vulnerability and gain administrative privileges. Then, delete Carlos's account.

You can log in to your own account using the following credentials: `wiener`:`peter`  

### Solution
Từ lab description, ta biết được là ứng dụng web này sử dụng cơ chế serialization để tạo session cookie. Để xác minh, ta có thể dùng Burp Suite để inspect các tương tác giữa client và server:  

![image](https://user-images.githubusercontent.com/44528004/137713138-7779bea7-1b98-4d42-9caa-464e2c5360d8.png)  

Xem POST request:  
![image](https://user-images.githubusercontent.com/44528004/137713421-efa79234-fbbd-406e-9ea9-87a87411e98d.png)  

Ở đây, ta thấy được là server set cookie `session` cho client với giá trị là `Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo1OiJhZG1pbiI7YjowO30%3d`. Khi decode URL và base64, ta sẽ có kết quả sau:  
![image](https://user-images.githubusercontent.com/44528004/137713625-b3c5d7d3-b3ee-41cb-ad79-2116e37a2e55.png)  

Từ kết quả decode, ta thấy được rằng, `session` cookie này được tạo từ một `User` object với `username` là `wiener` và `admin=false`.  

Vậy để leo thang đặc quyền (privilege escalation), ta có thể set `admin=true` bằng cách thay `b:0` thành `b:1` và thực hiện decode base64 để tái tạo `session` cookie.  

![image](https://user-images.githubusercontent.com/44528004/137713842-d00c9527-842f-42d4-9e4d-aa114566f2a7.png)

Thử GET `/` và thay đổi `session` cookie:  
![image](https://user-images.githubusercontent.com/44528004/137714086-2215018a-9d7e-4877-acbd-9dc755c03ede.png)

Lúc này, home page của chúng ta có một tab `Admin panel`. Thử truy cập vào `Admin panel` với `session` cookie mới ta được:  

![image](https://user-images.githubusercontent.com/44528004/137714347-5f480cff-75f7-43d4-aa7e-d443da3d9405.png)

Cuối cùng, xóa `carlos` user.  

![image](https://user-images.githubusercontent.com/44528004/137714452-5ecbabf0-93df-41f1-ac52-d0a02cf0eacc.png)
> Solved!

## Câu 9

## Câu 10
> Đã làm tại lớp  

## Câu 11

