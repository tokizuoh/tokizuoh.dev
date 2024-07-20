---
title: "Docker上のJenkinsをnginxでリバースプロキシする"
date: 2020-09-20T22:57:43+09:00
draft: true
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- docker
- jenkins
- nginx
---
  
Docker上で起動しているJenkinsをnginxでリバースプロキシして、バインドマウントでホストからJenkinsに繋げる。（カタカナ多い）
  
<!--more-->  
  
## リバースプロキシとは  
クライアントとサーバーの間に入り、サーバーの応答を代理し中継する機能。  
性能向上や負荷分散、システム構成の自由度向上に利用される。  
  
## 構成
  
## 開発環境  
  
```bash
> docker --version
Docker version 19.03.12, build 48a66213fe

> docker image inspect nginx
..., "NGINX_VERSION=1.19.2", ...
```
  
## Dockerfile  
  
```docker
[wip]
```
  
#### ビルド  
  
```bash
docker build . -t {image-name}
```
  
　
  
|オプション|内容        |備考                             |
|:-------|:----------|:--------------------------------|
|t       |名前とタグ付け|{image-name}, {image-name}:{tag}|
  
　
  
#### コンテナ起動後に潜る  
  
```bash
docker run -it {image-name}  
```
  
　
  
|オプション|内容|備考|
|--------|--------------------------------------------------|:----------------------------------------------------------------------|
|i       |(=interactive)Keep STDIN open even if not attached|このオプションを付与しない場合、矢印が効かないなどの現象が起こる|
|t       |(=tty, teletypewriter)Allocate a pseudo-TTY       |このオプションを付与しない場合、コマンドの出力が表示されないなどの現象が起こる|
  
　
  
#### apk add --no-cache
  
```bash
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
revpro-1            latest              f276e1f767a2        50 seconds ago      8.76MB
revpro-2            latest              efeba2b4e783        3 seconds ago       8.76MB
```
  
revpro2が `--no-cache` ありでビルドしたイメージ。速い。  
  
## コンテナの起動を継続させたい  
  
例えば、ホストからコンテナへディレクトリを追加する際 (ADD) に、コンテナに潜って正常に追加されたか確認したい場合がある。  
  
```dockerfile
FROM alpine:3.12

RUN apk update
RUN apk add --no-cache nginx

ADD app /app
```
  
上記のようなDockerfileだと、コンテナ起動後にすぐに停止してしまう。  
  
```
docker run -itd {image} 
```
  
コンテナ起動時に`-itd (-i -t -d)` を付与する。  
  
|オプション|内容|備考|
|-|---------------------|:----------------------------------------------------------------------|
|i|コンテナのSTDINにアタッチ|-                                                                      |
|t|疑似ターミナルの割当     |-                                                                      |
|d|デタッチド             |コンテナをバックグラウンドで起動。指定しないとターミナルがコンテナのログに専有される。|
  
## 参考  
- [run — Docker-docs-ja 17.06 ドキュメント](https://docs.docker.jp/engine/reference/commandline/run.html)  