---
layout: post
title: Xử lý log production dùng LogRotate
subtitle:
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [test]
comments: true
---


Khi triển khai ứng dụng lên production, một vấn đề chúng ta phải quan tâm đó là log

Làm thế nào để quản lý file log cho tốt, dễ dàng tìm kiếm khi gặp vấn đề, file log k bị phình to chiếm nhiều bộ nhớ của server

Ý tưởng ta mỗi ngày ta sẽ ghi ra 1 file log sau đó nén chúng lại để tiết kiệm không gian lưu trữ cũng như dễ dàng cho việc quản lý

File log của bạn ngày 1 nặng đặc biệt nếu hệ thống có nhiều người truy cập. Hết dung lượng trên máy chủ hệ thống sẽ k hoạt động dc nữa

Để thực hiện dc ý tưởng trên ta sẽ dùng logrotate
hoho


This is a demo post to show you how to write blog posts with markdown.  I strongly encourage you to [take 5 minutes to learn how to write in markdown](https://markdowntutorial.com/) - it'll teach you how to transform regular text into bold/italics/headings/tables/etc.

**Here is some bold text**

## Here is a secondary heading

Here's a useless table:

| Number | Next number | Previous number |
| :------ |:--- | :--- |
| Five | Six | Four |
| Ten | Eleven | Nine |
| Seven | Eight | Six |
| Two | Three | One |


How about a yummy crepe?

![Crepe](https://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg)

It can also be centered!

![Crepe](https://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg){: .mx-auto.d-block :}

Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.
