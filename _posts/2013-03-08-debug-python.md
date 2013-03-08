---
layout: post
title: Debug phần mềm Python
description: "Tập hợp những kiến thức và kinh nghiệm mình đúc rút được trong quá trình
phát triển dự án BoGo (một hôm nào đó sẽ viết về BoGo)."
---

Debugging hay gỡ lỗi là một trong những giai đoạn quan trọng nhất của việc phát triển phần mềm.
Đôi khi debugging thậm chí còn chiếm nhiều thời gian của tác giả hơn là thực sự viết tính năng
mới cho phần mềm. Vì vậy, một khi bạn đã đầu tư thời gian nghiên cứu một hệ thống nào đó thì
nhất quyết không thể bỏ qua các kỹ thuật debugging cho hệ thống đó. Trong khuôn khổ giới hạn
của bài viết này, tôi sẽ giới thiệu một vài kỹ thuật debugging thông dụng dành cho Python nhưng
chỉ dừng lại ở mức giới thiệu cơ bản. Nếu hứng thú bạn có thể nghiên cứu các link liên quan mà 
tôi đặt rải rác trong bài viết.

## print

Đây có thể coi là phương pháp thủ công nhất trong các phương pháp mà tôi biết. Tuy nhiên nó cũng
là phương pháp tự nhiên nhất, được tất cả mọi người nghĩ ra mà không cần ai hướng dẫn khi gặp sự
cố trong khi lập trình.

Chắc khỏi nói bạn cũng biết phải làm gì rồi đúng không? Bất cứ khi nào
gặp lỗi thì bạn quan sát code của mình, dự đoán xem là chỗ nào có khả năng xảy ra vấn đề cao
nhất rồi viết một lệnh `print` vào đó. Bạn có thể `print` nội dung của một biến nào đó để theo
dõi quá trình xử lý hoặc thậm chí một lệnh `print("here")` đôi khi cũng đã đủ.

Lợi thế của `print()`
là vô cùng đơn giản, dễ sử dụng, hiển thị được nhiều kiểu dữ liệu (*thực chất là kiểu nào cũng print*
*được hết theo dạng `<function main at 0xa431f2c>`*). Nhưng ngược lại, nó có nhiều hạn chế như 
bạn phải tự tay xóa các dòng `print` khỏi code sau khi debug và deploy cho khách hàng chẳng hạn.

## logging

Phát triển phương pháp print lên một bậc thì người ta đã nghĩ ra giải pháp logging, tức ghi chép
những gì đang diễn ra trong phần mềm. `logging` giống y hệt `print` về cách sử dụng nhưng nó
có một cái lợi rất lớn là bạn có các cấp độ logging khác nhau và có thể chỉ hiện một nhóm cấp
độ nào đó chứ không phải lúc nào cũng print hết ra như cách làm trước. Các cấp độ thường có của
logging là *DEBUG, INFO, WARNING, ERROR, CRITICAL* theo trật tự ưu tiên tăng dần. **Khi bật một mức logging**
**nào đó thì tất cả các mức trên cũng được hiển thị theo**. Thông thường thì *CRITICAL* là
cấp độ cao nhất và luôn được hiện (hiện xong thì die luôn), nếu bật cấp *WARNING* thì cũng bật cấp
*ERROR* và *CRITICAL*, bật cấp *DEBUG* thì bật luôn cả 3 cấp trên nó, v.v.

Để sử dụng logging trong Python thì bạn cần phải import module `logging`. Sau đó, bất cứ chỗ nào
bạn muốn đặt lệnh `print()` thì thay thế bằng lệnh `logging.debug()`. Ứng với
mỗi cấp độ logging thì module `logging` sẽ có một hàm tương ứng. Tuy nhiên, theo default thì ban
đầu chỉ có các message ở cấp độ *ERROR* mới được hiển thị nên bạn cần phải thêm một dòng setup thì
các cấp độ khác mới được hiển thị theo.

    logging.basicConfig(stream=sys.stderr, level=logging.DEBUG)

Một VD khác hoàn thiện hơn:

    import logging

    def do_stuff():
        logging.debug("In do_stuff")
        i = 5
        logging.debug("i = %d", i)
        return i
        logging.error("Code should not be reached!")

    def main():
        logging.info("Program started")

    if __name__ == "__main__":
        # Có thể comment dòng dưới để tắt debugging hoặc viết arg parser để thêm
        # option --debug khi chạy chương trình.
        logging.basicConfig(stream=sys.stderr, level=logging.DEBUG)
        main()

