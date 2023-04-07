![image](https://user-images.githubusercontent.com/125866921/230387401-163fcf49-e570-4ba1-a374-485204f6f0b3.png)

  - Bước vào chall ta thấy đây là 1 dạng upload file.  
  
  - Và nó chỉ nhận file có phần subtype là ``.jpg``.  

Thử upload 1 file ảnh bình thường xem thế nào, và nó hiện ra chính cái ảnh đấy.  

![image](https://user-images.githubusercontent.com/125866921/230570331-7a5b39eb-9858-4754-bd27-fbd8e4ac64d2.png)

  - Nhìn vào phần url thì ta dễ dàng thấy được dấu hiệu của lỗ hổng ``LFI``.  

  - Cùng thử với các các cơ bản như ``../`` và tất nhiên là nó không hoạt động.  

Sau 1 hồi tham khảo từ nguồn sau:  

    https://book.hacktricks.xyz/pentesting-web/file-inclusion
    
    https://www.cdxy.me/?p=752
    
  - Tham khảo 2 nguồn này giúp ta loại bỏ được 1 vài solution.  

  - Cùng với định dạng file mà web cho ta upload ở đây là ``.jpg``.  

  - Do đó cách giải quyết chall lần này là sử dụng ```PHP FLI wrapper``` với ``zip``.  

![image](https://user-images.githubusercontent.com/125866921/230578613-42292d1a-1cc5-48ba-b89a-1bde245b8184.png)

  - Đầu tiên tạo 1 file ``a.php`` kèm nội dung như trên ảnh.  

  - Sau đó ``zip file`` đó lại và đổi tên thành ``a.jpg``.  

  - Cần lưu ý là file sau khi được upload sẽ được lưu trữ tại /tmp/upload.  

Sau khi upload file sẽ được đổi tên trên server. Truy cập với nội dung như sau.  

![image](https://user-images.githubusercontent.com/125866921/230581524-a323fb00-04f9-4989-bc28-951323ecf209.png)

  - Trong đó ``D6ji6OxzI`` là tên của file sau khi được upload.  

  - Tiếp tục tìm flag trong các file ``index.php``, ``view.php``, ``upload.php``. Nhưng không thấy flag đâu.

  - Thử sử dụng các hàm như ``system()``, ``shell_exec()`` nhưng đều không được do chúng đã bị chặn.  

Do đó phải tìm những hướng giải quyết khác, sau 1 hồi lan man trên gu gồ thì tìm được hàm ``scandir()``.  

![image](https://user-images.githubusercontent.com/125866921/230582777-e09d1ba6-f9a4-4a3a-996c-8caaf25a378d.png)

  - Tiếp tục viết 1 file ``b.php`` với nội dung như trên.  

  - Sau đó ``zip file`` lại và đổi phần subtype thành ``.jpg``.  

  - Upload file lên và làm theo các bước tương tự như lúc upload file ``a.jpg``.  

![image](https://user-images.githubusercontent.com/125866921/230583390-ef97362a-162d-459c-8a00-17e7126e2e1c.png)

  - Ta đã tìm được file chưa flag.  

  - Giờ chỉ việc mở nó ra là có được flag thôi.  

    password: lf1-Wr4pp3r_Ph4R_pwn3d
