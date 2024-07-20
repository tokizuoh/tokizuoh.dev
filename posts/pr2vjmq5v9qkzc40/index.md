---
title: "ngrokを使ってJenkinsを試す"
date: 2020-08-26T22:51:22+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/jenkins.png"
tags:
- Jenkins
---
  
ngrokを使って、手軽にwebhookを利用したJenkinsのジョブを試す。  
  
<!--more-->  
  
## 用語説明  
  
*Jenkins*  
  
> Jenkins is a self-contained, open source automation server which can be used to automate all sorts of tasks related to building, testing, and delivering or deploying software.  
>   
> (from: [Jenkins User Documentation](https://www.jenkins.io/doc/))  
  
ビルドやテストなどを自動化するサーバー。  
  
　
  
*ngrok*  
  
> Ngrok exposes local servers behind NATs and firewalls to the public internet over secure tunnels.  
>  
> (from: [ngrok - secure introspectable tunnels to localhost](https://ngrok.com/product))
  
ローカルサーバーをPublicなURLに割り当てるサービス。  
  
## 作るもの  
GitHub運用リポジトリのmasterブランチへpushした際に、Jenkinsで設定した処理を走らせる。  
  
GitHubのwebhookを使うために、ngrokでローカルサーバーにPublicなURLを割り当てた。  
  
## 開発環境  
  
```
> java --version
java 14.0.2 2020-07-14

> jenkins --version
2.254
```
  
## Jenkins ジョブ設定  
  
0. インストール, セットアップ: [JenkinsでCI環境構築チュートリアル （Linux編・macOS編）](https://ics.media/entry/14273/#)  
1. 新規ジョブ作成  
2. GitHub project > Project url: GitHubのリポジトリURL  
3. ソースコード管理 > Git
4. ソースコード管理 > リポジトリURL: GitHubのリポジトリURL  
5. ビルドするブランチ > ブランチ指定子 (空欄はすべてを指定) > **/master**  
6. ビルド・トリガ > GitHub hook trigger for GITScm polling  
7. コンソール出力にタイムスタンプを追加する ※お好み  
8. ビルド > シェルの実行 > シェルスクリプト > **cat README.md** ※ masterブランチへのpush時に走らせたい処理  
  
## ngrok 設定  
  
0. インストール, セットアップ: [Setup - ngrok](https://dashboard.ngrok.com/get-started/setup)  
1. **ngrok http 8080**  
  
## GitHub 設定  
  
0. セットアップ: リポジトリ作成  
1. GitHubのリポジトリ > Settings > Webhooks > 「Add webhook」  
2. Payload URL: {**ngrok http 8080** で得たURL}/github-webhook/  
  
## Jenkins で処理を走らせる  
  
1. masterブランチでpush  
  
Jenkinsトップのコンソール出力から処理を実行した形跡が確認できる。  
  
## 参考  
  
- [Jenkins User Documentation](https://www.jenkins.io/doc/)  
- [JenkinsでCI環境構築チュートリアル （Linux編・macOS編） - ICS MEDIA](https://ics.media/entry/14273/#)  
- [ngrok - secure introspectable tunnels to localhost](https://ngrok.com/)  
- [【jenkins】GitHub pushをトリガーに処理連携 - memo & Attachment](https://www.machida92.com/entry/2018/01/25/220934)  
- [Jenkinが終了できない場合の対処 - pogin’s blog](http://pogin.hatenablog.com/entry/20141005/1412497694)  