---
layout: post
title: Finite State Transducer và gõ tiếng Việt
tags: programming
description: "Ý tưởng về sử dụng Finite State Transducer để implement một thư viện xử lý gõ tiếng Việt."
---

Finite State Machine là một dạng Finite State Machine. Nó có thể dùng để biến
một chuỗi input thành một chuỗi output theo những luật cho trước.

TODO: vẽ hình ví dụ FST, bảo là tốn công.

[SFST][1] là một phần mềm và cũng là một ngôn ngữ lập trình để có thể dễ dàng
tạo ra các FST mà không cần mất công tự viết từng state và transition một. Nó
được viết bởi các chuyên gia ở trường đại học Muenchen ở Đức.

Với SFST, bạn không định nghĩa các state mà chỉ viết các transition, phần mềm
sẽ tự động tạo ra các state. Ví dụ của một transition: '1:a 2:b 3:c'. Các
khoảng trắng không có ý nghĩa và dấu hai chấm được dùng để ngăn cách
transition của input và transition của output. Như vậy, ở đây input là *a*,
*b*, *c* và output là *1*, 2, 3. Bây giờ nếu bạn biên dịch FST này và đưa vào
input là "abc" thì kết quả sẽ là "123". Nếu bạn gõ "a" hay "ab" thì phần mềm
sẽ báo là không nhận dạng được input này.

SFST cũng hỗ trợ một số thao tác thông thường trong regular expression như *
(match biểu thức đứng trước 0 hoặc nhiều lần), + (match biểu thức đứng trước ít
nhất 1 lần), \[abc\] (match một trong a hoặc b hoặc c). Regular expression cũng
là một dạng finite state machine nên nó có nhiều điểm tương đồng với FST, tuy
nhiên FST dùng để chuyển đổi input thành output còn regular expression chỉ có
thể dùng để thử match và đưa ra kết quả là match hay không match.

Giờ chúng ta đã có thể bắt đầu implement một bộ gõ Telex rất đơn giản. Bộ gõ
của chúng ta sẽ thực hiện được một số thao tác như sau:

- Gõ một nguyên âm và s thì sửa nguyên âm đó và thêm dấu sắc
- Gõ một nguyên âm và f thì sửa nguyên âm đó và thêm dấu huyền
- Gõ một nguyên âm và r thì sửa nguyên âm đó và thêm dấu hỏi
- Gõ một nguyên âm và x thì sửa nguyên âm đó và thêm dấu ngã
- Gõ một nguyên âm và j thì sửa nguyên âm đó và thêm dấu nặng
- Gõ aa ra â
- Gõ oo ra ô
- Gõ ee ra ê
- Gõ aw ra ă
- Gõ uw ra ư
- Gõ ow ra ơ
- Gõ dd ra đ

Dễ thấy có hai nhóm luật: luật áp dụng cho mọi nguyên âm và luật áp dụng riêng
cho từng chữ cái đặc biệt. Chúng ta sẽ gọi nhóm luật đầu tiên là luật dấu
thanh (tone) và nhóm thứ hai là luật dấu chữ cái (mark). Để tiện xử lý sau này, chúng ta sẽ đặt tên cho tất cả các dấu:

- Dấu sắc: acute
- Dấu huyền: grave
- Dấu hỏi: hook
- Dấu ngã: tidle
- Dấu nặng: dot
- Dấu mũ: hat
- Dấu ă: breve
- Dấu móc ư: horn
- Dấu ngang đ: bar

```
á:{as}
à:{af}
.
.
.
â:{aa}
ô:{oo}
.
.
.
```

OK ngon. Nhưng ana không ra ân? Đó là vì bộ gõ đang chỉ match hai chữ a đứng
cạnh nhau. Cần phải match cả 2 chữ a đứng tách rời nữa.


```
ALPHABET = [aZ]

â:a (. & [^a])* <>:a
```

Dấu chấm có nghĩa là bất kỳ một cặp output:input đã được biết nào. Nếu sử dụng dấu chấm thì cần phải định nghĩa một tập hợp hữu hạn các cặp đã biết. Chúng ta đã định nghĩa ở trên là mọi chữ cái từ a tới Z tự map tới chính nó. Như vậy đã có thể đọc luật gõ ở trên là: nếu gặp một chữ a, theo sau nó là 0 hoặc nhiều chữ cái bất kỳ và một chữ a khác thì biến chữ a thứ nhất thành â và chữ a thứ hai thành không có gì. Tuy nhiên, trong tập hợp chữ cái bất kỳ đó không được phép chứa a (nếu không thì không bao giờ gặp chữ a thứ hai) vì vậy chúng ta sử dụng phép ^ (not) và & (intersection) để không cho match a.

Vậy là đã xử lý được ana nhưng naa? Luật này khá đơn giản, chỉ cần match chữ cái bất kỳ ở đầu string là được:

```
.* â:a (. & [^a])* <>:a
```

Chương trình đang có sự phụ thuộc vào các magic constant, ở đây là các chữ cái
trong luật Telex. Nếu muốn sửa chương trình để hỗ trợ thêm kiểu gõ VNI thì
chúng ta sẽ phải viết lại tất cả các luật. Not cool. Để giải quyết vấn đề này,
chúng ta sẽ tạo ra một bước trung gian, chuyển đổi từ phím gõ ra luật thêm
dấu.

```
a<TONE_ACCUTE>:{as}
a<MARK_HAT_A>:{aa}
```

SFST sẽ coi mọi thứ nằm trong hai dấu <> là một ký tự đặc biệt nên có thể hiểu
a<TONE_ACCUTE> là một chuỗi có 2 ký tự.


[1]: http://www.cis.uni-muenchen.de/~schmid/tools/SFST/
