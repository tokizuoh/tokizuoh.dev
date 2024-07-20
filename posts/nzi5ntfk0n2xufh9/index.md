---
title: "IPAファイルからビルドバージョンを一発で抽出する"
date: 2020-10-17T14:28:37+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/python.png"
tags:
- python
- iOS
---  
  
IPAファイルからXcodeのバージョンを一発で出したい。  
<!--more-->  
  
複数バージョンのXcodeを使用したCI/CD環境を作っていると、成果物のIPAファイルがどのXcodeでビルドされたか一見分からない。  
  
そこで、一発でIPAファイルからXcodeのバージョンを抽出できたら便利だと考えて実装した。  
  
※ 本記事の内容は[こちら](https://qiita.com/bricklife/items/8b7c9dc4f7ab164e738d)の記事を参考にさせていただきました。  
  
## GitHub
https://github.com/tokizuoh/burnet
  
## 開発環境
  
```bash
> python -V
Python 3.8.5
```
  
## コード  
  
```python
import os
import sys
import shutil
import os.path
import zipfile
import datetime
import xml.etree.ElementTree as ET


class ArgumentError(Exception):
    def __init__(self, message = "Wrong number of arguments."):
        self.message = message
        super().__init__(self.message)


class FileExtensionNotIpaError(Exception):
    def __init__(self, message = "File extension is not .ipa."):
        self.message = message
        super().__init__(self.message)


def generate_now_time_str():
    return datetime.datetime.now().strftime('%Y%m%d%H%M%S')


def extract_dtx_code_build(plist_path):
    tree = ET.parse(plist_path)
    root = tree.getroot()
    isKeyExists = False
    is_before_key_dtx_code_build = False
    version = ""
    for child in root[0]:
        if child.text == "DTXcodeBuild":
            is_before_key_dtx_code_build = True
            continue
        if is_before_key_dtx_code_build:
            version = child.text
            break
    return version


if __name__ == '__main__':
    # コマンドライン引数取得
    args = sys.argv
    if len(args) != 2:
        raise ArgumentError

    # コマンドライン引数の2つ目の末尾が.ipaであることを確認
    ipa_path = args[1]
    if (ext := (_ := os.path.splitext(ipa_path))[1]) != ".ipa":
        raise FileExtensionNotIpaError

    # ipaファイルの存在確認
    if not os.path.exists(ipa_path):
        raise FileNotFoundError(ipa_path)

    # ipaファイルをコピーし、zipに変換
    zip_file_path = "{}_{}.{}".format(ipa_path[:-4], generate_now_time_str(), "zip")
    shutil.copyfile(ipa_path, zip_file_path)
    
    # unzip
    with zipfile.ZipFile(zip_file_path) as ez:
        ez.extractall('./')
    
    # Info.plist(xml)からipaをビルドしたXcodeのバージョンを抽出
    plist_path = './Payload/{}.{}/Info.plist'.format(ipa_path[2:-4], "app")
    xcode_build_version = extract_dtx_code_build(plist_path)
    print(xcode_build_version)

    # .zip, unzipに展開されたPayloadを削除
    os.remove(zip_file_path)
    shutil.rmtree('./Payload')
```
  
　
  
```python
    if (ext := (_ := os.path.splitext(ipa_path))[1]) != ".ipa":
```
  
Python3.8から導入された `:=` を使ってみた。  
一行で代入と比較が出来るので便利だが、上記のように可読性を損ねるので注意したい。  
  
## 実行
```bash
> ls
Test20201016.ipa
main.py

> python main.py ./Test20201016.ipa
12A7300  # Xcode12.0.1 (12A7300)
```
  
## 参考
- [iOSアプリのビルドに使ったXcodeのバージョンを調べる方法 - Qiita](https://qiita.com/bricklife/items/8b7c9dc4f7ab164e738d)  
- [組み込み例外 — Python 3.9.0 ドキュメント](https://docs.python.org/ja/3/library/exceptions.html)  
- [PythonでZIPファイルを圧縮・解凍するzipfile | note.nkmk.me](https://note.nkmk.me/python-zipfile/)