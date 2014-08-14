---
layout: post
title: Lập trình C trên Linux the hard way
tags: programming
description: ""
---

Tôi thấy rất nhiều bạn hỏi cách lập trình C trên Ubuntu. Nếu các bạn muốn nhẹ
nhàng, ăn sẵn thì có thể bắt đầu với một [IDE][1] như [Code::Block][2] hay
[Eclipse có cài CDT][3] hay thậm chí là cài [Borland Turbo C trên DOSBox][4].
Còn cá nhân tôi thích nhất [Qt Creator][5]. Nếu bạn chọn cách này thì vẫn nên
đọc phần [conio.h](#conio.h) bên dưới.

Nhưng nếu các bạn muốn thực sự hiểu sâu vấn đề thì tốt nhất nên code bằng
editor và compile bằng tay. Giống như cách mà mọi người vẫn làm từ những năm
70 đến giờ.

[1]: http://en.wikipedia.org/wiki/Integrated_Development_Environment
[2]: http://www.codeblocks.org/
[3]: http://eclipse.org/cdt/
[4]: http://linuxmotion.com/tutorials/78-how-to-install-turbo-c-in-linux
[5]: http://en.wikipedia.org/wiki/Qt_Creator

## Setup

> Bài viết tập trung vào môi trường Ubuntu và các distro tương thích. Một số
> lệnh trên các distro khác như Fedora hay Arch Linux sẽ hơi khác một chút.
> Nhưng nếu bạn hiểu được ý tưởng căn bản thì áp dụng ở đâu cũng được.

Trước hết chúng ta sẽ cần một text editor. Sẵn có trên Ubuntu thì có Gedit,
nano. Bạn nào dùng KDE thì có Kate cũng rất tốt. Trendy hiện nay thì có
[Sublime Text 3][1]. Hard core hơn thì có [Emacs][2] và [Vim][3], ban đầu khó
dùng nhưng rất đáng bỏ công học. Bạn dùng editor nào cũng được, tất cả đều là
editor tốt.

Tiếp theo sẽ cần một bộ compiler. Thông thường với C/C++ trên Linux thì **gcc**
(GNU C Compiler) là compiler thông dụng nhất. Bạn có thể cài đặt gcc và các
công cụ hỗ trợ bằng một lệnh sau:

```
sudo apt-get install build-essential
```

[1]: http://www.sublimetext.com/3
[2]: http://david.rothlis.net/emacs/howtolearn.html
[3]: http://learnvimscriptthehardway.stevelosh.com/

## Compile thủ công

Bây giờ thì ta có thể bắt đầu với một ứng dụng đầu tiên. Bạn gõ chương trình
sau vào file `hello.c` bằng editor đã chọn ở trên.

> Một lập trình viên nên có thói quen sắp xếp trật tự ngăn nắp các file và thư mục.
> Bạn có thể tạo một thư mục `dev` ở `$HOME`
> trong đó có chứa các project, nếu muốn có thể thêm một lớp folder chứa ngôn ngữ nữa:
> 
> ```
> $ mkdir --parents $HOME/dev/c/hello
> $ cd $HOME/dev/c/hello
> $ gedit hello.c &
> ```

```C
#include <stdio.h>

int main(int argc, char **argv)
{
	printf("Hello, World!\n");
	return 0;
}
```

Compile và chạy:

```
$ gcc hello.c -o hello
$ ./hello
Hello, World!
```

Ở đây ta có thể thấy lệnh gcc được gọi với tham số hello.c là tập tin đầu vào
và flag `-o hello` thể hiện tên của tập tin đầu ra (`-o` là viết tắt của out).
Sau đó ta chạy file hello vừa tạo ra bằng lệnh `./hello`. Dấu chấm và gạch
chéo nghĩa là chạy file hello ở thư mục hiện tại (biết đâu trong `$PATH` của
bạn lại chứa một chương trình cũng tên là hello?). Nếu bạn không đặt tên cho
file đầu ra thì theo truyền thống, gcc sẽ tạo file có tên là **a.out**.

Bây giờ bạn muốn thay đổi chương trình và mở rộng thêm một file nữa. Coi như là
**main.c** đi. Bạn sẽ tách lệnh hiển thị ra màn hình thành hàm `hello()` chứa trong
**hello.c** còn hàm `main()` thì chứa trong **main.c**.

Chương trình giờ gồm 3 file:


**hello.h**

```C
#ifndef __HELLO_H
#def __HELLO_H

void hello();

#endif
```

**hello.c**

```C
#include <stdio.h>
#include "hello.h"

void hello()
{
	printf("Hello, World!\n");
}
```

**main.c**

```C
#include "hello.h"

int main(int argc, char **argv)
{
	hello();
	return 0;
}
```

Bạn vẫn compile như trên nhưng mở rộng thêm file main.c vào câu lệnh.

```
$ gcc main.c hello.c -o hello
$ ./hello
Hello, World!
```

## Make

Rất tuyệt. Nhưng bạn có để ý nãy giờ là việc compile bằng cách chạy
gcc trực tiếp thế này rất thủ công không? Stuart Feldman cũng nghĩ
vậy [vào năm 1976][1] và đã viết chương trình Make để tự động hóa những
thao tác lặp đi lặp lại như thế này. Make đã được cài tự động
cùng với gói `build-essential` ở trên.

[1]: http://en.wikipedia.org/wiki/Make_%28software%29

Để sử dụng Make, trước hết chúng ta sẽ viết một Makefile đơn giản,
đặt tên tập tin là **Makefile** và nằm cùng thư mục với chương trình
C của chúng ta.

> CHÚ Ý: Sau khi gõ ":" xuống dòng thì bạn nhấn một dấu TAB.

**Makefile**

```Makefile
all: hello run

hello: hello.c main.c
	gcc main.c hello.c -o hello

run:
	./hello

clean:
	rm --recursive --force hello
```

Bây giờ thay vì chạy gcc thì bạn chạy:

```
$ make all
gcc main.c hello.c -o hello
./hello
Hello, World!
```

Có thể thấy là chỉ với một lệnh make, các thao tác thủ công của chúng ta
đều đã được thực hiện. Cùng phân tích Makefile một chút.

Một Makefile bao gồm các target, là những thứ bạn muốn thực hiện. Ở đây có 3
target là `all`, `hello` và `run`. Sau dấu hai chấm của `all` có liệt kê 2
target còn lại, nghĩa là target `all` phụ thuộc vào `hello` và `run`. Như vậy,
khi chúng ta `make all` thì Make sẽ thực thi `hello` và `run` trước, sau đó
mới thực thi `all`. Nếu chỉ chạy `make` không thì target đầu tiên trong file
sẽ được thực thi.

Các target trong Makefile thực chất là các tên tập tin. Như vậy có thể "dịch"
nội dung target `hello` ở trên là "tôi cần file hello, và các lệnh dưới đây là
hướng dẫn tạo file hello". Bây giờ nếu bạn chạy `make all` một lần nữa thì sẽ
thấy là không còn dòng gcc hiện ra nữa. Target `hello` không được thực thi vì
Make biết rõ là đã tồn tại file hello trong hệ thống. Nhưng nếu bạn `make
clean` (để xóa file hello) thì nó sẽ lại được thực thi. Nó cũng sẽ được
chạy nếu một trong các dependency của nó (hello.c và main.c) bị thay đổi.

Ý tưởng của Make là tạo một mạng lưới các tập tin và target phụ thuộc lẫn nhau
và chỉ thực thi một target nếu kết quả của nó chưa tồn tại hoặc một trong
các tập tin phụ thuộc của nó có thay đổi.

Lấy thêm một ví dụ, chúng ta có thể tạo file run trong cùng thư mục và target
run cũng sẽ không được thực thi.

```
$ touch run
$ make all
make: Nothing to be done for 'all'.
$ rm run
```

Để tránh hiện tượng này thì ta có thể thêm dòng `.PHONY` vào cuối Makefile
để ép Make luôn thực thi target `run`:

```Makefile
.PHONY: run
```

Nhìn lại Makefile, bạn có thể thấy là chúng ta lặp lại chữ hello quá nhiều.
Hoàn toàn không tuân thủ theo nguyên tắc
[DRY](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself) (Don't Repeat
Yourself). Để giải quyết vấn đề này, chúng ta sẽ dùng biến.

```Makefile
PRGNAME = hello
HELLO_SRC = hello.c main.c

all: $(PRGNAME)

$(PRGNAME): $(HELLO_SRC)
	$(CC) $(HELLO_SRC) -o $PRGNAME

run: $(PRGNAME)
	./$(PRGNAME)

clean:
	rm --recursive --force $(PRGNAME)

.PHONY: run clean
```

Makefile giờ trông đã "generic" hơn. Thậm chí gcc cũng đã được thay bằng biến
`$(CC)`, là biến sẵn có của Make, có nội dung là compile chuẩn của hệ thống. Nếu
sau này bạn muốn compile bằng compiler khác như clang thì chỉ cần chạy
`CC=clang make all` là được. Không cần phải thay đổi Makefile.

Với chương trình nhỏ vài file như thế này thì Makefile như vậy là hoàn toàn
OK. Nhưng bạn có thể nghiên cứu thêm trên Google và [tài liệu tra cứu của GNU Make][1].
Ngoài ra tôi cũng thấy Makefile của [trình phiên dịch ngôn ngữ Lua][2]
được viết cũng rất đơn giản mà vẫn cross-platform, mang sang Windows hay Mac
OS X vẫn compile được. Rất hợp để nghiên cứu.

Sau này bạn có thể tìm hiểu thêm về [Autotools][3] và [CMake][4] để thay thế việc viết
Makefile thủ công và tạo hệ thống build tương thích nhiều nền tảng mà không cần
phải lo nghĩ nhiều.

[1]: https://www.gnu.org/software/make/manual/html_node/Introduction.html#Introduction
[2]: https://github.com/lua/lua/tree/950db94fc42fd6fa9186c0aa9410a0f721504112
[3]: http://en.wikipedia.org/wiki/GNU_build_system
[4]: http://www.cmake.org/cmake/help/cmake_tutorial.html

## conio.h

Tạm coi như bạn đã hiểu cách build đi. Nhưng khả năng cao là bạn sẽ
vấp tiếp một vấn đề nữa. Phần lớn các giáo trình lập trình C/C++ ở Việt Nam
được xây dựng dựa trên môi trường DOS/Windows. Bạn có thể nhận biết ngay điều
này nếu thấy trong giáo trình có đề cập đến thư viện **conio.h** và hàm
`getch()`. Thư viện này chỉ có cũng như chỉ hoạt động trên môi trường
DOS/Windows. Mục đích của nó là cho phép bạn thực hiện các thao tác vào/ra
trực tiếp với console.

Console (hay terminal) thông thường hoạt động trong chế độ có buffer ([cooked mode][1]).
Tức là các phím nhấn của bạn không được gửi trực tiếp đến chương trình
mà sẽ được giữ lại ở phần mềm console để cho phép bạn chỉnh sửa tự do trước
khi nhấn ENTER và gửi đến chương trình. Tương tự, các thao tác `printf()` cũng
không ghi trực tiếp vào console mà ghi vào một file đặc biệt, gọi là file
`stdout`. Hệ thống sẽ chuyển tiếp nội dung file này vào console. File này có
buffer nên đôi khi lệnh `printf()` của bạn sẽ không hiện gì ra màn hình hết và bạn
phải `fflush(stdout)` thủ công. Conio.h cho bạn công cụ để đọc/ghi trực tiếp
với console mà không qua buffer. VD: `getch()` sẽ return bất cứ ký tự nào vừa
được nhấn xong mà không cần nhấn ENTER.

Chúng ta sẽ implement lại một số tính năng của `conio.h` trên Linux.

[1]: http://en.wikipedia.org/wiki/Cooked_mode

### clrscr()

Trước hết là hàm clrscr() dùng để xóa trắng terminal. Với Linux thì có thể
dụng lệnh `clear`:

```C
system("clear");
```

### getch()

Trên Windows, vì khi chạy từ IDE, cửa sổ console sẽ tắt ngay lập tức sau khi
chương trình chạy xong nên người ta thường thêm lệnh `getch()` ngay cuối chương
trình làm "đứng hình" console để nhìn rõ kết quả. Với Linux thì lúc nào
chúng ta cũng ở trong terminal nên không cần lệnh này.

Nhưng nếu bạn cần tính năng kiểu *Press any key to continue* thì có thể dùng
`getchar()` và đổi lời nhắn thành *Press ENTER to continue*.

Hoặc hardcore hơn là chuyển terminal về [cbreak mode][1], `getchar()`, rồi chuyển
lại trở về cooked mode.

```C
system("/bin/stty cbreak");
getchar();
system("/bin/stty cooked");
```

[1]: http://en.wikipedia.org/wiki/Cooked_mode#cbreak_mode

### textcolor() và textbackground()

Terminal thông thường trên Linux tuân theo [chuẩn VT100][1], chuẩn này có định
nghĩa một số tính năng cho bạn điều khiển trực tiếp terminal sử dụng một loại
mã đặc biệt gọi là escape code. Bạn gửi mã này tới terminal chỉ đơn giản bằng
cách `printf` nó ra với ký tự đầu tiên là <ESCAPE> (mã ASCII 0x1B).

Ví dụ, để đổi màu text thành đỏ và background thành vàng chúng ta làm như sau:

```C
printf("\x1B[31;43m");
printf("This text is red on yellow background.");
printf("\x1B[0m\n");  // Reset + new line
printf("This text is normal.\n");
```

Trong đó, 31 là mã màu đỏ foreground còn 43 là màu vàng background còn 0 là mã
reset. Bạn có thể xem một số mã màu và các hiệu ứng khác [ở đây][2].

[1]: http://en.wikipedia.org/wiki/VT100
[2]: http://www.termsys.demon.co.uk/vtansi.htm#colors

### gotoxy()

Tương tự như trên, chúng ta có thể `printf` mã `<ESC>[{ROW};{COLUMN}H`
để đưa con trỏ tới vị trí bất kỳ trong terminal.

```C
// Row 4; column 6
printf("\x1B[4;6H");
```

----

Pheww, vậy là xong một bài hướng dẫn khá dài. Hy vọng tôi đã giúp bạn hiểu hơn
về lập trình C trên Linux nói riêng và Unix nói chung.