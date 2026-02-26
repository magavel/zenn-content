---
title: "背骨折れててもGo Conference mini 2026 in Sendaiに行ってきた（登壇&参加レポート）"
emoji: "✈️"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["go", "conference", "仙台", "登壇"]
published: false
publication_name: "team_soda"
---

こんにちは、SODAの[@magavel](https://x.com/magavel_dev)です。背骨が疎結合になっている人です（）

先週2/21(土)に開催されたGo Conference mini 2026 in Sendaiで登壇の機会をいただいたので、参加レポートを書いておこうと思います。

# 「モジュラモノリスにおける境界をGoのinternalパッケージで守る」というタイトルで発表しました！

@[speakerdeck](52cdc21278874c8dafb7a21812d6bd81)

弊社SODAではモノリスからモジュラモノリスへの移行を進めていまして、実は前回のGo Conference mini 2022 Autumn in SENDAIでも「[SNKRDUNKでGo+gRPCで すすめるモジュラモノリス](https://speakerdeck.com/sh0e1/snkrdunkdego-plus-grpcde-susumerumoziyuramonorisu)」というタイトルで発表がありました。

あれから約3年、その後の歩みと当時モジュラモノリス化を進めていくうえで課題の一つだった「モジュール間の境界をどう守っていくか」ということに対するアプローチなど、Goのコードベースにおけるモジュール分割で実践していることを3つ設計事例としてLTで話しました。

発表後、internal配下での詳しい構成について質問いただいたり、他社でも類似した方針を採用されていることが知れて良かったです。

関連セッション

- [AI時代のGo開発2026 爆速開発のためのガードレール](https://www.docswell.com/s/r4mimu/ZQXGNY-2026-02-21-102435) by [@r4mimu](https://x.com/r4mimu)さん
- [Goで実現する堅牢なアーキテクチャ：DDD、gRPC-connect、そしてAI協調開発の実践](https://speakerdeck.com/fujidomoe/godeshi-xian-surujian-lao-naakitekutiya-ddd-grpc-connect-sositeaixie-diao-kai-fa-noshi-jian) by [@fujidomoe](https://x.com/fujidomoe)さん

https://x.com/tenntenn/status/2025047114976509969

# 鑑定士Gopherくんと一緒にブース出展もしてました

https://note.com/team_soda/n/n9ac76e6ebeb2?sub_rt=share_b

協賛レポートはnoteで公開されているのでぜひご覧ください↑

https://x.com/soda_engineers/status/2025143043423437004?s=20

今回のブースでは「Goめんなさいボード」と「GopherType診断」という企画を2つ用意しました。

Goめんなさいボードでは多くの人に失敗談を書いていただき、特に[@goccy54](https://x.com/goccy54)さんの「linkname使ってrscに怒られた話」は有名でGopherたちからの反応が良かったですね！

あと個人的には、[@sago35tk](https://x.com/sago35tk)さんの「ブースにキーボードを展示してて登壇時に手元になかった話」も当日生まれたエピソードで好きでした。

> Goめんなさいボードがあって面白かったですw
https://zenn.dev/jo1106/articles/2785a393827627#soda.inc%E3%81%95%E3%82%93%E3%81%AE%E3%81%A8%E3%81%93%E3%81%A7%E3%81%AF%E3%81%93%E3%82%93%E3%81%AA%E3%81%AE%E3%81%8C%E3%81%82%E3%82%8A%E3%81%BE%E3%81%97%E3%81%9F%EF%BC%81

嬉しいコメントもありがとうございます！懇親会では去年のGoCon2025で企画したFind Gopherも好きだったと言ってくださる方もいてとても励みになりました。（ブース企画考えるの大変なので）

# 今回は2人同時登壇も実現しました

@[speakerdeck](7b6296382ab744cf89e7de1adffd7b20)

これまで仙台mini2022、京都mini2023、GoCon2025と登壇実績はあるもののいずれも一人だけだったのですが、
今回CfP締切当日に「プロポーザル出しませんか」と呼びかけたところ、[@22skyy](https://x.com/_22skyy)さんに手を挙げていただき、当日中にネタを考えてプロポーザルを出すというスピード感で実現しました。

※ちなみに文字通り"同時"登壇だったので当日お互いの発表を聞くことはできませんでした！

# miniだけどminiではないGo Conferenceだった

参加者数117名、セッション数30(LT含む)、スポンサーブースもあり、良い意味でminiとは？を考えさせられる規模感で賑わっていました。

会場ではRoomAで行われている発表がスポンサールームのモニターにもミラーリングされていたのですが、ブースを掛け持ちしつつもセッションを聞きたい自分にとってはとても助かりました。
