---
layout: post
title: Set up Ruby on Rails Development Environment
subtitle:
cover-img: 
tags: [rails]
comments: true
---

# Install RVM and Ruby
RVM- Ruby Version Manager, which means managing the Ruby version, so we can install multiple Ruby versions at the same time.

You can install RVM by following https://github.com/rvm/ubuntu_rvm

run
```
rvm version
```
to check we have installed it successfully

After installing rvm, we will install ruby:

```
rvm install ruby-2.7.1

#check ruby version
ruby -v
```

then Setting  the shell as Login Shell in terminal. The settings can be found in

```
Open Terminal: Menu > Edit > Profile Preference > Command Tab > Choose Run command as a login shell
```

# Install Rails
With Rails we need the JavaScript Runtime to compile the Rails compile asset pipeline, before installing Rails you need to install Nodejs and Yarn


```
sudo apt install gcc g++ make
							
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
						
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list							
sudo apt update	

sudo apt install yarn nodejs
```

Check to see if the installation has been successful
```
node --version							
yarn --version
```

Now we can install Rails

```
gem install rails
```
After installation, check Rails version
```
rails --version							
```

# Install Mysql
Install by following by https://linuxize.com/post/how-to-install-mysql-on-ubuntu-18-04/

# Install GIT

Create account in https://github.com/

Install it 
```
sudo apt-get install git-core		
```

Config git
```
git config --global color.ui true

git config --global user.name ""Your Name""

git config --global user.email newuser@example.com
```

Your email should be the same as your Github account. You may set it differently, but it will introduce some additional work.

You should use ssh-key to authentication each time you pull from or push to Github.
```
ssh-keygen -t rsa -b 4096 -C "YOUR@EMAIL.com"
```
The next step is to take the newly generated SSH key and add it to your Github account. 
You want to copy and paste the output of the following command and paste it here.
```
cat ~/.ssh/id_rsa.pub

```
Once you've done this, you can check and see if it worked:
```
ssh -T git@github.com
```
You should get a message like this:
```
Hi excid3! You've successfully authenticated, but GitHub does not provide shell access.
```

# Install Sublime text
Sublime Text differs from other IDEs in its lightness, flexibility in customization, and offers many convenient features when programming. I suggest you use this software to code

Install it by flowing https://tecadmin.net/install-sublime-text-editor-on-ubuntu/

After installing the sublime text, you should set up the code to standard, help avoid the wrong convention

Open Sublime Text choose Preferences -> Settings and paste setting into file

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

If you use Visual Studio :

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

