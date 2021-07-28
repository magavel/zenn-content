---
title: "「MySQLに割り当てられているメモリ量を確認して」と言われたときに見る記事"
emoji: "🐬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["MySQL", "Linux"]
published: false
---

# 前提知識
- 

# メモリ量の確認クエリ
```sql
SELECT @@GLOBAL.INNODB_BUFFER_POOL_SIZE/1024/1024/1024 as メモリサイズ（単位：GB）;
```

# 参考文献
- [MySQL最低限のメモリ設定](https://qiita.com/zaburo/items/65bac3b8e0a635ada68c)
