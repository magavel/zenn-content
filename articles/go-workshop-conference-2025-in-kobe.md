---
title: "GWC2025 IN KOBE 参加レポート"
emoji: "🚢"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["go", "workshop", "conference", "アドベントカレンダー"]
published: true
publication_name: "team_soda"
published_at: 2025-12-14 13:30
---

:::message
[スニダンを開発しているSODA inc.の Advent Calendar 2025](https://qiita.com/advent-calendar/2025/soda-inc) 14日目の記事です。

- 昨日は[@22skyy](https://x.com/22skyyyy)さんによる「[バックエンドエンジニアがFlutter領域に越境してみて](https://zenn.dev/team_soda/articles/5454568194ed33)」でした！
- 明日は[@imajo](https://x.com/imasirooo)さんによる記事です、お楽しみに！
:::

おはようございます。SODAのmagavelです。

昨日(12/13)開催されたGo Workshop Conference 2025 IN KOBEに参加してきたので、そのレポートを書いていこうと思います。

通常のGo Conferenceとは違ってワークショップが主体のカンファレンスで、午前と午後の部があり、それぞれ一つずつ好きなワークショップに応募して参加することができるようになっていました。

https://gwc.gocon.jp/2025/

# 【午前】低レベルコンテナランタイム自作講座

https://go-workshop-conference.connpass.com/event/375445/

午前は、[@logica0419](https://x.com/logica0419)さんの「低レベルコンテナランタイム自作講座 ～コンテナ技術の地盤を理解する～」に参加してきました。

このワークショップでは、コンテナとは何か？という問いに対する解像度の高い答えを持つことができる内容になっており、冒頭30分ほど座学があってその後チーム or 個人に分かれてハンズオンを行うという形で進められました。

https://x.com/gwcjp/status/1999666853027938688

具体的には「仮想マシンより軽量な仮想化技術」というよく聞く抽象的な理解から一歩踏み込んで、OSやコンテナランタイム、システムプログラミングの視点からコンテナの実態について理解することができる内容でした。

特に座学パートは順を追ってわかりやすく構成されており、冒頭30分で参加して良かったなと感じる満足感でした。
今後も資料を更新して不定期で実施していく予定とのことだったので、

コンテナとは、

> 何も仮想的にシミュレートはしていない

> プロセスツリーのある枝から先を、Namespaceやchroot/pivot_root、cgroupなどの機能を使って隔離して実行したもの

と聞いて、よくわからないと思った方はぜひ参加してみると良いかもしれません。

# 【午後】Go with AI

https://go-workshop-conference.connpass.com/event/375461/

午後は、[@tenntenn](https://x.com/tenntenn)さんの「Go with AI」に参加しました。

このワークショップでは、Google ADK(Agent Development Kit) for Goを使ってAIエージェントを作成する方法をハンズオン形式で学べるものになっていました。

最初はシンプルなエージェントを作ることから始まり、カスタムツールを作成してエージェントに実行させたり、そしてそれをMCPサーバーとして切り出すところまで実装することができました。

当日使われた資料は↓のリポジトリで公開されています。

https://github.com/gohandson/adk-ja

ステップごとにREADMEが用意されていたり、実装自体も穴埋め形式で行えるようになっていたりと、とてもわかりやすい親切な作りになっているので、実際手元で試してみることができると思います。

さくさく進められるので社内の勉強会の題材としても良さそうだなと感じました（なんと好きに開催してOKとの記載あり）

https://x.com/magavel_dev/status/1999760251927846982

# 他にも参加したいワークショップが多かった

https://go-workshop-conference.connpass.com/event/375456/

https://go-workshop-conference.connpass.com/event/375457/

どれもみんな魅力的なワークショップで、その中でも特に↑の二つはかなり気になっていたワークショップだったので参加された方の参加レポートを楽しみにしています。

# TinyGoの展示ブースが豪華だった

![](https://storage.googleapis.com/zenn-user-upload/14883b9d15d5-20251214.jpg)

ワークショップの合間に立ち寄れるブースがいくつかありました。
TinyGoの展示ブースは、Gopherくん基板や名札基板、リアルな抵抗があるハンドルなど、メカメカしくてずっと見てられました。次は必ず名札基板を作りたいです。

# 飛び込み参加OKのLT会場や、シャッフルランチ、ジェスチャーゲームなどなど

会場は全体的に開放感があって、ワークショップ中も他のワークショップの盛り上がってる声が聞こえてきたり活気がありました。

シャッフルランチも途中参加させていただき、色々話せて楽しかったです。

ハッシュダグ [#GWC2025](https://x.com/hashtag/GWC2025) で検索してもらうと会場の雰囲気がよくわかると思います。

https://x.com/gwcjp/status/1999688593934258480

https://x.com/gwcjp/status/1999651022223081926

# 懇親会とずっと行ってみたかったHACK.BAR

![](https://storage.googleapis.com/zenn-user-upload/866b22e38c6b-20251214.jpg)

https://x.com/magavel_dev/status/1999849167691419996
