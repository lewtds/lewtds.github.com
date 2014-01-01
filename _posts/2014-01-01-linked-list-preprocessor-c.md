---
layout: post
title: Linked list bằng preprocessor trong C
tags: c, programming
description: "Giới thiệu về queue.h"
---

C nói chung không phải là ngôn ngữ dễ. Nếu bạn khởi đầu với Python hay Java thì sẽ rất shock khi viết chương trình đầu tiên bằng C bởi mọi cấu trúc dữ liệu bạn thường dùng như *list*, *hashmap*/*dictionary* đều không có sẵn mà phải tự build tay hết. Trong đó kiểu dữ liệu mà bạn nhớ nhất có lẽ là list, đúng hơn là [linked list][1] với độ dài vô hạn.

[1]: http://en.wikipedia.org/wiki/Linked_list

Phần lớn mọi người tự build lấy struct và các hàm xử lý list riêng cho mình. Và nếu bạn giống tôi (tức là một tay gà mờ), thì có thể list của bạn trông sẽ giống như thế này:

{% highlight C %}
#include <malloc.h>
#include <stdio.h>
#include <string.h>

struct ListEntry {
    void *item;
    struct ListEntry *next;
    struct ListEntry *prev;
};

struct List {
    struct ListEntry *first;
    struct ListEntry *last;
    int count;
};

void list_init(struct List *list) {
    memset(list, 0, sizeof(struct List));
}

void list_append(struct List *list, void *item) {
    struct ListEntry *new_entry = malloc(sizeof(struct ListEntry));
    new_entry->item = item;

    if (list->count == 0) {
        list->first = new_entry;
    } else {
        list->last->next = new_entry;
        new_entry->prev = list->last;
    }

    list->last = new_entry;
    list->count++;
}

void list_free(struct List *list) {
    for(struct ListEntry *iter = list->first;
        iter != NULL;
        iter = iter->next) {
        free(iter);
    }
    free(list);
}

int main() {
    struct List *my_int_list = malloc(sizeof(struct List));
    list_init(my_int_list);

    int num1 = 10;
    int num2 = 20;

    list_append(my_int_list, &num1);
    list_append(my_int_list, &num2);

    for(struct ListEntry *iter = my_int_list->first;
        iter != NULL;
        iter = iter->next) {
        int *item = iter->item;
        printf("%d\n", *item);
    }

    list_free(my_int_list);

    return 0;
}

{% endhighlight %}

Thiết kế này nói chung là hoạt động tốt nhưng có một điểm cực kỳ bất lợi là sử dụng con trỏ void. Khi debug, bạn vẫn có thể traverse qua list của bạn bằng tay nhưng các entry trong list sẽ hiện ra là một con trỏ void vô cùng tối nghĩa. Nếu bạn may mắn có một debugger tốt thì còn có thể ép kiểu con trỏ đó về đúng kiểu mong muốn. Nhưng chắc cũng chả ai muốn đi ép kiểu thủ công với từng item như thế cả.

Hơn nữa, khi bạn viết một hàm có chứa một list trong danh sách argument thì cũng rất khó để người sử dụng hiểu ngay được list của bạn chứa những phần tử kiểu gì. Họ sẽ phải dựa vào những dấu hiệu trong tên biến hay trong tài liệu đi kèm (đó là nếu bạn có viết =) ).

Nói tóm lại là không **type-safe**.

Rất may nếu bạn sử dụng Linux, FreeBSD hay bất cứ hệ điều hành Unix nào thì hệ thống đã đi kèm sẵn một "thư viện" linked list cho C nằm ở `sys/queue.h` sử dụng hoàn toàn [preprocessor][3] và quan trọng hơn cả là **type-safe**. Thư viện này thực chất chỉ là một file .h được viết bởi trường Đại học California và phát hành với giấy phép BSD (sử dụng thoải mái trong các dự án commercial, miễn là giữ tên tác giả và phát hành cùng giấy phép gốc).

Cách tốt nhất để học cách sử dụng thư viện này là đọc [manpage][2] của nó bằng lệnh `man queue`, có giải thích và ví dụ rất rõ ràng, hoặc đọc thẳng [mã nguồn][1]. Tuy nhiên, tôi cũng sẽ làm một tutorial giống hệt chương trình ở trên nhưng sử dụng `queue.h`. Những dòng nào có macro sẽ được expand trong comment:

[1]: http://fxr.watson.org/fxr/source/sys/queue.h
[2]: http://www.freebsd.org/cgi/man.cgi?query=queue
[3]: http://en.wikipedia.org/wiki/C_preprocessor

{% highlight C %}
#include <stdio.h>
#include <sys/queue.h>

struct MyStruct {
    int a_field;
    float another_field;
};

TAILQ_HEAD(MyStructList, MyStructListEntry);
/*
 * struct MyStructList {
 *     struct MyStructListEntry *first;
 *     struct MyStructListEntry **last;
 * }
 */

