---
title: "同一ファイル内の差分のcommitを分割させる"
date: 2020-11-22T09:56:57+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/git.png"
tags:
- git
---
  
commitは細かく分けたい。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> git --version
git version 2.24.3 (Apple Git-128)
```
  
## 同一ファイル内の差分のcommitを分割させる  
  
例えば以下のような差分があり、`hoge()`内の差分と`main()`内の差分のcommitを分ける場合を考える。  
```bash
> git diff   
...
 def hoge():
+    # hoge
     print("hoge")
 
 def main():
+    # main
     hoge()
```
  
　
  
そこで `-p` オプションを使う。  
  
```bash
> git add -p
...
 def hoge():
+    # hoge
     print("hoge")
 
 def main():
+    # main
     hoge()
...
\ No newline at end of file
(1/1) Stage this hunk [y,n,q,a,d,s,e,?]? e # ここでeを選択
  
# vimが立ち上がるので、今commitしたい差分以外の+行を削除する（dd）
# Manual hunk edit mode -- see bottom for a quick guide.
@@ -1,7 +1,9 @@
 def hoge():
+    # hoge
     print("hoge")

 def main():
+    # main
     hoge()

 main()
\ No newline at end of file
# ---
# To remove '-' lines, make them ' ' lines (context).
# To remove '+' lines, delete them.
# Lines starting with # will be removed.
#
# If the patch applies cleanly, the edited hunk will immediately be
# marked for staging.
# If it does not apply cleanly, you will be given an opportunity to
# edit again.  If all lines of the hunk are removed, then the edit is
# aborted and the hunk is left unchanged.
```
  
pはpatchの略。
  
## 参考  
- [Git - git-add Documentation](https://git-scm.com/docs/git-add)
