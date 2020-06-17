---
layout: post
title: Callback Trong Rails
subtitle:
cover-img: 
tags: [rails]
comments: true
---

# 1. Mở đầu 
`Callback` là một phương thức của `Active Record`, nó sẽ được gọi tới vào một thời điểm nào đó trong vòng đời của một đối tượng (Thêm, cập nhập, xóa đối tượng). `Callback` thường được dùng để thực thi các phương thức logic trước hoặc sau khi đối tượng có một sự thay đổi nào đó. 

Ví dụ:  Ta có màn hình nhập user name : "vu thi ngoc"
nhưng trong CSDL tên phải được lưu là viết hoa hết (VU THI NGOC) => Vậy phải làm ntn

B1: Vẫn cho người dùng nhập tùy thích

B2: Trước khi lưu vào CSDL ta cho tất cả các kí tự viết hoa hết => Cái này sử dụng Callback before_save
# 2. Tạo Callback
Vì `Callback` tác động trực tiếp đến các thao tác (create, update, destroy) liên quan đến đối tượng nên nó phải được khai báo trong model của đối tượng đó.. Bạn có thể viết một phương thức thông thường rồi sử dụng macro-style để khai báo nó như một `Callback`
```
class User < ApplicationRecord
  validates :login, :email, presence: true
 
  before_validation :ensure_login_has_a_value
 
  private
    def ensure_login_has_a_value
      if login.nil?
        self.login = email unless email.blank?
      end
    end
end
```
Ở đây ta kiểm tra đảm bảo rằng `logic` chứa giá trị giống như `email` trước khi validate. Ở đây ta sử dụng phương thức lớp macro-style để nhận 1 block. Ngoài sử dụng cách trên ta có thể viết cách khác 1 cách ngắn hơn dưới dạng block:
```

class User < ApplicationRecord
  validates :login, :email, presence: true
 
  before_create do
    self.name = login.capitalize if name.blank?
  end
end
```

# 3. Danh sách Callback
Đây là danh sách các `Callback` của Active Record, được sắp xếp theo thứ tự gọi đến trong quá trình hoạt động của một đối tượng
### 3.1 Tạo
* before_validation
* after_validation
* before_save
* around_save
* before_create
* around_create
* after_create
* after_save
* after_commit/after_rollback
### 3.2 Cập nhập
* before_validation
* after_validation
* before_save
* around_save
* before_update
* around_update
* after_update
* after_save
* after_commit/after_rollback
### 3.3 Xóa
* before_destroy
* around_destroy
* after_destroy
* after_commit/after_rollback

Trong đó `after_save` chạy sau khi `create` và `update`. Nếu chỉ muốn sau khi `create` ta có thể dùng `after_create` và sau khi `update` là `after_update`. 
###  3.4 after_initialize và after_find
Callback `after_initialize` được gọi khi đối tượng được khởi tạo hoặc được load từ database. Nó trở nên hữu dụng tránh việc ghi đè phương thức ` Active Record initialize`
`after_find` được gọi khi `Active Record` load 1 record từ database. `after_find` được gọi trước khi `after_initialize`  nếu cả 2 đều được định nghĩa
```
class User < ApplicationRecord
  after_initialize do |user|
    puts "You have initialized an object!"
  end
 
  after_find do |user|
    puts "You have found an object!"
  end
end
 
>> User.new
You have initialized an object!
=> #<User id: nil>
 
>> User.first
You have found an object!
You have initialized an object!
=> #<User id: 1>
```
# 4. Chạy Callback
Đây là các method sẽ chạy callback:
* create
* create!
* destroy
* destroy!
* destroy_all
* save
* save!
* save(validate: false)
* toggle!
* update_attribute
* update
* update!
* valid?

`after_find` sẽ được gọi khi các method find này được gọi:
* all
* first
* find
* find_by
* find_by_*
* find_by_*!
* find_by_sql
* last

`after_initialize` sẽ được gọi bất cứ khi nào 1 đối tượng mới được khởi tạo

