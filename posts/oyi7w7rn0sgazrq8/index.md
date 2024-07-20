---
title: "DockerでJenkinsを構築"
date: 2020-09-21T21:33:04+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/docker_jenkins.png"
tags:
- docker
- jenkins
---
  
Docker上でJenkinsを構築する。リバースプロキシの設定が不要。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> docker --version
Docker version 19.03.12, build 48a66213fe
```
  
## イメージ  
  
1. [jenkins - Docker Hub](https://hub.docker.com/_/jenkins)  
2. [jenkins/jenkins - Docker Hub](https://hub.docker.com/r/jenkins/jenkins)
  
調べると2種類イメージがヒットするが、上のイメージは更新が停止しているため2を使用。
  
## コンテナ起動  
  
```bash
docker run -p 8080:8080 jenkins/jenkins
```
  
　
  
|オプション|内容           |備考                                                                    |
|:-------|:-------------|:----------------------------------------------------------------------|
|p       |ポートのマッピング|-p 8080:8080 （ホストマシンの8080ポートをコンテナの8080ポートにマップ）             |
  
## 参考  
- [jenkins - Docker Hub](https://hub.docker.com/_/jenkins)  
- [jenkins/jenkins - Docker Hub](https://hub.docker.com/r/jenkins/jenkins)