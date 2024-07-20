---
title: "Jenkins on Amazon EC2 (Ubuntu)"
date: 2020-08-30T21:54:04+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/aws_jenkins.png"
tags:
- aws  
- jenkins  
---
インスタンス作成からJenkinsのアクセス制限設定まで。  
<!--more-->  
  
## 注意！  
当サイトの御利用につき、何らかのトラブルや損失・損害等につきましては一切責任を問わないものとします。  
  
当サイトが紹介しているウェブサイトやソフトウェアの合法性、正確性、道徳性、最新性、適切性、著作権の許諾や有無など、その内容については一切の保証を致しかねます。  
  
## インスタンス作成  
  
無料枠AMIの **Ubuntu Server 18.04 LTS (HVM)** を使用。  
  
セキュリティグループのインバウンドルールにJenkinsのデフォルトポート8080を追加。  
  
> Runs Jenkins listener on port $HTTP_PORT using standard http protocol. The default is port 8080.  
>  
> (from [Starting and Accessing Jenkins - Jenkins - Jenkins Wiki](https://wiki.jenkins.io/display/JENKINS//Starting+and+Accessing+Jenkins))  
  
## Jenkins インストール  
  
基本は「[How to Install Jenkins on Ubuntu 18.04 | Linuxize](https://linuxize.com/post/how-to-install-jenkins-on-ubuntu-18-04/)」の通りに実行。  
  
**Administrator password** の入力が求められた際に、指定されたファイルが存在しなかった。(権限周りで上手く行っていないみたい)  
  
「[amazon ec2 - What is the default Jenkins password? - Stack Overflow](https://stackoverflow.com/questions/15227305/what-is-the-default-jenkins-password)」の回答の通り実行するとJenkinsに入ることができる。  
  
ここの数秒で、URLさえ知ってれば誰でもJenkinsに入ることが出来るので注意したい。  
  
## Jenkins アクセス制限設定  
初期状態はアクセス制限が設定されていない。  
  
1. 「グローバルセキュリティの設定」 > 「ユーザー情報」 > 「Jenkinsのユーザーデータベース」 > 「ユーザーにサインアップを許可」を有効に
2. ログアウト  
3. ユーザー作成  
4. ログイン  
5. 1.の設定を無効に  
  
以降は、Jenkinsにアクセスするためにログインが必要となる。  
  
## 参考  
  
- [Starting and Accessing Jenkins - Jenkins - Jenkins Wiki](https://wiki.jenkins.io/display/JENKINS//Starting+and+Accessing+Jenkins)  
- [How to Install Jenkins on Ubuntu 18.04 | Linuxize](https://linuxize.com/post/how-to-install-jenkins-on-ubuntu-18-04/)  
- [amazon ec2 - What is the default Jenkins password? - Stack Overflow](https://stackoverflow.com/questions/15227305/what-is-the-default-jenkins-password)  