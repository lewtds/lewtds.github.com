---
layout: post
title: Ước mơ bộ gõ kiểu Unikey trên Linux
tags: programming
description: "Gõ tiếng Việt là một trong những nhu cầu rất cơ bản của người dùng Việt. Tuy nhiên, hiện tại vẫn chưa có một bộ gõ tiếng Việt nào đạt được sự ổn định và thoải mái như bộ gõ Unikey trên Windows."
---

Đặc biệt có một vấn đề mà rất nhiều người dùng than phiền là khi gõ tiếng Việt
xuất hiện một dấu gạch bên dưới dòng chữ đang gõ. Dấu gạch này được gọi là
preedit line. Để hiểu về vấn đề này thì cần phải biết sơ qua cách hoạt động
của một bộ gõ.

## Backspace và Preedit

Một bộ gõ (không nhất thiết là tiếng Việt) thường là một phần mềm chuyển đổi
một chuỗi phím nhấn thành một đoạn văn bản ở ngôn ngữ mong muốn. Để làm điều
này thì bộ gõ cần phải liên tục thay đổi và hiển thị mẩu văn bản đang gõ dựa
theo những phím mới nhấn. VD: khi người dùng gõ <kbd>a</kbd> thì sẽ có một chữ
**a** hiện ra màn hình, nhưng nếu gõ thêm một chữ <kbd>a</kbd> nữa thì phải
thay thế chữ a đầu tiên bằng chữ **â** theo kiểu gõ Telex.

Gần như tất cả các bộ gõ trên Windows (tôi không dám chắc nhưng không thể kiểm
chứng được vì phần lớn là phần mềm nguồn đóng) giải quyết vấn đề này bằng cách
sử dụng dấu backspace giả. Như vậy, khi ấn phím <kbd>a</kbd> thứ hai thì bộ gõ
sẽ gửi một dấu backspace giả để xóa chữ **a** thứ nhất rồi mới gửi chữ **â**
đến ứng dụng.

Pseudo code cho Unikey:

```python
while True:
    next_key = get_next_key()
    if next_key == 'a' and last_key == 'a':
        send_back_space()
        send_text('â')
    else:
        send_text(next_key)

    last_key = next_key
```


Tuy nhiên, preedit mới là giải pháp "chính thống" cho vấn đề edit liên tục
này. Preedit là một vùng buffer tạm bên trong ứng dụng, cho phép bộ gõ sửa đổi
thoải mái. Chỉ khi nào bộ gõ *commit* đoạn preedit này thì text đang gõ mới
trở thành một phần chính thức của văn bản còn không thì nó được coi là độc lập
và có thể bị discard bất cứ lúc nào. Vì có sự phân biệt này nên trong tất cả
các hướng dẫn thiết kế giao diện của Windows, Mac OS X hay Linux đều khuyến cáo
preedit phải được đánh dấu một cách đặc biệt, thông thường là với một dấu gạch
chân màu đen 1 pixel. Preedit được dùng rất thông dụng với các bộ gõ tiếng
Nhật, Trung, Hàn (CJK).

![Preedit với bộ gõ tiếng Nhật trên Windows](/assets/img/posts/japanese-preedit.png)

Code thay đổi khi dùng preedit:

```python
    ...
    if next_key == 'a' and last_key == 'a':
        update_preedit_text('â')
    ...
```

Người dùng Việt do quen với kiểu gõ trực tiếp như Unikey trên Windows cảm thấy
khó thích nghi với preedit, đặc biệt là với dấu gạch chân, nhưng cũng vì nhiều
lý do khác.

