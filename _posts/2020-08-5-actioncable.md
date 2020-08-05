---
layout: post
title: Demo nhỏ về Action Cable - Tạo Realtime Notification
subtitle:
cover-img: /assets/img/path.jpg
tags: [rails]
comments: true
---

### Setup
Trước mắt để dùng ta cần config 1 số chỗ

Gemfile
```
gem 'redis'
```

config/cable.yml
```
default: &default
adapter: redis
url: redis://localhost:6379/1

development:
  <<: *default

test:
  <<: *default
```

Trong file routes thêm
```
mount ActionCable.server => "/cable"
```

Ở thư mục javascripts: tạo 1 file cable.js
```
//= require action_cable
//= require_self
//= require_tree ./channels

(function() {
  this.App || (this.App = {});

  App.cable = ActionCable.createConsumer();

}).call(this);

```

Nhớ restart server 

### Cách nó hoạt động

Phía server ta tạo meos_channel.rb để định nghĩa channel user sẽ connect đến tại `app/channels/meos_channel.rb`

```
class MeosChannel < ApplicationCable::Channel
  def subscribed
    stream_from "meo_meo_channel"
    # đăng kí nhận tất cả tin nhắn trên channel backs
  end

  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end
end
```
subscribed là method mặc định cho phép chúng ta đăng kí kênh channel mà chúng ta muốn lắng nghe.
Khi client connect channel nó sẽ sử dụng thằng channel chúng ta định nghĩa ở đây để lắng nghe những thay đổi

Phía client javascript:

```
App.notifications = App.cable.subscriptions.create({
      channel: 'MeosChannel'
    },
    {
      connected: function() {
        console.log("connected channel");
      },
      disconnected: function() {
        console.log("disconnected channel");
      },
      received: function(data) {
        console.log("received data")
        console.log(data);
      }
    });
  }).call(this);
```

connected: Called when the subscription is ready for use on the server

disconnected: Called when the subscription has been terminated by the server

received: Called when there's incoming data on the websocket for this channel

Khi tải trang bất kì ta xem log server sẽ thấy user đang kết nối đến kênh meo_meo_channel, phía client js sẽ nhận bất cứ data nào gửi đến meo_meo_channel

```
MeosChannel is transmitting the subscription confirmation
MeosChannel is streaming from meo_meo_channel
```

Ta có thể thử test server gửi message về cho client bằng rails console:

```
Loading development environment (Rails 5.2.2.1)
[1] pry(main)> ActionCable.server.broadcast "meo_meo_channel", message: "haha"
[ActionCable] Broadcasting to meo_meo_channel: {:message=>"haha"}
=> 1
[2] pry(main)> 
```

Nếu kết quả trả về là 1 tức là đã gửi tin nhắn đến meo_meo_channel thành công. meo_meo_channel sẽ lập tức gửi data về dưới client.

Nhìn log trên server sẽ thấy nó đã gửi {message: "haha"} đến meo_meo_channel
```
MeosChannel transmitting {"message"=>"haha"} (via streamed from meo_meo_channel)
```

Nếu kết quả  = 0 tức là gửi thất bại, cái này nguyên nhân có thể là do ta truyền sai tên kênh channel mà user đang kết nối đến hoặc do chưa đổi config cable.yml
```
default: &default
adapter: redis
url: redis://localhost:6379/1

development:
  <<: *default

test:
  <<: *default
```


Dưới client check trên console của trình duyệt sẽ nhận được data mà ta truyền từ server (do mình để console.log nó sẽ in ra kết quả mình trả về)
```
received data
{message: "haha"}
```
ở javascript
```
received: function(data) {
        console.log("received data")
        console.log(data);
}
```

ta  xử lý thêm đoạn received, khi nhận dữ liệu từ server về
ta append data zô html ví dụ ntn
```
$("p").append("data.message");
```
thì sẽ nhìn đoạn message dc hiển thị ra

=> Chung quy lại ý tưởng là xử lý server khi dữ liệu thay đổi ntn ta sẽ bắn message về cho channel, việc của client là dùng js để hiển thị nó lên


Vậy là qua ví dụ nhỏ trên ta đã mường tượng sơ qua về cách server truyền dữ liệu đến client ntn để nó realtime

Sau khi hiểu sơ cua về nó ta ứng dụng nó gửi notification nhé
Về ý tưởng có thể tham khảo tại đây

https://gist.github.com/excid3/4ca7cbead79f06365424b98fa7f8ecf6
