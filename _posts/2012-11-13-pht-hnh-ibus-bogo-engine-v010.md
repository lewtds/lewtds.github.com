---
layout: post
title: "Phát hành IBus BoGo Engine v0.1.0"
description: "...với nhiều tính năng mới."
category: 
tags: [release]
---

Nhóm phát triển xin trân trọng gửi đến các bạn sản phẩm IBus BoGo Engine
phiên bản đầu tiên.

Version: 0.1.0
License: [GNU General Public License Version 3](http://www.gnu.org/licenses/gpl-3.0.txt)

## Tính năng

* Kiểu gõ: Telex, VNI
* Bảng mã: Unicode
* Loại bỏ hoàn toàn dấu gạch chân (preedit string) so với các bộ gõ Tiếng Việt dựa trên IBus khác
* Hỗ trợ mở rộng các kiểu gõ theo ý muốn (dành cho nhà phát triển)
* Dễ dàng gõ tiếng Anh lẫn trong văn bản tiếng Việt

## Hướng dẫn cài đặt


### Từ gói cài đặt cho các bản phân phối Linux thông dụng

Đây là cách đơn giản nhất.

* [Ubuntu](https://github.com/BoGoEngine/ibus-bogo-python/downloads)
(hoặc thêm [ppa:ndtrung4419/bogo](https://launchpad.net/~ndtrung4419/+archive/bogo)
và cài gói ibus-bogo)
* [Arch Linux](https://aur.archlinux.org/packages/ibus-bogo/)
* Fedora (Đang cập nhật)

### Từ mã nguồn

IBus BoGo Engine được xây dựng có sử dụng các thành phần sau, hãy chắc
chắn rằng các thành phần này đã được cài trên máy nếu bạn cần cài đặt
từ mã nguồn:

* ibus 1.4
* python 2.7
* python-gi
* gir1.2-ibus-1.0 (gobject introspection data)

Tải mã nguồn tại [đây](https://github.com/BoGoEngine/ibus-bogo-python/tags)
và cài đặt như hướng dẫn tại [đây](https://github.com/BoGoEngine/ibus-bogo-python)


### Cấu hình sau khi cài đặt

Để đảm bảo chương trình vận hành như ý muốn. Thêm các dòng sau vào
file ~/.profile sau đó logout và login:
    
    export GTK_IM_MODULE=ibus
    export XMODIFIERS=@im=ibus
    export QT_IM_MODULE=xim
    
## Thông báo vấn đề
 
Nếu bạn gặp bất cứ khó khăn gì hoặc có đề xuất về chức năng cho IBus
BoGo Engine xin hãy đừng ngần ngại chia sẻ với chúng tôi tại 
[bug tracker](https://github.com/BoGoEngine/ibus-bogo-python/issues) của nhóm:

[https://github.com/BoGoEngine/ibus-bogo-python/issues](https://github.com/BoGoEngine/ibus-bogo-python/issues)


## Tham gia phát triển

Rất hoan nghênh các bạn đến với nhóm phát triển. Đây là nơi chúng tôi làm việc:

[https://github.com/BoGoEngine](https://github.com/BoGoEngine)
