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