Bạn có thể xem tutorial chi tiết hơn về module `logging` ở [đây](http://docs.python.org/2/howto/logging.html).

## ipython

Trên đây là hai phương pháp để theo dõi quá trình xử lý của một phần mềm. Tuy nhiên, những gì
bạn có được vẫn chỉ là những dòng thông báo. Có lẽ bạn sẽ muốn tận tay thay đổi một option nào
đó **ngay khi chương trình đang chạy**. Vậy thì bạn sẽ thấy thích tính chất interpreted của
Python.

Interpreted (thông dịch) tức là chương trình Python không được biên dịch một lần thành
file chạy như các ngôn ngữ compiled như C hay Java mà được đọc từng dòng một từ trên xuống dưới.
Và bạn có thể sử dụng một giao diện dòng lệnh của ngôn ngữ để gõ từng dòng code một, gõ đến đâu
thực hiện đến đấy như terminal của Unix. Trình thông dịch chuẩn của Python chính là lệnh `python`.
Tuy nhiên, nó có nhiều giới hạn như không có syntax highlighting, không có autocomplete,... nên
có nhiều dự án được phát triển để thay thế trình thông dịch `python`. Một trong số đó rất nổi
tiếng là `ipython`.

Giả sử bạn đang viết một phần mềmkiểm tra chính tả. Phần mềm của bạn định nghĩa một hàm chính 
và rất nhiều hàm phụ trợ như `xoa_dau_cach`,`tach_thanh_phan`,... Vậy thì
làm thế nào bạn chạy thử các hàm phụ trợ đó với input ngẫu nhiên mà bạn nghĩ ra? Thông thường
thì bạn sẽ viết một chương trình test riêng, import module của bạn và chạy thử từng hàm một.
Đây là một phương pháp classic, rất mạnh và được khuyên dùng (*trong một bài viết tiếp theo
tôi sẽ giới thiệu về unit testing trong Python*). Nhưng
đôi khi bạn muốn tự tay mình chạy thử hàm mình viết ra cơ. Vậy bạn sẽ bật `ipython` lên, import
module mới viết, gọi hàm đang test và kiểm tra kết quả. Thử một vài input thấy kết quả không đúng,
bạn quay lại sửa code từng tí một. Mỗi lần sửa xong bạn chỉ cần quay lại ipython và chạy lại lệnh
lúc nãy. iPython có một tính năng rất mạnh là extension `autoreload`. Không cần phải tạo file
hay chỉnh sửa, compile gì hết vì `autoreload` của iPython đã tự động import lại khi code của bạn
thay đổi rồi.

    (ipython) > %ext_reload autoreload
    (ipython) > %autoreload 2

Sau khi cài đặt và chạy lệnh `ipython`, bạn có thể gõ `?` để đọc hướng dẫn sử dụng của iPython.

## pdb

Ok. Vậy logging cho bạn nhìn thấy quá trình xử lý, còn iPython cho bạn chạy thử một lệnh Python
bất kỳ từ dòng lệnh. Thế có cách nào kết hợp được cả hai điều này không? Yup, **pdb**.

Pdb (Python Debugger) là một module trong thư viện chuẩn của Python.
Sử dụng pdb, bạn có thể tạo ra các debugging session như gdb nên nếu bạn
đã quen với việc debug C/C++ (hay nhiều ngôn ngữ compiled khác) bằng gdb thì có thể bạn sẽ thấy
thoải mái ngay lập tức với pdb.

Có nhiều cách để khởi động pdb. Cách đơn giản nhất là launch nó như một file chạy bình thường
với argument là script bạn muốn debug.

    python -m pdb my_script.py

Flag `-m` dùng để chạy một module (đã cài đặt) như một script thông thường. Sau khi chạy thì bạn
sẽ có một command prompt như iPython, ở đây bạn có thể dùng các lệnh của pdb như `s` (step), `n`
(next), `c` (continue), `b` (break), `l` (list)... để đi theo từng bước một của quá trình thực
thi script.

    > /tmp/test(1)<module>()
    -> def main():
    (Pdb) l  # Ấn `l` để hiện source code
      1  ->	def main():
      2  	    print("Hello, Mankind!")
      3 
      4  	if __name__ == "onethuoneuthseou":  # Should be "__main__" here
      5  	    main()
    [EOF]
    (Pdb) n  # `n` -> next
    > /tmp/test(4)<module>()
    -> if __name__ == "onethuoneuthseou":  # Should be "__main__" here
    (Pdb) n
    --Return--
    > /tmp/test(4)<module>()->None
    -> if __name__ == "onethuoneuthseou":  # Should be "__main__" here
    (Pdb)   # Chương trình đã kết thúc vì __name__ không bằng "onethuoneuthseou"

Cách thứ hai là đặt break point ngay trong script của bạn (giống như cách dùng print). Chỗ nào bạn
nghi ngờ là có vấn đề trong code thì có thể đặt thêm dòng `import pdb; pdb.set_trace()` ngay trước
hoặc sau nó và chạy script như bình thường. Khi nào chỗ bạn đặt breakpoint được thực thi thì pdb
sẽ ngắt chương trình và cho bạn một command prompt như lúc nãy. Cũng giống như `print`, sau khi
dùng xong thì bạn phải xóa dòng này đi. Rõ ràng là bạn không muốn khách hàng đang chạy dở script
thì gặp một command prompt giữa chừng phải không? ;)

