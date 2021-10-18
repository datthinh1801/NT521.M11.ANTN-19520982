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

## Câu 8

## Câu 9

## Câu 10
> Đã làm tại lớp
