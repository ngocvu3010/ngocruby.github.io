---
layout: post
title: Dính n commit trên 1 pull request
subtitle: git reset --soft, git reset --hard 
cover-img: 
tags: [git]
comments: true
---

##### Tái hiện trường hợp :


Giả sử có bạn đang code trên 1 nhánh mới tên test_git được checkout từ nhánh develop `git checkout -b test_git`

- Thêm text "commit A", thực hiện `git add . `,  `git commit -m "Commit A"`
-  Thêm text "commit B", thực hiện `git add . `,  `git commit -m "Commit B"`
-  Thêm text "commit C", thực hiện `git add . `,  `git commit -m "Commit C"`

Lúc này commit sẽ theo sơ đồ ntn:

`A - B - C (HEAD -> develop)`

A - B - C tương ứng vs 3 commit ta vừa tạo ở trên ứng vs nhánh ta đang làm việc là test_git
Sử dụng `git log --oneline` ta sẽ thấy tên các commit và mã SHA tương ứng vs commit

```
107f88c commit C
376419e commit B
b047857 commit A
2d40b74 Merge pull request #905 from ngoc-0914/bug/25074-Accounting => commit này ở develop
```

Hiện tại nội dung file sẽ là :

```
commit A
commit B
commit C
```

##### Xử lý

Có 3 cách để gộp 3 commit trên thành 1 commit 

**git reset --soft mã_SHA** : (ở đây mã SHA =   b047857)  Đưa HEAD về commit A, tức loại bỏ commit B, C nhưng vẫn giữ nguyên code. Code của B,C đã dc chuyển vào vùng Staging Area (tức đã coi như `git add`)
Khi bạn git log --oneline thì chỉ còn :

```
b047857 commit A
```

Vậy 3 commit sẽ gộp làm 1. Vì code B,C đã dc chuyển vào vùng Staging Area nên bạn phải `git commit --amend --no-edit` để commit nó vào commit A.

Nếu không làm như trên ta tiếp tục git commit -m "commit D" thì nhánh test_git sẽ có 2 commit, commit D sẽ chứa code của commit B,C cũ

**git reset mã_SHA**: Tương tự như git reset --soft có điều code B,C dc chuyển vào vùng Working directory (code B,C chưa git add .)  khi `git diff` bạn sẽ nhìn thấy :

```
commit B
commit C
```
bạn sẽ phải 

```
git add .
git commit --amend --no-edit
```

**git reset --hard mã_SHA**: Cái này hơi nguy hiểm nhé, nó sẽ loại bỏ code tại commit B, C và đưa HEAD về commit A. Nội dung 

```
commit B
commit C
```
sẽ mất mà chỉ còn
```
commit A
```

#### Đôi điều về HEAD

**HEAD** : vị trí commit hiện tại

**HEAD~1**  Tức sẽ lùi lại 1 commit so với commit hiện tại trên cùng nhánh

**HEAD~n** : Lùi lại n commit so vs commit hiện tại trên cùng nhánh
**HEAD~~** : sẽ giống với HEAD~2 
Thay vì chỉ định **mã SHA** ta có thể thay thế nó bằng **HEAD~n**

```
git reset --soft b047857
git reset --soft HEAD~2
git reset --soft HEAD~~
```
3 câu lệnh này tương đương

### Nghịch linh tinh xong muốn trở về code ban đầu ntn


Sau khi nghịch rối tung rối mù lên bạn muốn trở về code ban đầu, giữ nguyên 3 commit và code lúc đầu
ta dùng ORIG_HEAD

```
git reset --hard ORIG_HEAD
```


### Muốn quay lại 1 thời điểm bất kỳ thì làm thế nào


Giả sử mình đã làm rất nhiều thao tác, thêm code, rồi commit,reset các kiểu

Bạn có thể xem lịch sử bằng :

```
git reflog
```

```
107f88c HEAD@{0}: reset: moving to 107f88c
fc12c05 HEAD@{1}: commit (amend): commit C
107f88c HEAD@{2}: reset: moving to ORIG_HEAD
7f8a829 HEAD@{3}: commit (amend): commit A
b047857 HEAD@{4}: reset: moving to HEAD~~
107f88c HEAD@{5}: reset: moving to 107f88c
b047857 HEAD@{6}: reset: moving to b047857
107f88c HEAD@{7}: reset: moving to 107f88c
b047857 HEAD@{8}: reset: moving to b047857
107f88c HEAD@{9}: commit: commit C
376419e HEAD@{10}: commit: commit B
b047857 HEAD@{11}: commit: commit A
```


Muốn quay về vị trí bất kỳ bạn chỉ cần dùng các câu lệnh git reset trên sử dụng mã SHA ở đầu 

VD muốn quay về thời điểm commit amend của commit C:

```
git reset --soft fc12c05
```


#### Nếu đã commit thì sẽ k baoh lo bị mất code


Nếu đã commit code git sẽ bắt đầu theo dõi nó và gán cho nó 1 mã SHA cho commit đó. Sử dụng git log và git reflog để access vào những commit đó nên anh em yên tâm nhé :D