struct MyStructListEntry {
    struct MyStruct *item;
    TAILQ_ENTRY(MyStructListEntry) list_pointers;
    /*
     * struct {
     *     struct MyStructListEntry *next;
     *     struct MyStructListEntry **prev;
     * } list_pointers;
     */
};

int main()
{
    struct MyStructList list;

    TAILQ_INIT(&list);
    /*
     * do {
     *     (&list)->first = (void *) 0;
     *     (&list)->last = &(&list)->first;
     * } while (0);
     */

    struct MyStruct item1 = { 10, 10 };
    struct MyStruct item2 = { 20, 20 };

    struct MyStructListEntry entry1;
    struct MyStructListEntry entry2;

    entry1.item = &item1;
    entry2.item = &item2;

    TAILQ_INSERT_TAIL(&list, &entry1, list_pointers);
    /*
     * do {
     *     (&entry1)->list_pointers.next = (void *)0;
     *     (&entry1)->list_pointers.prev = (&list)->last;
     *     (&list)->last = (&entry1);
     *     (&list)->last = &(&entry1)->list_pointers.next;
     * } while (0);
     */

    TAILQ_INSERT_TAIL(&list, &entry2, list_pointers);

    struct MyStructListEntry *iter;
    TAILQ_FOREACH(iter, &list, list_pointers) {
    /*
     * for ((iter) = ((&list)->first);
     *      (iter);
     *      (iter) = ((iter)->list_pointers.next)) {
     */
        printf("%d\n", iter->item->a_field);
    }

    return 0;
}
{% endhighlight %}

Như vậy có thể thấy hai implementation gần như giống hệt nhau về mặt ngữ nghĩa. Tuy nhiên ở implementation thứ hai thì ta đạt được type-safe bằng cách **định nghĩa kiểu riêng** cho mỗi loại list: list của `int` sẽ có kiểu `IntList`, list của `MyStruct` có kiểu `MyStructList`. Việc định nghĩa kiểu này được thực hiện bởi macro:

```
TAILQ_HEAD (tên-kiểu-list, tên-kiểu-entry);
```

Sau đó chúng ta lại tự định nghĩa kiểu cho entry. Bên trong mỗi entry có 2 con trỏ `next` và `prev` được bọc trong trường `list_pointers`. Tên của trường này là do người dùng tự chọn và cần thiết cho gần như mọi thao tác với list như `TAILQ_INSERT_TAIL`, `TAILQ_CONCAT`,...

Có thể ngay trong đầu bạn lúc này đang nảy ra ý tưởng đặt luôn `list_pointers` trong `MyStruct` và dùng chính nó làm list entry cho gọn (giống tôi lúc đầu). Nhưng hãy thật cẩn thận vì mỗi entry chỉ có thể nằm trong một list và  bạn có thể tạo ra những đứt gãy không mong muốn như miêu tả dưới đây:

```
List 1: A -- B -- C
List 2: 1
--
1.next = B
B.prev = 1
--
List 1: A
List 2: 1 -- B -- C
```

Trong khi kết quả bạn muốn là:

```
List 1: A -- B -- C
List 2: 1 -- B
```

Một chú ý nữa là một khi đã xin cấp phát bộ nhớ thì cần phải giải phóng và với list thì phải giải phóng thêm cả các phần tử con. Trong `queue.h` không định nghĩa nhưng bạn có thể dùng macro của tôi:

{% highlight C %}
#define TAILQ_FREE(iter, list, field) do {  \
    TAILQ_FOREACH(iter, list, field) {      \
        free(iter);                         \
    }                                       \
    free(list);                             \
} while (0)
{% endhighlight %}

Have fun!
