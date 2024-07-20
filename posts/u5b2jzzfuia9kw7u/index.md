---
title: "Docker Compose で立ち上げたコンテナ間の疎通を試す"
date: 2021-08-13T11:43:43+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/docker.png"
tags:
- docker
- network
---
  
昨日、[Linuxで動かしながら学ぶTCP/IPネットワーク入門](https://www.amazon.co.jp/Linux%E3%81%A7%E5%8B%95%E3%81%8B%E3%81%97%E3%81%AA%E3%81%8C%E3%82%89%E5%AD%A6%E3%81%B6TCP-IP%E3%83%8D%E3%83%83%E3%83%88%E3%83%AF%E3%83%BC%E3%82%AF%E5%85%A5%E9%96%80-%E3%82%82%E3%81%BF%E3%81%98%E3%81%82%E3%82%81-ebook/dp/B085BG8CH5) を読み終えた。  
  
<!--more-->  
  
今までネットワークは苦手だったが、具体的な通信方法についてハンズオン形式で取り組めたので身になった。  
今回は本で勉強したことを踏まえて、Docker Compose を使ってネットワーク周りを見ていく。  
  
## やること
  
- Docker Compose で立ち上げたコンテナ間の疎通確認
- Docker Compose を使わずに立ち上げたコンテナ間の疎通確認
  
## 開発環境  
  
```bash
> docker --version
Docker version 19.03.12, build 48a66213fe

> docker-compose --version
docker-compose version 1.27.2, build 18f557f9
```
  
## Docker Compose で立ち上げたコンテナ間の疎通確認
  
以下の内容を `docker-compose.yml` に追加。  
  
```yml
version: '3.8'
services:
    udp-server:
        build:
            context: ./us
            dockerfile: Dockerfile
        tty: true
        volumes:
            - ./us:/go/src
    udp-client:
        build:
            context: ./uc
            dockerfile: Dockerfile
        tty: true
        volumes:
            - ./uc:/go/src
```
  
　
  
以下の内容を `Dockerfile` に追加。  
  
```bash
FROM golang:1.16-alpine
WORKDIR /go/src

COPY ./ ./
RUN apk add --no-cache gcc musl-dev
```
  
　
  
`docker-compose up --build -d` を実行。  
  
　
  
コンテナの IP アドレスを確認。  
  
```bash
> docker exec 52b ifconfig
eth0      Link ...  HWaddr ...
          inet addr:192.168.176.3  Bcast: ...  Mask: ...
  
> docker exec e69 ifconfig
eth0      Link ...  HWaddr ...
          inet addr:192.168.176.2  Bcast: ...  Mask: ...
```
  
それぞれ、プライベートIPアドレスのクラスC (192.168.0.0 ~ 192.168.255.255) に割当られた。  
  
　
  
pingを飛ばす。  
  
```bash
> docker exec 52b  ping -c 3 192.168.176.2
PING 192.168.176.2 (192.168.176.2): 56 data bytes
64 bytes from 192.168.176.2: seq=0 ttl=64 time=0.118 ms
64 bytes from 192.168.176.2: seq=1 ttl=64 time=0.238 ms
64 bytes from 192.168.176.2: seq=2 ttl=64 time=0.174 ms

--- 192.168.176.2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.118/0.176/0.238 ms


> docker exec e69  ping -c 3 192.168.176.3
PING 192.168.176.3 (192.168.176.3): 56 data bytes
64 bytes from 192.168.176.3: seq=0 ttl=64 time=0.075 ms
64 bytes from 192.168.176.3: seq=1 ttl=64 time=0.309 ms
64 bytes from 192.168.176.3: seq=2 ttl=64 time=0.411 ms

--- 192.168.176.3 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.075/0.265/0.411 ms
```
  
コンテナ同士で疎通が出来た。  
  
## なぜプライベートIPアドレス間で疎通ができるのか
  
192.168.176.2 と 192.168.176.3 が同じ Docker ネットワーク内に存在するため、上記が可能となっている認識だが、手順を踏んで確認していく。
  
　
  
コンテナが属する Docker ネットワークを確認。  
  
```bash
> docker inspect --format='{{range .NetworkSettings.Networks}}{{.NetworkID}}{{end}}' db4
28d1810c62a94cd430fdae19dcd9a098e298870533ac4698b8edadaff66ad5d2
  
> docker inspect --format='{{range .NetworkSettings.Networks}}{{.NetworkID}}{{end}}' b9f
28d1810c62a94cd430fdae19dcd9a098e298870533ac4698b8edadaff66ad5d2
```
  
同じネットワークに属していることが分かる。  
  
　
  
Docker ネットワークの一覧を確認。  
  
```
> docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
de9eec0db02d        bridge              bridge              local
...
28d1810c62a9        rubellum_default    bridge              local
```
  
1 で確認した ID は rubellum_default という Name の Docker ネットワークであることが分かった。なお、docker run 実行時に指定がない限り、bridge の Docker ネットワークに接続される。2つのコンテナ起動時に指定はしなかったので、どちらも同じ bridge の Docker ネットワークに属するため、プライベートIPアドレス間で疎通ができた。
  
## Docker Compose を使わずに立ち上げたコンテナ間の疎通確認
  
比較として、Docker Compose を使わずに立ち上げたコンテナ間の疎通確認を行う。  
  
　
  
以下の内容を `Dockerfile` に追加。  
  
```bash
FROM golang:1.16-alpine
WORKDIR /go/src

COPY ./ ./
RUN apk add --no-cache gcc musl-dev
```
  
　
  
`docker build .` を実行。  
  
　
  
コンテナを起動。  
  
```
> docker run -itd --name "manual-udp-server" 9801d6f8b5ce
cdaaae4e91cd9ea10c636e254f87a3f622719ca9ed063051f87f7f8484cce7ef
  
> docker run -itd --name "manual-udp-client" 5473ff303be3
3ce12bfcd25a9b6c675af7b57ab577fd7526a4090aa5099e15a01dcf5b4a060e
```
  
　
  
コンテナの IP アドレスを確認。  
  
```bash
> docker exec cda ifconfig 
eth0      Link ...  HWaddr ...
          inet addr:172.17.0.2  Bcast: ...  Mask: ...
  
> docker exec 3ce ifconfig
eth0      Link ...  HWaddr ...
          inet addr:172.17.0.3  Bcast: ...  Mask: ...
```
  
それぞれ、プライベートIPアドレスのクラスB (172.16.0.0 ~ 172.31.255.255) に割当られた。  
  
　
  
pingを飛ばす。  
  
```bash
> docker exec cda ping -c 3 172.17.0.3
PING 172.17.0.3 (172.17.0.3): 56 data bytes
64 bytes from 172.17.0.3: seq=0 ttl=64 time=0.197 ms
64 bytes from 172.17.0.3: seq=1 ttl=64 time=0.326 ms
64 bytes from 172.17.0.3: seq=2 ttl=64 time=0.366 ms

--- 172.17.0.3 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.197/0.296/0.366 ms


> docker exec 3ce ping -c 3 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.078 ms
64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.770 ms
64 bytes from 172.17.0.2: seq=2 ttl=64 time=0.142 ms

--- 172.17.0.2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.078/0.330/0.770 ms
```
  
コンテナ同士で疎通が出来た。
  
## なぜプライベートIPアドレス間で疎通ができるのか
  
172.17.0.2 と 172.17.0.3 が同じ Docker ネットワーク内に存在するため、上記が可能となっている認識だが、手順を踏んで確認していく。  
  
　
  
コンテナが属する Docker ネットワークを確認。  
  
```bash
> docker inspect --format='{{range .NetworkSettings.Networks}}{{.NetworkID}}{{end}}' 3ce
de9eec0db02d06f0115cbc50575bfa97de04991ce89436fbb48da853d3b07feb
  
> docker inspect --format='{{range .NetworkSettings.Networks}}{{.NetworkID}}{{end}}' cda
de9eec0db02d06f0115cbc50575bfa97de04991ce89436fbb48da853d3b07feb
```
  
同じネットワークに属していることが分かる。  
  
　
  
Docker ネットワークの一覧を確認。  
  
```bash
> docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
de9eec0db02d        bridge              bridge              local
...
```
  
1 で確認した ID は bridge という Name の Docker ネットワークであることが分かった。 なお、docker run 実行時に指定がない限り、bridge の Docker ネットワークに接続される。 2つのコンテナ起動時に指定はしなかったので、どちらも同じ bridge の Docker ネットワークに属するため、プライベートIPアドレス間で疎通ができた。  
  
## おわりに
  
Docker Compose の使用に関わらず、コンテナ起動時に指定しない場合は bridge の Docker ネットワークに属することが分かった。  
  
## 参考  
  
- [inspect — Docker-docs-ja 19.03 ドキュメント](https://docs.docker.jp/engine/reference/commandline/inspect.html)  
  