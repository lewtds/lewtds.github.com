---
layout: post
title: Python Quickstart
---

## Sơ lược về Python

Dưới đây là những kiến thức tóm tắt. Có thể tham khảo các nguồn khác đầy đủ hơn (có lẽ cũng dễ hơn nữa =]):
- <http://code.google.com/edu/languages/google-python-class/>
- <http://www.openbookproject.net/thinkcs/python/english2e/>
- <http://www.python.org/doc/>

### Giới thiệu về ngôn ngữ

- Python là một ngôn ngữ *thông dịch (interpreted)*, tức là ngôn ngữ không
cần phải biên dịch một lần ra file chạy mà đọc code đến đâu chạy đến
đấy.

- Khi chạy lệnh `python` ta sẽ có một giao diện dòng lệnh giống của Unix,
có thể chạy từng dòng code Python ngay trực tiếp tại đây. Ngoài ra còn
có `ipython` là một phiên bản cải tiến hơn của dòng lệnh `python` có
đánh mã màu, tự động điền khi ấn tab (autocomplete) và nhiều tính năng
khác khiến việc khám phá ngôn ngữ trở nên dễ dàng hơn.

    __NOTE__ Nên phân biệt 2 khái niệm ở đây là ngôn ngữ Python viết hoa chữ 
    cái đầu bằng text thường và `python` là dòng lệnh và trình thông dịch
    của Python.

- Python có mặt ở mọi nơi và dễ dàng cài đặt. Gần như tất cả các bản
phân phối Linux đều cài sẵn Python, Mac OS X từ bản Lion trở đi cũng
cài sẵn Python. Trên Windows có thể tải Python từ [trang chủ](http://www.python.org/getit/windows/).

- Python có rất nhiều module đi kèm và vô số module từ các bên thứ ba
để tăng thêm tính năng cho ngôn ngữ như truy cập mạng, hệ thống file,
mã hóa, giao diện đồ họa, 3D...

- Python có hệ thống documentation rất độc đáo, đi kèm trong code luôn.
  VD: vào `python\ipython` chạy lệnh sau `help(''.upper)`

***

### Basic stuffs

#### Biến (variable)

- Biến trong Python không cần khai báo, dùng đến đâu tạo đến đấy. Thậm chí không
cần nói rõ biến là kiểu biến gì, Python sẽ tự suy luận từ giá trị được gán cho biến.

- Có một số loại biến cơ bản là:
    - `int` (số nguyên)
      
      VD: `a = 1`
    
    - `float` (số thập phân)
    
      VD: `a = 1.5`
    
    - `string` (chuỗi ký tự)
    
      VD: `a = 'abc'`
      
          hoặc `a = "abc"` # tự động escape dấu '
      
          hoặc `a = """abc"""` # escape tất cả mọi thứ
    
    - `list` (danh sách, truy cập bằng thứ tự, giống array nhưng linh hoạt hơn rất nhiều)
    
      VD: `a = [1, 2, 3]` -&gt; `a[0]` = `1`
      
          `b = ['a', 'b', 'c']` -&gt; `b[1]` = `'b'`
      
          `c = [1, 'b', 3.5]` -&gt; `c[1]` = `3.5`
    
    - `dictionary` (mapping `<khái niệm>:<ý nghĩa>` tương tự `list` 
    nhưng truy cập thành phần bằng tên chứ không bằng số thứ tự)
    
      VD: `months = { "Jan" : 1, "Feb" : 2, "Mar" : 3 }` -&gt; `month["Jan"]` = `1`

#### If/else

Chả cần phải giải thích nhiều =))

    if <điều kiện 1>:
        <code>
    elif <điều kiện 2>: # else + if = elif
        <code>
    elif <điều kiện n>:
        <code>
    else:
        <code>

VD:

    if age > 18:
        print('legal')
    elif age == 18:
        print('barely legal')
    else:
        print('go home, kid')

__NOTE__ Trong Python không dùng begin/end hay {} để đánh dấu code block mà dùng
indentation (việc thụt đầu dòng) để đánh dấu. Thường dùng 4 dấu cách hoặc một
phím tab để indent. Không cần đóng code block vì chỉ cần khác indent tức là khác
level rồi.

VD:

    level 1
        level 2
            level 3
            vẫn level 3
        quay lại level 2
            

#### Vòng lặp

- Vòng lặp `while`:

        while <điều kiện>:
            <code>
            
    VD:
    
        i = 0
        while i < 5:
            print(i)
            i = i + 1
         
- Vòng lặp `for`:

        for <tên biến> in <list>:
            <code>
            
    VD:
    
        for i in [1, 2, 3, 4]:
            print(i)

    __NOTE__ Python có hàm range() để tạo list tự động rất phù hợp để dùng chung
    với `for`.
    
    VD: `range(5)` cho kết quả là một list như sau `[0, 1, 2, 3, 4]`.
    Chạy `help(range)` để biết thêm chi tiết.

#### Đối tượng