Cách thứ ba là launch pdb từ trong ipython, đây là cách tôi hay sử dụng nhất. Trong giao diện ipython,
bạn `import pdb`, sau đó bạn chạy `pdb.run("<câu lệnh muốn debug>")`. Ngay lập tức sẽ có một command
prompt. Ấn `s` (step) để bắt đầu debug.

Tài liệu [hướng dẫn sử dụng](http://docs.python.org/2/library/pdb.html) chi tiết của pdb.

## pudb

pdb thật tuyệt. Nhưng có lẽ bạn đã quen sử dụng một môi trường đồ họa nên giao diện dòng lệnh của
nó có thể gây rất nhiều khó khăn cho quá trình debug. Hơn nữa, bạn muốn theo dõi tất cả code của
mình trong khi debug. Vậy bạn nên dùng pudb.

Trước hết phải cài đặt pudb:

    (sudo) pip install pudb

pudb cũng là một module như pdb nhưng có giao diện đồ họa trong dòng lệnh viết bằng thư viện
ncurses giống như bộ debugger của Turbo Pascal ngày trước. Cách sử dụng nó thì cũng giống như pdb.
Nhưng tôi thường chạy theo cách thứ nhất, tức launch module như một script:
    
    python -m pudb my_script.py

Ngay sau đó bạn sẽ được chào đón bởi một giao diện đồ họa như thế này:
![Pudb](/assets/img/posts/2013-03-08-debug-python-pudb.png)

Phía trên cùng có một dòng hướng dẫn các phím tắt cơ bản cho quá trình debug. Bạn có thể ấn nút `?`
để biết thêm chi tiết. Có một số điều nho nhỏ mà tôi phải mất vài ngày sử dụng pudb mới nhận ra nên
sẽ viết ra đây cho các bạn luôn.

Trước hết là bạn có thể dùng con trỏ để di chuyển giữa các khu vục khác nhau của màn hình debug:
variables, stack, breakpoints. Khi ở trong khu vực variables, các cấu trúc dữ liệu phức tạp như list,
dictionary, tuple, class... thường không được hiện ra nhưng bạn có thể di chuyển con trỏ, select biến
đó và ấn một trong các phím *t/r/s/s* tương ứng với cách hiển thị *type/repr/str/custom*.

Thứ hai là khi đang ở một dòng code bất kỳ thì bạn có thể chuyển sang giao diện của interpreter để
gõ thử một vài dòng code như cách debug bằng ipython nói trên.

Thứ ba là bạn có thể nhấn tổ hợp phím Control-P để vào màn hình Preferences. Trong này có một vài tinh
chỉnh đáng chú ý như chỉnh theme, chọn `ipython` thay vì `python` làm interpreter, chỉnh cách thể
hiện default cho cấu trúc dữ liệu.

Thứ tư là để tắt pudb, bạn không dùng tổ hợp Control-C như trong các phần mềm dòng lệnh khác mà phải
ấn nút `q` (quit) và chọn *Quit* (còn có nút Restart, Examine ở đó nữa).

Thứ năm là pudb sử dụng module `pygments` để cung cấp tính năng syntax highlighting. Nhưng không rõ
vì module này hay vì cách pudb sử dụng module này mà nếu source code của bạn có ký tự Unicode (tiếng
Việt chẳng hạn) thì [pudb sẽ crash](https://github.com/inducer/pudb/issues/56). Vậy nên tôi thường 
gỡ cài đặt module này.

Ngoài ra trong mục help có khá nhiều clue để bạn tiếp tục khám phá pudb.


----


Trên đây là một số phương pháp cơ bản để debug phần mềm Python. Dĩ nhiên là còn rất nhiều để bạn khám
phá chỉ bằng việc Google "*python debug*" (thử google "*winpdb*" xem). Hi vọng với lượng thông tin ngắn ngủi
này thì bạn đã có thể bắt đầu fix lỗi đầu tiên trong phần mềm Python của mình.
