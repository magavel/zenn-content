---
title: "「MySQLに割り当てられているメモリ量を確認して」と言われたときに見る記事"
emoji: "🐬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["MySQL", "Linux"]
published: false
---

実務でDBのメモリ設定を確認することがあったので、手順を残しておきます。
メモリの設定方法はカバーしていないので、設定が目的の場合は他を参照してください。

# ざっくり言うと
- MySQLのメモリ設定はクエリを流して調べることができる
- 一般的な環境の場合はinnodb_buffer_pool_sizeの値を確認する
- MySQLのメモリに関する設定は色々種類があって、使っているバージョンや内部エンジンによっても確認する項目が変わるから注意が必要

# 前提条件
1. MySQLのストレージエンジンがInnoDBであること（確認方法は後述します）
2. MySQL全体で使用されるメモリの確認が目的であること

# 確認手順

## 【手順1】ストレージエンジンの種類を確認する
MySQLは使用しているストレージエンジンによってメモリの使い方や確認項目が変わってきます。

今回は代表的なInnoDBを対象としているため、下記クエリを実行してストレージエンジンがInnoDBであることを確認してください。

```sql:mysql
SHOW TABLE STATUS FROM データベース名;
```

**各テーブルのEngineカラムの値がInnoDBになっていればOKです。**

※ストレージエンジンとは、
> SQL文を受け取って結果を返すデータベースエンジン部分　⇨ つまりテーブルにデータを書き込んだり読みだしたりするプログラム

https://qiita.com/ishishow/items/280a9d049b8f7bcbc14a

## 【手順2】グローバルバッファのメモリ量を確認する
手順1でストレージエンジンがInnoDBであることを確認できたら、INNODB_BUFFER_POOL_SIZEの値を確認します。

```sql:mysql
SELECT @@GLOBAL.INNODB_BUFFER_POOL_SIZE/1024/1024 as メモリサイズ（単位：MB）;
```

**このINNODB_BUFFER_POOL_SIZEの値がMySQL全体で使用できるメモリ量です。**

INNODB_BUFFER_POOL_SIZEの値は単位がバイトになっているので、そのまま取得すると分かりづらいため、1024で割って調整すると良いです。
例はMB（メガバイト）ですが、さらに/1024を付け足せばGB（ギガバイト）に変換できます。

# まとめ
かなり前にMySQLのメモリ設定を確認する作業に同席してたことはあったけど、
なにやってるかちゃんと理解してなかったから良い機会だった。

強い人が残してくれたクエリと、
下記の参考記事のおかげで点と点が繋がって理解度が深まった。

長いクエリでも、ひとつひとつ丁寧に細分化して読み解いていけば意外と簡単だった。
（@@はグローバル変数とか、/1024してるのは単位調整のためだとか、クエリが長く見えるのはasでカラム名調整してるからとか）

# 強い人からもらった参考クエリ
```sql
select
@@GLOBAL.KEY_BUFFER_SIZE as GLOBAL_KEY_BUFFER_SIZE,
@@GLOBAL.INNODB_BUFFER_POOL_SIZE as GLOBAL_INNODB_BUFFER_POOL_SIZE,
@@GLOBAL.INNODB_LOG_BUFFER_SIZE as GLOBAL_INNODB_LOG_BUFFER_SIZE,
@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE as THREAD_BUFFER_SIZE,
@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH
 + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS AS TOTAL_MEMORY_SIZE,
 (@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH
 + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024 AS TOTAL_MEMORY_SIZE_kb,
 (@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH
 + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024/1024 AS TOTAL_MEMORY_SIZE_mb,
 (@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH
 + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024/1024/1024 AS TOTAL_MEMORY_SIZE_gb;
```

# 参考記事
- [MySQL最低限のメモリ設定](https://qiita.com/zaburo/items/65bac3b8e0a635ada68c)