Trong Python *gần như* mọi thứ đều là các đối tượng. Đối tượng là những *cục code*
có chứa các biến và các hàm để thực hiện trên đối tượng đó.

VD: 'abc' là một đối tượng string. Ta có thể gọi hàm `'abc'.upper()` để return 
'ABC' hoặc `'ABC'.lower()` để có được 'abc'.
Chạy lệnh `dir('abc')` để list những tính năng (hàm, biến) mà đối tượng 'abc' sở hữu.
Sau đó dùng hàm  `help()` để biết thêm về những hàm, biến đó. Thử chạy `help('abc'.lower)`
xem :D

#### Module

Module là các đối tượng được viết sẵn để tạo thêm tính năng cho ngôn ngữ.
Khi muốn sử dụng một tính năng thuộc module nào đó thì phải import module đó:
`import <tên module>`.

VD:

- Module `sys` (system) để truy cập các thành phần hệ thống.
VD: `sys.exit()` để thoát chương trình. `sys.version` chứa phiên bản Python hiện
tại. `sys.platform` chứa tên hệ điều hành hiện tại. `help(sys)` hoặc trong
ipython, gõ `sys.` và nhấn `TAB` để biết thêm chi tiết.

- Module `time` có các hàm, biến, hằng để làm việc với thời gian. VD: `time.sleep(3)`
sẽ pause chương trình trong 3 giây.

***

### Now let's get dirty

Dưới đây là một chuỗi bài tập về việc implement lại các lệnh cơ bản của 
Unix bằng Python.

#### Bài tập 1: cat

`cat` là lệnh để đưa input ra output.
Chạy `cat` không có đối số, nó sẽ lặp vô hạn, đợi input từ người dùng
và ghi ngược cái input đó ra màn hình.

Thường dùng để:

- Tạo file mới

        cat > new_file.txt
    
    (khi chạy lệnh, cat sẽ đợi input, gõ văn bản vào và nhấn Control-D để kết thúc)
    
- In nội dung file text ra màn hình

        cat text_file.txt
    
Ý tưởng:

1. Nếu có argument là file thì mở file và in ra màn hình.
2. Nếu không thì lặp vô hạn việc đọc input và ghi ra output.

__NOTE__ Để làm được bài tập này thì cần biết thêm một số khái niệm sau

- Module `sys` dùng để giao tiếp với hệ thống. Nó có một số hàm và biến khá hay
ho:
    - `sys.exit(<exit code>)` dùng để thoát chương trình khi cần.
    - `sys.argv` là một list chứa các argument của chương trình. Hãy tưởng tượng
    chương trình là một hàm, được hệ thống gọi, có đối số, `sys.argv` cho ta truy
    cập các đối số đó.
    
        VD: Nếu chạy lệnh sau trong terminal `echo a b c` thì sys.argv sẽ chứa
        giá trị `['echo', 'a', 'b', 'c']`

- Hàm `raw_input()` dùng để nhập dữ liệu từ người dùng, luôn return một string.
Có thể so sánh với `scanf()` của C và `readln()` của Pascal.

- Hàm `open(<tên file đầy đủ>)` dùng để mở file. Return một đối tượng file.

Code mẫu:

{% highlight python %}
#-*- utf-8
# Dòng trên để đánh dấu file này là file Unicode.

# Module sys để giao tiếp với hệ thống
import sys

# Hàm len() để đo độ dài một string hoặc một list
# Nếu list sys.argv có độ dài bằng 1 thì chứng tỏ không có argument nào
# -> lặp vô hạn
if len(sys.argv) == 1:
    while True:
        print(raw_input())
else:
    # Nếu không thì sys.argv[1] sẽ chứa path đến file người dùng muốn mở
    path = sys.argv[1]
    # Mở file
    f = open(path)
    # In toàn bộ nội dung ra màn hình
    print(f.read())
    # Xong oy thì đóng cái file đấy lại
    f.close()
{% endhighlight %}

#### Bài tập 2: echo

`echo` là lệnh để in ra màn hình. Cách dùng:

    echo <text để in ra>
    VD: `echo em bị điên` sẽ có kết quả là `em bị điên`

Ý tưởng:

1. Nếu không có argument nào thì thoát
2. Nếu có thì nối hết lại thành một string và in ra màn hình


__NOTE__ Để làm được bài tập này thì cần biết thêm một số khái niệm sau

- trong đối tượng string có một hàm là hàm `join(<một list>)` dùng để nối các
phần tử của list với nhau sử dụng dấu phân cách chính là đối tượng string đang
nhắc tới.

VD: `';'.join([1, 2])` return kết quả là `'1;2'`

Code mẫu:

{% highlight python %}
import sys

if len(sys.argv) == 1:
    sys.exit()
else:
    print(' '.join(sys.argv))
{% endhighlight %}

#### Bài tập 3: more

`more` giống như `cat` nhưng không in hết một đống ra màn hình mà in dần thành
từng trang ~24 dòng một. Nhấn phím enter để xuống một dòng, phím cách để xuống
một trang.

Cái này coi như là BVN đi =)