# 5. Bỏ qua Callbacks
Giống với validation, Bạn có thể bỏ qua callbacks bằng cách sử dụng các method sau:
* decrement
* decrement_counter
* delete
* delete_all
* increment
* increment_counter
* toggle
* touch
* update_column
* update_columns
* update_all
* update_counters

Các method này nên được sử dụng thận trọng vì các validate dữ liệu hay các logic được lưu lại trong callback. Nên việc bỏ qua các callbacks này có thể dẫn đến dữ liệu không hợp lệ

# 6. Callbacks có điều kiện
Đôi khi không phải lúc nào ta cũng muốn sử dụng `callbacks` mà tùy vào từng trường hợp. Ta có thể sử dụng `:if` và `:unless`  cùng với `symbol`, `Proc` và `Array`.
### 6.1 Sử dụng `:if` và `:unless` với `Symbol`
```
class Order < ApplicationRecord
  before_save :normalize_card_number, if: :paid_with_card?
end
```
Trước khi save sẽ gọi callback `normalize_card_number` nếu `paid_with_card?` trả về `true`

### 6.2 Sử dụng `:if` và `:unless` với `Proc`
Ngoài sử dụng `symbol` ta có thể sử dụng `Proc`  với mục đích tương tự
```
class Order < ApplicationRecord
  before_save :normalize_card_number,
    if: Proc.new { |order| order.paid_with_card? }
end
```
### 6.3 Nhiều điều kiện cho Callbacks
Ta có thể dùng `:if` và `unless` cho cùng 1 callbacks:
```
class Comment < ApplicationRecord
  after_create :send_email_to_author, if: :author_wants_emails?,
    unless: Proc.new { |comment| comment.article.ignore_comments? }
end
```
# 7. Custom Callbacks
Rails cung cấp rất nhiều callback liên quan đến vòng đời của đối tượng như: create, update, destroy. Ví dụ: before_create, after_create, before_update, after_destroy... Chúng ta sử dụng chúng để viết và chạy code dựa trên các method đã được định nghĩa sẵn. Nhưng nếu chúng ta muốn callback cho 1 phương thức khác  create, update, save và destroy. Ví dụ: Ta có một model `Article` và muốn làm gì đó trước và sau khi `article` xuất bản. 
ActiveRecord cung cấp cho chúng ta 1 module `ActiveModel::Callbacks` cho phép chúng ta định nghĩa 1 callback sử dụng phương thức `define_model_callbacks`. 
```
class Article 
  extend ActiveModel::Callbacks
  
  define_model_callbacks :publish, :only => [:before, :after]

  before_publish :check_publishability
  after_publish  :notify_subscribers

  def publish
    run_callbacks :publish do
      puts "Publishing article..."
    end
  end

  private
  
  def check_publishability
    puts "Checking publishability rules..."
  end

  def notify_subscribers
    puts "Notifying subscribers..."
  end

end
```
Mặc định `define_model_callbacks` cung cấp 3 callbacks đó là:  before, after và around. Nhưng trong ví dụ trên, ta chỉ sử dụng `before` và `after`. Bằng cách sử dụng ` define_model_callbacks :publish, :only => [:after, :before]` ta sẽ có được 2 callback mới đó là `before_publish` và `after_publish`.
Kết quả của ví dụ trên:
```
>> article = Article.last
>> article.publish
Checking publishability rules...
Publishing article...
Notifying subscribers...
```

`Callback` mang lại nhiều lợi ích nhưng chúng ta không nên lạm dụng sử dụng nó. Chỉ sử dụng callback khi logic liên quan đến bản thân đối tượng. Điều này sẽ tránh việc đối tượng phải xử lý những vấn đề nằm ngoài phạm vi kiểm soát của chính nó. Hi vọng bài viết sẽ giúp bạn hiểu thêm về `Callback` và sử dụng nó một cách tối ưu.

### Nguồn tham khảo
http://guides.rubyonrails.org/active_record_callbacks.html
http://vinsol.com/blog/2014/07/07/custom-model-callbacks-in-rubyonrails/
