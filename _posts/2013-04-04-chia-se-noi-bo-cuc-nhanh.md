---
layout: post
title: Chia sẻ file cực nhanh trong mạng nội bộ
description: "Hướng dẫn sử dụng tính năng HTTP server của Python để tạo
một web server chia sẻ file."
---

Người dùng Linux thực ra không thiếu lựa chọn để chia sẻ file. Ta có thể
dùng chia sẻ FTP, NFS mounting truyền thống của Unix, có thể tạo
SSH server và dùng SFTP truy cập an toàn, hoặc nếu muốn ít phải setup hơn
mà vẫn đồng bộ được với mạng Windows thì có Samba. Tuy nhiên, tất cả các
giải pháp trên đều không phù hợp với tình huống đột nhiên bạn có một vài
file nhỏ nhỏ muốn copy nhanh giữa các máy với nhau. Google nhanh có thể
tìm thấy một vài công cụ chuyên biệt cho việc này nhưng tôi sẽ giới thiệu
với bạn một cách vô cùng thuận tiện, không phải cài đặt thêm bất cứ thứ
gì mà chỉ cần có Python luôn luôn sẵn có trong gần như tất cả các Linux 
distro thông dụng, kể cả Mac OS X cũng cài sẵn.

Giả sử tôi muốn chia sẻ thư mục `/home/chin/public`, trước hết tôi sẽ `cd`
đến thư mục này:

    $ cd /home/chin/public
    
Sau đó chạy lệnh này để tạo HTTP server:

    $ python2 -m SimpleHTTPServer
    Serving HTTP on 0.0.0.0 port 8000 ...
    
Bam! Bạn đã có một HTTP server chia sẻ tất cả những file bạn có trong
thư mục `/home/chin/public` đang listen trên cổng 8000. Ngay bây giờ
bạn có thể kiểm tra bằng cách bật Firefox hoặc Chrome, truy cập địa chỉ
`0.0.0.0:8000` (hoặc localhost:8000, tùy bạn) sẽ thấy kết quả hiện ra
như sau:

![Truy cập từ browser](/assets/img/posts/2013-04-04-httpserver.png)

Click vào bất cứ file nào để tải về.

Nhưng sau đó thì bạn cần phải truy cập trang web này từ một máy khác
trong mạng LAN. Để làm vậy, bạn cần biết địa chỉ IP hiện tại của máy mình
trong mạng nội bộ bằng cách chạy lệnh sau:

    $ ifconfig

Kết quả của bạn có thể có dạng như thế này:

    eth0      Link encap:Ethernet  HWaddr 10:1f:74:ea:08:6b  
              UP BROADCAST MULTICAST  MTU:1500  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000 
              RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
              Interrupt:46 Base address:0x6000 

    wlan0     Link encap:Ethernet  HWaddr d0:df:9a:af:31:55  
              inet addr:192.168.29.171  Bcast:192.168.29.255  Mask:255.255.255.0
              inet6 addr: fe80::d2df:9aff:feaf:3155/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:3205830 errors:0 dropped:0 overruns:0 frame:0
              TX packets:2575431 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000 
              RX bytes:3286071184 (3.2 GB)  TX bytes:1234751640 (1.2 GB)
              
Tôi đang dùng mạng Wifi nên tôi quan tâm đến mục `wlan0` ở dưới dùng, để
ý dòng `inet addr:192.168.29.171` là địa chỉ IP hiện tại của máy tôi
trong mạng LAN (nếu bạn dùng Ethernet cắm dây thì xem mục `eth0`).

Như vậy, ở những máy còn lại, tôi chỉ cần truy cập địa chỉ
http://192.168.29.171:8000 là có thể tải được những file đang được chia
sẻ ở máy tôi.

Có một số điều cần chú ý với phương pháp này. Trước hết là nó rất không
an toàn, không có quản lý phân quyền gì hết nên chỉ dùng trong mạng nội
bộ mà bạn tin tưởng. Thứ hai là ở đây tôi có ghi rõ là phải dùng python2
vì python2 và python3 là hai môi trường khác biệt nhau rất nhiều, với
python3 thì tôi sẽ chạy lệnh sau:

    python3 -m http.server

Phần lớn các distro đều có lệnh `python` không có số đi kèm ở cuối sẽ
gọi phiên bản python tiêu chuẩn. Tuy nhiên, vì lệnh của chúng ta phụ
thuộc vào phiên bản nên cần ghi rõ là python2.

Một lưu ý nữa là bạn có thể thay đổi cổng của server bằng cách thêm một
số nguyên trong khoảng 1024 - 65535 ở cuối lệnh.

Trong bài này tôi sử dụng Python vì sẵn có và dòng lệnh cũng tương đối
ngắn nhưng nếu thích, Rubyist có thể dùng lệnh sau:

    ruby -rwebrick -e'WEBrick::HTTPServer.new(:Port => 8000, :DocumentRoot => Dir.pwd).start'

Lợi thế là giao diện web trông đẹp hơn một chút :v

![Ruby](/assets/img/posts/2013-04-04-ruby.png)
