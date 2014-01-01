---
layout: post
title: «¡£eß spæk Germän!»
description: Gõ tiếng Đức (và một số ký tự đặc biệt) bằng bàn phím tiếng Anh trong GNU/Linux.
---

Gần đây mình đang học tiếng Đức. Ở mức cơ bản thì có thể nói rằng đây là
một ngôn ngữ rất thú vị. Vừa lạ vừa quen. Dĩ nhiên trong phương pháp học
hiện đại thì không thể thiếu cái máy tính. Và đã dùng đến máy tính thì
phải có cách nào đó để gõ được các ký tự đặc biệt trong tiếng Đức. Ngoài
26 ký tự thông thường thì tiếng Đức còn có 3 âm dẹt *ä (ae), ö*
*(oe), ü (ue)* gọi là Umlaut và một ký tự nối âm *ß (ss)*. Ở Đức thì
người ta có kiểu bàn phím riêng của người Đức khá giống bàn phím thông
thường của Mỹ nhưng bên trái phím Z còn một phím nữa (phím Shift bị phân
đôi) và một số ký tự bị thay đổi trật tự.

<figure>
<img src="/assets/img/posts/2013-03-10-german_qwertz.png">
<figcaption>
Bàn phím QWERTZ của Đức.
</figcaption>
</figure>

Mình đã định dùng kiểu mapping
này cho bàn phím của Mỹ nhưng cảm thấy không ổn vì mình không gõ bàn
phím QWERTY thông thường mà sử dụng một kiểu layout khác là DVORAK
đỡ mỏi, nhức tay hơn và có thể cho tốc độ gõ nhanh hơn. Với kiểu DVORAK dành cho tiếng
Đức thì rất không may, phím Ä lại nằm đúng vị trí phím thêm vào bên cạnh
phím Z ban đầu nên hoàn toàn không thể gõ được bằng bàn phím kiểu US.

<figure>
<img src="/assets/img/posts/2013-03-10-german_dvorak.jpeg">
<figcaption>
Bàn phím DVORAK của Đức.
</figcaption>
</figure>

Google một hồi thì mình cũng đã tìm ra được một giải pháp là [Compose Key](http://en.wikipedia.org/wiki/Compose_key).
Compose Key cũng giống như một bộ gõ tiếng Việt nhưng đơn giản hơn
rất nhiều. VD, để gõ chữ *ä*, bạn sẽ gõ phím Compose, gõ phím *A*, sau
đó gõ phím *"*. Cool, eh? :3

Trong Windows cũng có một thứ tương tự gọi là [Alt Codes](http://en.wikipedia.org/wiki/Windows_Alt_keycodes)
(có lẽ nhiều bạn trỏe cũng đã sử dụng bấy lâu nay để gõ teen code `;;)`). Và trong Mac OS X
thì cũng có [Option Key](http://en.wikipedia.org/wiki/Option_key). Cá nhân mình thích kiểu gõ logical của
Compose Key hơn, và về mặt khách quan thì đó cũng là lựa chọn duy nhất của mình
trong môi trường Linux.

Compose Key không chỉ gõ được tiếng Đức mà một loạt ngôn ngữ đơn giản khác
mà không phải dùng quá nhiều ký tự đặc biệt như tiếng Pháp, tiếng Tây Ban Nha
... Thậm chí là gõ emo (☺), gõ ký tự toán học đơn giản(÷, ½),... Wikipedia đã tổng hợp
một bảng ngắn tổ hợp phím cho [những ký tự hay dùng](http://en.wikipedia.org/wiki/Compose_key#Common_compose_combinations).
Bạn có thể tham khảo bảng tra cứu đầy đủ hơn ở [đây](http://www.hermit.org/Linux/ComposeKeys.html).
Còn nếu muốn tìm được danh sách đầy đủ các chuỗi Compose Key có thể trên hệ thống của bạn thì
nên xem [câu trả lời này](http://askubuntu.com/questions/34932/where-can-i-find-the-full-list-of-compose-combinations-for-my-locale) trên trang *stackoverflow*.

Giờ thì ***Auf Wiedersehen!***
