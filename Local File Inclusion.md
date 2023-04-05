![image](https://user-images.githubusercontent.com/125866921/229974266-70f136c8-6a66-4e9a-81b4-75a039218a0d.png)

  - Đầu tiên khi bắt đầu chall, ta thấy ở phần URL đã xuất hiện thêm 2 tham số để chỉ định đường dẫn mà ta vừa truy cập vào. Đây là dấu hiệu cực kì rõ ràng của lỗi RFI/LFI.  

  - Nhìn vào góc phải ta thấy có 2 kiểu kết nối là ``guest`` và ``admin``. Do đó có thể có 1 file là admin, đề bài yêu cầu chúng ta tìm được password trong phần admin.  

  - Như vậy việc ta cần làm là truy cập vào file admin kia để xem bên trong đó có gì.  

Hãy cùng thử với ../ và truy cập vào file admin xem có gì xảy ra.  
    
    http://challenge01.root-me.org/web-serveur/ch16/?files=../admin  
    
Tại đây sẽ xuất hiện file ``index.php``, thử truy cập vào xem có gì không.  
    
![image](https://user-images.githubusercontent.com/125866921/229975088-981a13c4-47b0-4742-a00f-3931fefbb85b.png)


    password: OpbNJ60xYpvAQU8