Thao tác hiển thị preedit bắt buộc phải được implement phía ứng dụng chứ không
phải ở phía hệ thống hay phía bộ gõ vì lý do rất đơn giản là nếu muốn hiển thị
preedit ở đúng vị trí con trỏ và với formatting của text đang gõ từ trước thì
cần phải có thông tin chính xác về text buffer, mà thông tin này thì chỉ có
ứng dụng có. Chính vì được implement ở phía ứng dụng nên điều tất yếu xảy ra
là có nhiều implementation khác nhau với những behavior khác nhau. Ví dụ như
khi gõ mật khẩu trong `gksu` thì không hiện preedit nhưng khi gõ với lệnh
`sudo` trong terminal thì sẽ hiện (rất nguy hiểm). Hay trong phần lớn các ứng
dụng thì preedit không tạo hiệu ứng autocomplete như phím nhấn bình thường,
người dùng phải gõ hết từ muốn tìm kiếm và commit thì chương trình mới bắt đầu
autocomplete. Preedit cũng không hoạt động với các ứng dụng Windows chạy qua
Wine vì Wine chỉ là một lớp tương thích và không thể biết chính xác con trỏ
trong ứng dụng đang ở ví trí nào. Một vấn đề nữa là nếu bộ gõ chỉ sử dụng
preedit thì một khi đã commit thì không thể quay trở lại sửa những từ đã gõ ở
trước. VD như với [ibus-unikey](https://code.google.com/p/ibus-unikey/) thì
nếu đã gõ **cón có** thì không thể nhấn backspace quay lại sửa dấu cho **ó**
được (ibus- unikey gặp dấu cách sẽ commit luôn).

Vì vậy, nhiều bộ gõ trên Linux đã cố gắng bắt chước kiểu gõ direct của Unikey
trên Windows với nhiều kỹ thuật và mức hiệu quả khác nhau.

### Fake X11 event

Trên môi trường Unix thì X11 là phần mềm quản lý giao diện đồ họa, mọi thao
tác nhập xuất bàn phím, con trỏ, hiển thị ra màn hình đều phải qua X11. Vì vậy
có thể thấy cách đơn giản nhất là tạo phím backspace giả từ X11.
[xvnkb](http://xvnkb.sourceforge.net/hacking/xvnkb1-hacking.html) phiên bản
0.1 sử dụng phương pháp này. Về ý tưởng thì gần như giống hệt Unikey, trên Win
Unikey sử dụng hàm `PostMessageW()` với message type là `WM_IME_CHAR` hoặc
`WM_CHAR` để tạo phím nhấn giả.

Tuy nhiên, X11 là một hệ thống bất đồng bộ nên không có gì đảm bảo sự kiện
backspace sẽ đến trước sự kiện commit. Vì vậy có thể dẫn tới trường hợp chữ â
tới trước dấu backspace và về phía người dùng thì chữ a thứ hai không có tác
dụng gì hết.

Hơn nữa, fake X11 event được coi là một nguy cơ bảo mật nên nhiều ứng dụng
không chấp nhận xử lý những sự kiện kiểu này.

### Forward key event

Các bộ gõ trên Linux thường không đứng riêng lẻ mà hoạt động với một phần mềm
quản lý bộ gõ (thông thường có ibus, scim, uim,...). Các phần mềm quản lý bộ
gõ này sẽ cung cấp các thư viện liên kết động chạy trong context của các ứng
dụng. VD khi gõ với Gedit thì Gedit sẽ nhận một key event từ X11, sau đó
truyền qua cho thư viện liên kết động của IBus để nó truyền tiếp qua cho bộ gõ
tiếng Việt.

Pseudo code của Gedit:

```python
while True:
    event = get_next_event()
    if event is keyboard_event:
        result = ibus.process_key_event(event)
```

Vòng lặp `while True` này được gọi là event loop. Ở đây, thư viện IBus có thể
tạo backspace giả, đặt vào cho loop xử lý. Trái với fake event ở phương pháp
trên, fake event này nằm ngay trong context của ứng dụng.

Thế nhưng, vì được implement phía ứng dụng nên phương pháp này vẫn gặp phải
vấn đề là có ứng dụng hỗ trợ, ứng dụng không (VD: Google Chrome từ bản 35.xxx
trở lên là không hỗ trợ [#216 @ ibus-bogo](https://github.com/BoGoEngine/ibus-bogo/issues/216)).

[ibus-bogo](https://github.com/BoGoEngine/ibus-bogo),
[xunikey](http://unikey.org/linux.php) và 
[scim-unikey](https://code.google.com/p/scim-unikey/) dùng phương
pháp này.

### Hijack X11 function

Tác giả Đào Hải Lâm của dự án xvnkb có nghĩ ra một 
[phương pháp chặn](http://xvnkb.sourceforge.net/?menu=hacking2) rất sáng
tạo. Gần như tất cả các ứng dụng đồ họa trên Linux đều phải giao tiếp với X11
thông qua thư viện libX11. Để nhận event thì ứng dụng sẽ chạy một vòng lặp vô
hạn và gọi hàm `XNextEvent()` (như mô tả trong pseudo code của Gedit). Ta có
thể viết lại hàm này trong một thư viện liên kết động và preload thư viện
riêng trước khi ứng dụng load libX11 để ép ứng dụng phải gọi hàm của ta thay
vì hàm của libX11. Sau đó, từ trong context của ứng dụng có thể tạo backspace
event như mong muốn.

Cách làm này yêu cầu người dùng phải làm thêm thao tác preload thư viện
trước khi khởi động ứng dụng. Và với Gtk+ 3 trở lên thì không hoạt động nữa,
không rõ lý do.

Gần đây còn xuất hiện thêm thư viện libxcb để thay thế libX11. Những ứng dụng
sử dụng libxcb không hoạt động với phương pháp này.

### Surrounding text

Surrounding text là tính năng của một số ứng dụng cho phép bộ gõ đọc được văn
bản có sẵn xung quanh con trỏ (surrounding) và xóa một phần của văn bản đó. Có
thể dùng nó thay thế cho backspace giả. Bộ gõ scim-unikey ở chế độ Legacy và
ibus-bogo có sử dụng tính năng này, nếu có.

Đặc biệt, ibus-bogo còn tận dụng tính năng đọc văn bản có sẵn để phát triển
tính năng sửa dấu của từ bất kỳ trong văn bản (Unikey trên Win chỉ có thể sửa
dấu của một số từ vừa gõ xong, nếu xuống dòng hay nhấn chuột, di chuyển con
trỏ là không thể sửa được nữa).

Tuy nhiên, surrounding text phải được ứng dụng hỗ trợ và kể cả khi được hỗ trợ
thì vẫn có ứng dụng hoạt động không tốt (VD như Firefox hỗ trợ surrounding
text với những ô input thông thường, còn với những ô input kiểu custom, phức
tạp như Google Docs thì không).

Nhóm tác giả ibus-bogo đang làm việc với các phần mềm tự do nguồn mở lớn để
hoàn thiện hỗ trợ surrounding text. Xem 
[issue #4 @ ibus-ringo](https://github.com/lewtds/ibus-ringo/issues/4)

Trên Windows cũng có surrounding text nhưng lần cuối tác giả thử nghiệm thì
gần như chỉ có bộ MS Office là hỗ trợ tốt.

### Preedit ẩn

Như đã thấy, các phương pháp xóa trực tiếp không phải lúc nào cũng ổn định,
trong khi đó thì preedit thì gõ ở đâu cũng được. Vậy ta có thể vẫn sử dụng
preedit nhưng không cho hiện dấu gạch chân. Phương pháp này được sử dụng bởi
bộ gõ ibus-m17n. Nó ẩn được dấu gạch chân những vẫn gặp phải những vấn đề nói
trên của preedit. Hơn nữa, trong một số ứng dụng thì không thể tắt được gạch
chân, VD như Google Chrome.

Hiện nay, tác giả (cũng là contributor chính của 
ibus-bogo) đang thử nghiệm
phương pháp này nhưng không preedit một từ như ibus-unikey và preedit cả một
cụm từ liền nhau để có thể sửa dấu của những từ đứng trước.

## Tương lai với Wayland

Với Wayland thì nhóm phát triển đã định nghĩa input method là một phần quan
trọng ngay từ đầu của protocol. 
[Forward key event](http://cgit.freedesktop.org/wayland/weston/tree/protocol/input-method.xml?id=70ee3ad47c12dc3b4173373f98e1dc1c7486c5d7#n128)
và [surrounding text](http://cgit.freedesktop.org/wayland/weston/tree/protocol/input-method.xml?id=70ee3ad47c12dc3b4173373f98e1dc1c7486c5d7#n100) cũng
được định nghĩa. Vì vậy có thể hi vọng rằng một khi đã có một chuẩn chung thì
mọi ứng dụng sẽ hỗ trợ hai tính năng này một cách ổn định và một bộ gõ kiểu
Unikey có thể trở thành hiện thực.
