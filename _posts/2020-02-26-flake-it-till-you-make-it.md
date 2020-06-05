---
layout: post
title: Tạo một custom validate trong Rails
subtitle: Custom Validators in Ruby on Rails
cover-img: /assets/img/path.jpg
tags: [books, test]
---

### Tại sao lại sử dụng validate?
Validation (Kiểm chứng) được sử dụng để đảm bảo rằng chỉ có dữ liệu hợp lệ được lưu vào cơ sở dữ liệu của bạn. Ví dụ để đảm bảo rằng mỗi người sử dụng cung cấp một địa chỉ email và địa chỉ gửi thư hợp lệ. Có nhiều cách để xác nhận dữ liệu trước khi nó được lưu vào cơ sở dữ liệu như là client-side validations (xác thực ở client), controller-level validations (xác thực ở controller), and model-level validations (xác thực ở model).

- Client-side validations: xác thực phía client có thể hữu ích nhưng không đáng tin cậy nếu được sử dụng một mình. Nếu chúng được triển khai sử dụng JavaScript, họ có thể được bỏ qua nếu JavaScript bị tắt trong trình duyệt của người dùng. Tuy nhiên, nếu kết hợp với các kỹ thuật khác, xác thực ở client có thể là một cách thuận tiện để cung cấp cho người dùng với thông tin phản hồi ngay lập tức khi họ sử dụng trang web của bạn.

- Controller-level validations: có thể sử dụng nhưng thường trở nên cồng kềnh và khó kiểm tra và bảo trì.

- Model-level validations: là cách tốt nhất để đảm bảo rằng chỉ có dữ liệu hợp lệ được lưu vào cơ sở dữ liệu của bạn, thuận tiện để kiểm tra và duy trì. Rails làm cho các validation của model trở nên dễ sử dụng, cung cấp các helper cho các nhu cầu thông thường, và cho phép bạn tạo ra các phương pháp xác thực của riêng bạn

Trước khi lưu 1 đói tượng, Rails sẽ chạy kiểm dữ liệu bạn tạo. Nếu những validate xảy ra bất kì lỗi nào thì Rails sẽ không lưu đối tượng, Chúng ta được biết các validation được hỗ trợ của rails. ví dụ đơn giản:

```ruby
class Person < ApplicationRecord
  validates :name, presence: true
end

p = Person.new
# => #<Person id: nil, name: nil>
p.errors.messages
# => {}

p.valid?
# => false
p.errors.messages
# => {name:["can't be blank"]}

p = Person.create
# => #<Person id: nil, name: nil>
p.errors.messages
# => {name:["can't be blank"]}

p.save
# => false

p.save!
# => ActiveRecord::RecordInvalid: Validation failed: Name can't be blank

Person.create!
# => ActiveRecord::RecordInvalid: Validation failed: Name can't be blank
```

#### Các method kích hoạt method validate và chỉ lưu khi đối tượng hợp lệ:
create

create!

save

save!

update

update!

valid?

#### Những method sau sẽ bỏ qua validate và lưu đối tượng vào database:
decrement!

decrement_counter

increment!

increment_counter

toggle!

touch

update_all

update_attribute

update_column

update_columns

update_counters

save(validate: false)

Câu hỏi đặt ra là làm sao có thể viết được validate ngoài những validate được hỗ trợ sẵn??? Bài viết chia sẻ cách tạo 1 validate thông thường và tạo validate có thể dùng chung cho nhiều class model khác nhau

### Tạo 1 custom methods cho 1 model
Bạn có thể tạo ra các method kiểm tra và thêm các message khi dữ liệu không hợp lệ.

```ruby
class Invoice < ApplicationRecord
  validate :expiration_date_cannot_be_in_the_past,
    :discount_cannot_be_greater_than_total_value

  def expiration_date_cannot_be_in_the_past
    if expiration_date.present? && expiration_date < Date.today
      errors.add(:expiration_date, "can't be in the past")
    end
  end

  def discount_cannot_be_greater_than_total_value
    if discount > total_value
      errors.add(:discount, "can't be greater than total value")
    end
  end
end
```

Theo mặc định validate sẽ chạy khi bạn gọi valid? hoặc lưu đối tượng. Nhưng nó có thể kiểm soát bằng cách cho thêm tùy chọn vào phương thức validate: on: option. ví dụ: on: :create hoặc on: :update

```ruby
class Invoice < ApplicationRecord
  validate :active_customer, on: :create

  def active_customer
    errors.add(:customer_id, "is not active") unless customer.active?
  end
end
```

### Tạo 1 validate dùng chung cho nhiều model (Custom validator)

Giả sử chúng ta muốn tạo một validate:

```ruby
# app/models/contact_ticket.rb
class ContactTicket < ActiveRecord::Base
  validates :name, :my_email_field, :content,  presence: true

  validates :my_email_field, email: true # <-- custom email validator

  # ...

end
```
Để sử dụng email vaidator trong Active Record chúng ta cần phải tạo 1 lớp EmailValidator. Một câu hỏi đặt ra là nơi để giữ cho các lớp validator? Nơi tốt để lưu trữ chúng là thư mục mà chúng ta cần phải tạo ra bằng tay. Để tạo thư mục này có thể nhìn thấy rails có thể load được, chúng ta cần phải thêm class đó vào application.rb

```ruby
# config/application.rb

    # ...

    # add custom validators path
    config.autoload_paths += %W["#{config.root}/app/validators/"]
```

Giờ chúng ta có thể tạo class EmailValidator trong thư mục app/validator


```ruby
# app/validators/email_validator.rb

class EmailValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    unless value =~ /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i
      record.errors[attribute] << (options[:message] || "wrong email address")
    end
  end
end
```

EmailValidator được kế thừa từ ActiveModel::EachValidator. Ta có thể sử dụng EmailValidator cho nhiều các thuộc tính và nhiều class model khác nhau, ví dụ:

```ruby
validates :email_1, email: true
validates :email_2, email: true
```

Hi vọng bài viết sẽ giúp ích cho bạn, giúp bạn tạo và kiểm soát dữ liệu 1 cách dễ dàng

