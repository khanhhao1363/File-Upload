# Lỗ Hổng File Upload

> Tên Tài Liệu: VulnLab

> Người Thực Hiện: Nguyễn Khánh Hào

> Cập Nhật Lần Cuối: 10/9/2024

# Mục Lục

[Summary](#summary)

[1. Unrestricted](#1.-unrestricted)

[2. MIME Type](#2.-mime-type)

[3. Magic Header](#3.-magic-header)

[4. Backlist 1](#4.-backlist-1)

[5. Blacklist 2](#5.-backlist-2)

[Khuyến Nghị Khắc Phục ](#khuyen-nghi-khac-phuc)

# Summary

Upload File là một chức năng phổ biến trên các website, nó cho phép up các file như image (png, jpeg), document, video, word, pdf,... Nếu lập trình viên triển khai chức năng này mà không kiểm soát chặt chẽ đuôi file thì attacker có thể lợi dụng điều đó mà up các file độc hại như PHP để kiểm soát hệ thống.

# 1. Unrestricted
![image](https://github.com/user-attachments/assets/43beab0a-dc93-4f5b-9878-7849788bb16c)

Sau khi access vào lab thì có thể thấy được trang web cho chúng ta một chức năng upload file

![image](https://github.com/user-attachments/assets/3b88e8c7-ddba-4449-b1ef-405a0fe529d9)

Để hiểu rõ chức năng này thì chúng ta hãy review qua source code.

![image](https://github.com/user-attachments/assets/34a7af84-0a4c-47d5-8b57-09f2529a662c)

Có thể thấy `$fileName = $_FILES['input_./src/image']['name'];` không kiểm tra đuôi file và chúng ta có thể upload bất kì file nào để thực thi (ví dụ như `.php`).
Với nội dung của file `.php` là

![image](https://github.com/user-attachments/assets/10d9a8ca-10d1-4c52-91f6-948f36d33f5f)

![image](https://github.com/user-attachments/assets/fa0e6af6-3cc9-4b8b-829a-0cdf8c4b3a37)

# 2. MIME Type
![image](https://github.com/user-attachments/assets/1e36eb16-2923-4b2c-97bf-5bb1b7ff3f24)

![image](https://github.com/user-attachments/assets/3d1401e6-ec69-4792-86e8-b66889205b31)

Đối với lab này thì developer đã sử dụng MIME Type `$fileType = $_FILES['input_image']['type'];` để check ngăn chặn upload các file nguy hiểm.
Để có thể bypass thì chúng ta có thể sử dụng burp suite để chỉnh sửa lại MIME Type ở phần header của HTTP request.

![image](https://github.com/user-attachments/assets/5feeffa1-8313-422b-94f8-22001331f14c)

![image](https://github.com/user-attachments/assets/f70f015d-d601-4f2d-bc40-2ba0a32733d5)

# 3. Magic Header
![image](https://github.com/user-attachments/assets/c1c99b54-9f03-4287-9fdc-9c1fcefbf00d)

Ở lab này sẽ sử dụng một hàm trong PHP chính là `mime_content_type`

![image](https://github.com/user-attachments/assets/d94dedad-fc59-4496-8723-5ddb08e08192)

Dùng để xác định loại MIME của một file dựa trên nội dung hoặc tên file.
Sử dụng burp suite để tiến hành chỉnh sửa lại bên trong ruột của một file gif (file header signature sẽ là GIF89a;) 

![image](https://github.com/user-attachments/assets/393c117c-507b-4a8c-88cb-f3ddc98b6cf3)


![image](https://github.com/user-attachments/assets/5b644302-fd60-4233-9af1-48fd65401fc4)

# 4. Backlist 1

![image](https://github.com/user-attachments/assets/faeafabe-8d09-482b-ba45-7fe4403964fa)

![image](https://github.com/user-attachments/assets/48c90609-9186-43f3-8872-9d26043864ce)

Ở lab này developer sẽ sử dụng `pathinfo($fileName)['extension']` để kiểm tra đuôi file có trong blacklist không (php).

`trim($fileName) != ".htaccess"` ngăn chặn không cho upload file `.htaccess`.

Vậy câu hỏi đặt ra là. LIỆU CÒN CÓ NHỮNG FILE NÀO MÀ CHÚNG TA CÓ THỂ THỰC THI ĐƯỢC CODE PHP HAY KHÔNG ?

Ngoài `.php` thì mod php có thể xử lý các loại file như `phtml`, `php3`, `php4`, `php5`, `phar`.

![image](https://github.com/user-attachments/assets/aa674bd2-fae4-4020-93c7-f7e5e73e3762)

![image](https://github.com/user-attachments/assets/53723365-50e9-47d4-b557-b3718db4fc39)

# 5. Blacklist 2

![image](https://github.com/user-attachments/assets/61944dd5-4d15-432e-9618-7f30e23e09ce)

Ở lab này thì hầu như đã chặn hết các đuôi file mà mod php có thể thực thi.

![image](https://github.com/user-attachments/assets/0b52ef7b-f99b-422f-af47-a40856399f18)

Nhưng nếu để ý thì ở source code này không chặn upload file `.htaccess`. Vậy thì chúng ta có thể viết lại rule để server chấp nhận bất kì file nào.

![image](https://github.com/user-attachments/assets/4dcfb43e-6696-4e15-bb76-0b2e21524eb2)

![image](https://github.com/user-attachments/assets/cb069de0-7b12-4cbc-b5a8-b2292db9a42a)

# Khuyến Nghị Khắc Phục 

Backlist hết các đuôi file mà mod php có thể sử lý, đồng thời chặn luôn file `.htaccess`.

Giới hạn quyền truy cập vào thư mục upload













