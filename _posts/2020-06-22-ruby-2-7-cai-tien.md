---
layout: post
title: Ruby 2.7 đã cải tiến những gì
subtitle:
cover-img: /assets/img/27.jpg
tags: [ruby]
comments: true
---
Ruby không ngừng ra các phiên bản mới cụ thể gần đây nhất 12/2019 Ruby đã ra phiên bản 2.7 Đây là phiên bản cuối cùng của phiên bản 2.x của Ruby trước khi ra phiên bản 3.0. Cùng nhìn lại xem phiên bản này có gì mới nhé :D 

# Enumerable#tally

Để đếm số phần tử trong mảng ta thường làm thủ công bằng các cách như sau :
```ruby
array
  .group_by { |v| v }
  .map { |k, v| [k, v.size] }
  .to_h
  
array
  .group_by { |v| v }
  .transform_values(&:size)

array.each_with_object(Hash.new(0)) { |v, h| h[v] += 1 }

```
Ở phiên bản 2.7 ta có thể dùng `tally`.

`tally` là method đếm các phần tử trong mảng rồi trả về dưới dạng hash có key : phần tử trong mảng, value là số lần xuất hiện.

Ví dụ :
```ruby
["A", "B", "C", "B", "A"].tally 
```
Kết quả 

```ruby
{"A"=>2, "B"=>2, "C"=>1}
```

# Enumerable#filter_map

`select` và `map` thường dùng kết hợp để chuyển đổi 1 mảng thành 1 mảng khác với các điều kiện.

VD: Chọn các số lẻ có trong mảng rồi trả về kết quả 1 mảng dưới dạng String ta có các cách như sau: 
```ruby
[1, 2, 3].select { |x| x.odd? }.map { |x| x.to_s }
[1, 2, 3].map { |x| x.to_s if x.odd? }.compact 
[1, 2, 3].each_with_object([]) { |x, m| m.push(x.to_s) if x.odd? }
```

`filter_map` đã giúp chúng ta giải quyết vde này 1 cách nhanh chóng, quá tiện lợi:

```ruby
[1, 2, 3].filter_map {|x| x.odd? ? x.to_s : nil } #=> ["1", "3"]

```


> `filler_map` không hẳn giống map + compact vì nó loại bỏ cả giá trị false, còn compact thì không, Compact chỉ loại bỏ nil



# Numbered Parameters

Nhìn vào ví dụ ta có 1 block với 1 tham số n:

```ruby
[1,2,3].each { |n| puts n }

```
|n| là tham số và ta phải define thì mới dùng được

Nếu |n| là 1 tham số mặc định, k cần define mà vẫn dùng dc thì dòng code của chúng ta sẽ ngắn gọn đi rất nhiều. 

Ruby 2.7 đã làm dc điều đó. Đoạn code trên có thể thay thế bằng :

```ruby
[1,2,3].each { puts _1 }
```

_1 là tham số đầu tiên, tương tự _n với tham số thứ n

Tham khảo thêm cách dùng với _n :

```ruby
# bad
names.map { |name| name.upcase }
# good
names.map(&:upcase)
# also good?
names.map { _1.upcase }
```

Với 2 tham số thì sẽ dùng ntn:

```ruby
hash = {
  key_1: "1",
  key_2: "2",
}

# bt sẽ là :
hash.map{|key, value| [key.to_s, value.to_i]}.to_h

# Sử dụng tham số mặc định :
hash.map { [_1.to_s, _2.to_i] }.to_h 
# => { "key_1" => 1, "key_2" => 2 }
```

# Array#intersection

Để tìm các phần tử có ở cả 2 mảng ta hay dùng :
```ruby
[ 1, 1, 3, 5 ] & [ 3, 2, 1 ]                 #=> [ 1, 3 ]
[ 'a', 'b', 'b', 'z' ] & [ 'a', 'b', 'c' ]   #=> [ 'a', 'b' ]
```

Giờ chúng ta có thể dùng `intersection` để trả về kết quả trùng nhau giữa 2 mảng

```ruby
[ 1, 1, 3, 5 ].intersection([ 3, 2, 1 ])                #=> [ 1, 3 ]
[ 'a', 'b', 'b', 'z' ].intersection([ 'a', 'b', 'c' ])  #=> [ 'a', 'b' ]
```


Nhớ lại 1 chút nếu làm việc vs 2 mảng ta có thể sử dụng các method sau :


| Short Version	 |Long version | Mục đích|
| -------- | -------- |  -------- | 
| &     | intersection     | Lấy về kết quả trùng nhau giữa 2 mảng|
|  I     | union     |Trả về mảng mới, k bao gồm các phần tử trùng lặp và giữ nguyên vị trí các phần tử trong mảng|
| –     | difference     |Lấy về kết quả khác nhau giữa 2 mảng|

# Argument forwarding syntax
Chúng ta có thể sử dụng syntax `(...)` để thêm toàn bộ tham số của method vào 1 method khác dc gọi bên trong.

VD:
```ruby
def add(a, b)
  a + b
end

def add_and_double(...)
  add(...) * 2
end

add_and_double(2, 3) #=> 10
```

# Pattern matching

Dùng `in` Chúng ta có thể so sánh pattern matching giống như regex
VD:

```ruby
[1,2,3] in [a,b,c] # true
[1,2,3] in [a]     # false
```

`[a,b,c]` đại diện cho mảng có 3 phần tử. ở đây check [1,2,3] có phải là mảng có 3 phần tử k. ta có thể thay thê a,b,c bằng c,d,f

Ngoài check trên array ta có thể check trên hash :

```ruby
{ a: 1, b: 2, c: [] } in { a: a, b: b, c: [] }
# true
p a
# 1

{ a: 1, b: 2, c: [] } in { a: a, b: b}
# false
```

# Gọi private method bằng self

Ở các phiên bản trước đó ta k thể gọi `self.private_method` mà chỉ có thể gọi `self.public_method` hoặc `self.protected_method`
Ở phiên bản 2.7 đã nâng cấp phạm vi truy cập của private : ta đã có thể gọi `self.private_method`
# Giao diện IRB được nâng cấp
IRB hỗ trợ highlight cú pháp. tự động thụt dòng khi code xuống dòng hỗ trợ auto completion rất tiện lợi khi code trực tiếp trên irb. So vs các phiên bản trc đó thì nó dc hỗ trợ về mặt giao diện rất nhiều nhưng vẫn đi sau thằng [Pry](https://cobaltbluemedia.com/pryrepl/)


Ref:

https://www.rubyguides.com/2019/12/ruby-2-7-new-features/

https://medium.com/rubyinside/whats-new-in-ruby-2-7-79c98b265502
