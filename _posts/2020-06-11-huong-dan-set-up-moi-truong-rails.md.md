---
layout: post
title: Hướng dẫn cài đặt môi trường để code Ruby on Rails
subtitle:
cover-img: /assets/img/path.jpg
tags: [books, test]
---


Để bắt đầu code Ruby on Rails mình suggest cài đặt các bước như sau: 

Những thứ cần cài đó là :

* Ubuntu 16.04 LTS							
* RVM - Ruby Version Manager							
* Ruby							
* Rails							
* MySQL 							
* GIT							
* Phần mềm Sublime Text : Khuyến khích các bạn dùng sublime text để code				


## Cài RVM và Ruby (Có thể dùng rbenv cũng dc)							
RVM viết tắt của từ  Ruby Version Manager nghĩa là quản lý version Ruby, giúp chúng ta có thể cài nhiều bản ruby cùng 1 lúc							

Trên ubutun ta có thể cài theo hướng dẫn :							
https://github.com/rvm/ubuntu_rvm			

Chạy rvm version để kiểm tra chúng ta đã cài đặt thành công		

Sau khi cài xong rvm ta cài ruby:

```
rvm install ruby-2.7.1  => 2.7.1: version ruby muốn cài, bạn có thể cài nhiều bản ruby version khác nhau

rvm use 2.4.2 --default => --default là set version ruby mặc định trong máy bạn, ở đây 2.4.2 là ruby version mặc định

ruby -v => check xem ruby version hiện tại là bao nhiêu. Tùy từng dự án sẽ sử dụng ruby version khác nhau
```						
							
Sử dụng RVM để cài sẽ có trường hợp bạn bật terminal lên ruby nó k nhận : ruby -v nó ra not found 							
							
Giải quyết bằng cách  :
https://askubuntu.com/questions/624610/ruby-command-not-recognized-after-installing-it-with-rvm							

*Solution 1*: Using ~/.bashrc file

Open your ~/.bashrc file and put these two lines (or last one) there.

 Load RVM into a shell session *as a function*
 ```
[[ -s ""$HOME/.rvm/scripts/rvm"" ]] && source ""$HOME/.rvm/scripts/rvm""
```
Solution 2: Make the regular shell session as login shell

Or you can turn on your virtual terminal's preference to consider the shell as Login Shell. The settings can be found in 
```
Gnome Terminal: Menu > Edit > Profile Preference > Command Tab > Run command as a login shell

Mate Terminal: Menu > Edit > Profile Preference > Title & Command Tab > Run command as a login shell

Xfce4 Terminal: Menu > Edit > Preference > General Tab > Run command as login shell"
```
## Cài đặt Rails

Với Rails chúng ta cần JavaScript Runtime để compile asset pipeline của Rails, trước khi cài Rails các bạn cần cài thêm Nodejs và Yarn 							
```
sudo apt install gcc g++ make
							
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
						
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list							
sudo apt update	

sudo apt install yarn nodejs
```
							
check xem đã cài thành công chưa bằng cách 	
```
node --version							
yarn --version
```

Giờ chúng ta có thể cài đặt Rails
```
gem install rails
```

Sau khi cài đặt hoàn tất check Rails version	
```
rails --version							
```

## Cài đặt Mysql							
Sử dụng MySQL							
Cài đặt theo hướng dẫn : https://linuxize.com/post/how-to-install-mysql-on-ubuntu-18-04/							
## Cài đặt git		

Tạo tài khoản trên https://github.com/							
Cài đặt git :
```
sudo apt-get install git-core		
```

Config git:	

```
git config --global color.ui true

git config --global user.name ""Your Name""

git config --global user.email newuser@example.com
```

newuser@example.com: email tài khoản github bạn đăng ký						
							
Vậy ta đã cài đặt git và config thành công rồi. bạn có thể kéo code về sử dụng https : bằng cách git clone `https://github.com/project_minh_muon_clone.git`	

Nếu sử dụng https mỗi lần đẩy code lên github bạn sẽ phải nhập user name và password. để giảm thao tác đó chúng ta sẽ sử dụng ssh		

Connect máy mình vs github thông qua ssh tham khảo thêm tại : https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh	
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"							
cat ~/.ssh/id_rsa.pub					
```

Sau đó copy kết quả của dòng lệnh trên, mở github vào link setting ssh https://github.com/settings/keys . 
Tạo mới ssh key, điền tên và paste kết quả dòng lệnh vừa rồi							
Vậy là đã xong. check bạn đã kết nối thành công bằng cách `ssh -T git@github.com`

							
Nếu hiện Hi excid3! You've successfully authenticated, but GitHub does not provide shell access. là đã kết nối github bằng ssh thành công rồi đó							
Giờ ta có thể thực hiện git clone bằng ssh : 
```
git clone git@github.com:project_minh_muon_clone.git	
```
## Cài đặt sublime text 

Sublime Text khác với các IDE khác ở đặc điểm nhẹ, linh hoạt trong tùy biến và cung cấp nhiều tính năng rất tiện lợi khi lập trình. Mình suggest các bạn dùng phần mềm này để code	

Hướng dẫn cài đặt  https://tecadmin.net/install-sublime-text-editor-on-ubuntu/	

Sau khi cài đặt sublime text xong bạn setting để code cho chuẩn, đỡ bị sai convention nhé							

Các bạn tham khảo setting của mình:	

```
{
        ""auto_complete"": true,
        ""auto_complete_commit_on_tab"": true,
        ""copy_with_empty_selection"": true,
        ""ensure_newline_at_eof_on_save"": true,
        ""font_size"": 12,
        ""highlight_line"": true,
        ""ignored_packages"":
        [
                ""All Autocomplete"",
                ""BracketHighlighter"",
                ""FileDiffs"",
                ""Git blame"",
                ""Sass"",
                ""Trimmer"",
                ""TypeScript"",
                ""Vintage"",
                ""WakaTime""
        ],
        ""index_files"": true,
        ""rulers"":
        [
                100
        ],
        ""show_encoding"": true,
        ""show_line_endings"": true,
        ""tab_size"": 2,
        ""translate_tabs_to_spaces"": true,
        ""trim_trailing_white_space_on_save"": true,
        ""word_wrap"": true,
        ""wrap_width"": 80
}
```

Đối với  Visual Studio:

```
{
    "editor.tabSize": 2,
    "editor.renderWhitespace": "all",
    "editor.rulers": [80],
    "files.insertFinalNewline": true,
    "files.trimFinalNewlines": true,
    "files.trimTrailingWhitespace": true,
    "editor.stablePeek": true,
    "editor.gotoLocation.multiple": "goto",
    "references.preferredLocation": "view",
    "git.autofetch": true,
    "diffEditor.ignoreTrimWhitespace": true,
    "window.zoomLevel": -1,
    "window.titleBarStyle": "custom",
    "explorer.confirmDelete": false,
    "search.location": "panel"
}
```
Giờ thì quẩy code Rails được rồi đó :v							
							
							
							
							
Ref : https://www.howtoforge.com/tutorial/ubuntu-ruby-on-rails/					
