---
title: "シャニマスでGraphQL入門 ①データ生成編"
date: 2022-01-15T17:45:22+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/docker_postgresql.png"
tags:
- docker
- postgresql
---
  
GraphQL、軽くドキュメントやGitHubのAPIを利用したものの、実際に作りたい欲が出てきたので作る。  
本記事はGraphQLで使用するデータを作る。  
  
<!--more-->  
  
---

※ 本記事は [作って学ぶGraphQL。gqlgenを用いて鉄道データ検索API開発入門](https://future-architect.github.io/articles/20200609/) を参考にしています。

---
  
## 開発環境  
  
```bash
> docker --version
Docker version 20.10.8, build 3967b7d

> docker-compose --version
docker-compose version 1.29.2, build 5becea4c
```
  
GitHubリポジトリ: [tokizuoh/faaaar](https://github.com/tokizuoh/faaaar)
  
## つくったもの
  
CSVファイルをPostgresSQLにインポート。  
　
  
これを、
  
```bash
> head -n 6 postgresql/init/idol_list.csv
id,name,age,height,birth_place,birth_day,blood_type,unit
1,櫻木 真乃,16,155,東京都,4/25,A,イルミネーションスターズ
2,八宮 めぐる,16,157,東京都,7/22,O,イルミネーションスターズ
3,風野 灯織,15,154,東京都,3/4,A,イルミネーションスターズ
4,月岡 恋鐘,19,165,長崎県,2/25,B,アンティーカ
5,幽谷 霧子,17,160,青森県,9/23,AB,アンティーカ
```
  
　
  
こうした。  
  
```
root@a12612163c44:/# PGPASSWORD=postgres psql -h localhost -p 5432 -U postgres -d postgres -c 'select * from idol limit 5';
 id |    name     | age | height | birth_place | birth_day | blood_type |           unit           
----+-------------+-----+--------+-------------+-----------+------------+--------------------------
  1 | 櫻木 真乃   |  16 |    155 | 東京都      | 4/25      | A          | イルミネーションスターズ
  2 | 八宮 めぐる |  16 |    157 | 東京都      | 7/22      | O          | イルミネーションスターズ
  3 | 風野 灯織   |  15 |    154 | 東京都      | 3/4       | A          | イルミネーションスターズ
  4 | 月岡 恋鐘   |  19 |    165 | 長崎県      | 2/25      | B          | アンティーカ
  5 | 幽谷 霧子   |  17 |    160 | 青森県      | 9/23      | AB         | アンティーカ
(5 rows)
```
  
　
  
コード: [tokizuoh/faaaar/releases/tag/0.1](https://github.com/tokizuoh/faaaar/releases/tag/0.1)
  
## 手順
  
1. CSVファイルを作る
2. CSVファイルをPostgresSQLにインポートする
  
## 1. CSVファイルを作る
  
手作業。  
[サイト](https://shinycolors.idolmaster.jp/) から手入力。  
  
## 2. CSVファイルをPostgresSQLにインポートする
  
[作って学ぶGraphQL。gqlgenを用いて鉄道データ検索API開発入門](https://future-architect.github.io/articles/20200609/) を参照。  
  
起動時に実行されるSQLファイルが実行時エラーになると、コンテナが起動されずエラーも標準エラー出力として出力されない。  
とても不便なので別記事で対応策を書く。 (書いたらここにリンクを貼る) 
  
## 参考  
  
- [アイドルマスター シャイニーカラーズ(シャニマス) | バンダイナムコエンターテインメント公式サイト](https://shinycolors.idolmaster.jp/)  
- [作って学ぶGraphQL。gqlgenを用いて鉄道データ検索API開発入門#PostgreSQLにデータを登録する | フューチャー技術ブログ](https://future-architect.github.io/articles/20200609/#PostgreSQL%E3%81%AB%E3%83%87%E3%83%BC%E3%82%BF%E3%82%92%E7%99%BB%E9%8C%B2%E3%81%99%E3%82%8B)  
  