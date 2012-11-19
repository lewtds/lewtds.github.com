---
layout: post
title: "Release notes: v0.2"
description: "aoeoh"
category:
tags: [release]
tagline: Type with fun
---

# Phát hành IBus Bogo Engine version 0.2

Một tuần sau khi phát hành phiên bản đầu tiên (0.1.0), nhóm phát triển
BoGoEngine xin chính thức giới thiệu phiên bản tiếp theo (0.2) với
nhiều cải tiến

## Lỗi được sửa
- telex: Sửa tất cả các vấn đề liên quan đến phím 'w'
- telex: Lỗi 'casse' -> 'cáe'
- telex: Lỗi khi gõ 'nheechs', 'hueechs'
- telex: Lỗi khi gõ 'tueej'
- telex: Lỗi khi gõ 'tesst'

## Cải tiến
- Gõ chữ hoa/thường thoải mái
- Thêm script test tự động thành phần kiểm tra chính tả với từ điển
6631 từ đơn âm chính tả tiếng Việt của Nguyễn Xuân Minh và Ivan Garcia
dựa theo từ điển tiếng Việt của Hồ Ngọc Đức.

## Khác

Người dùng có thể dùng thử cách gõ tiếng Anh mới của IBus BoGo
Engine.  Để sử dụng tính năng này, gõ vào lệnh

    $ dconf-editor
    
sau đó chuyển giá trị của key */org/kgcd/ibus-bogo/spellchecking* trong
dconf-editor thành *true*.

Ví dụ:

    Để gõ chữ "variable"
    Cách gõ truyền thống: varriable
    Cách gõ với khi bật spellchecking: variable
    
## Download và cài đặt

Việc [cài đặt](https://github.com/BoGoEngine/ibus-bogo-python/wiki/C%C3%A0i-%C4%91%E1%BA%B7t-%7C-Install) không thay đổi so với phiên bản cũ. Nếu bạn dùng Ubuntu
thì chỉ cần chạy `sudo apt-get update && sudo apt-get upgrade` để cập
nhật lên phiên bản mới.
