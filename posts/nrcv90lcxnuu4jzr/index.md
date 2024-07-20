---
title: "Dockerの任意のimageを強制的に一括で消す"
date: 2020-09-24T23:26:46+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/docker_python.png"
tags:
- docker
- python
---
  
image増えがちなので一括で消したかった。  
<!--more-->  
テスト用で都度都度 `docker build` してたら、imageが多くなって邪魔だった。  
強制的に消しても良かったので、コードで一括に消せるようにした。  
コード実行時は、消したいイメージに含まれる文字列をコマンドライン引数で渡す。  
  
コマンドの実行は subprocess を用いた。  
  
## 開発環境  
  
```bash
> docker -v
Docker version 19.03.12, build 48a66213fe
  
> python -V
Python 3.7.5
```
  
## コード  
  
```python
import sys
import subprocess

def main():
    args = sys.argv
    
    if len(args) != 2:
        exit("INCORRECT INPUT")

    # awkで空白区切り出力はカンマ
    cmd = "docker images | grep " + args[1] + " | awk '{ print $1,$2 }'"  
    
    # パイプでコマンドをつなぐ場合は run が使えないため Popen を使用
    result = subprocess.Popen(cmd, shell=True, stdout=subprocess.PIPE)
    stdout, strerr = result.communicate()

    # 改行区切りでsplitした場合、配列の最後に空文字が残るため [:-1]
    docker_images = [i.replace(' ', ':') for i in stdout.decode('utf-8').split('\n')[:-1]]
    
    for docker_image in docker_images:
        subprocess.run(['docker', 'rmi', '-f', docker_image])

main()
```
  
## 実行  
  
```bash
python main.py {消したいimageの部分文字列}
```
  
## 見境なしにイメージを整理  
  
```bash
docker system prune -a -f
```
  
保有している image が全て捨てて良いものなら上記で事足りそう。  
  
## 参考  
- [subprocess --- サブプロセス管理](https://docs.python.org/ja/3.7/library/subprocess.html#subprocess.Popen)  
- [docker system prune | Docker Documentation](https://docs.docker.com/engine/reference/commandline/system_prune/)