---
title: "Jenkin を nginx でリバースプロキシする"
date: 2020-09-16T21:19:47+09:00
draft: true
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- jenkins
- nginx
---

## 作るもの  
Jenkin を nginx でリバースプロキシする  
  
## 開発環境  
  
Jenkins, nginx は Homebrew でインストール。  
  
```
> jenkins --version
2.254

> nginx -v
nginx version: nginx/1.19.2
```
  
## よく使うコマンド  
  
### 任意のポートで走っているプロセスを確認  
```bash
lsof -i:{ポート番号}  # lsof: list open files  
```
  
　
  
### Jenkins  
  
```bash
# 起動
brew services start jenkins
  
# 停止
brew services stop jenkins
  
# 再起動
brew services restart jenkins
```
  
　
  
### nginx  
  
```bash
# 起動
nginx

# 停止
nginx -s stop
```
  
## nginxのデフォルトポートを変える  
  
Jenkinsとnginxのデフォルトポートは共に *8080* なので、どちらかを変える必要がある。  
  
`/usr/local/etc/nginx/nginx.conf`  
（`/etc/` は `/usr/local/etc` と `/etc/` があるので注意）  
  
```conf
server {
    listen       80;
}
```
  

  
## 参考  
