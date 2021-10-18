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
