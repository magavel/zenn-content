---
title: "Go Conference 2025でLT登壇してきました！"
emoji: "🛎️"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["go", "gocon", "LT", "登壇"]
published: true
publication_name: "team_soda"
published_at: 2025-10-02 18:30
---

こんにちは。SODAのmagavelです。

先日(9/27〜28)開催されたGo Conference 2025で登壇の機会をいただき、LTスピーカーとして参加してきたので、発表内容の振り返りと感想を書いておこうと思います！

# weak pointerについて話しました

https://gocon.jp/2025/talks/959089/

@[speakerdeck](d6215850486040e6b9bd8bd883495fe5)

LTではGo1.24で追加されたweakパッケージについて、weak pointerの概念から使い所、導入の背景を中心に、実際の使用例を少し交えて発表させていただきました。

聞き手は↓のような方々を想定しつつ、

- weak pointerの概念を知らない人
- 知ってるけど使い所はいまいちわかってない人
- 他言語で使ったことあるけどなんでGoに導入されたんだろうと思ってた人

Go初心者〜advancedな方まで何か一つでも持って帰ってもらえるような発表内容にしたいと考えながらスライドを作りました。

今振り返ると、まとめの最後にキーワードとして挙げたようなadvanced側に振った内容にフォーカスをあてても良かったかなと思ったりしましたが、応援で聞きにきてくれていた社内の人から「周りの人がすごい頷いていましたよ」と教えてもらって少し安心しました。

# はじめての登壇

実は今回がはじめての外部登壇でもありました。LTとはいえ、いきなり大舞台での登壇はかなり緊張しました。

プロポーザルを締切駆動で提出していたこともあり、当日の朝までホテルでruntimeの追加調査やスライド構成の見直しをしていたりしました。一睡もできなかったですが、無事作りきれてよかったです。

また、事前に社内でデモを行なってアンケートによるフィードバックも貰ったりしていました。みんな忙しいなか思っていたより多くの人が参加してくれて良い組織だなあと改めて感じました。

![](/images/go-conference-2025/slack.png)

:::details 社内フィードバック
![](/images/go-conference-2025/survey.png)
:::

### 当日

![](/images/go-conference-2025/talk.jpg)

2日目の昼休憩にプロジェクターの接続確認を行なったり、Speaker控室で待機していたらチョコを貰ったりしました。

LT本番では「古の作法」と呼ばれる(？)スタイルで司会の方の手腕もあり、会場は盛り上がっていました。慣れが必要そうですが笑いもあって発表しやすい雰囲気でしたね。登壇する側の立場を経験して、聞き手の反応のありがたみも身をもって学びました。

:::details X上での反応抜粋（ありがとうございました🙏）
https://x.com/tomtwinklestar/status/1972237578893201903
https://x.com/luccafort/status/1972237552456458261
https://x.com/sivchari/status/1972237632848748586
https://x.com/tenntenn/status/1972237724167114947
https://x.com/glassmonekey/status/1972237739811885191
https://x.com/songmu/status/1972237748083081443
https://x.com/ymotongpoo/status/1972237841326539070
https://x.com/katzchum/status/1972238067240145065
https://x.com/tesso57/status/1972238158692802775
https://x.com/osamingo/status/1972238295733342609
https://x.com/katzchum/status/1972238322757185959
https://x.com/tenntenn/status/1972238358043914369
https://x.com/utgwkk/status/1972238374879773032
https://x.com/tomtwinklestar/status/1972238413547119013
https://x.com/luccafort/status/1972238426570391893
https://x.com/glassmonekey/status/1972238465476727201
https://x.com/convto/status/1972238491338904006
https://x.com/sue445/status/1972238504576061867
https://x.com/knsh14/status/1972238529532235847
https://x.com/tenntenn/status/1972238535693566119
https://x.com/convto/status/1972238557868912813
https://x.com/hon_d7174/status/1972238564328137052
https://x.com/goccy54/status/1972238565737435547
https://x.com/utgwkk/status/1972238624960995357
https://x.com/ymotongpoo/status/1972238631692898518
https://x.com/otakakot/status/1972238680548102568
https://x.com/hon_d7174/status/1972238687594484162
https://x.com/o_ga09/status/1972238703994257585
https://x.com/glassmonekey/status/1972238719160836131
https://x.com/kazu_gor2/status/1972238730359648359
https://x.com/bb30330807/status/1972238849704427777
:::

LTトップバッターとして手間取ってしまった場面もありましたが、発表自体はなんとか時間内に納めることができました。（多分もう`Cmd + F1`を忘れることはないでしょう...）

# ブース出展のためにEbitenginでゲームも作りました

https://x.com/hajimehoshi/status/1971743015241994513

弊社SODAはSilverスポンサーとしても参加しており、ブースの出し物として登壇内容(interningの概念)から着想を得たブラウザゲームを作りました。Opening Sessionでも話にあがったEbitenginを使ってほぼGoで実装されています。

思っていた以上に大きな反響があり、Ebitengin作者の[@hajimehoshi](https://x.com/hajimehoshi)さんにもシェアいただくなど、Gopherの方々から好評でコントリビューター一同打ち上げで美味しいお酒が飲めました。

リポジトリはこちらで公開しています。

https://github.com/snkrdunk/find-gopher

スポンサーとしての詳細な参加レポートは、後日noteの方で公開される予定です。

また今回、登壇準備やブース対応などで昨年ほどセッションを聞けなかったのですが、面白そうなセッションやワークショップがたくさんあったので資料を見返したいと思っています。

なお、弊社エンジニアによるセッションレポートも別途公開される予定です。

# 次のGo Conference 2026に向けて

個人としてはShort Session(20分)に挑戦することを目標にしつつ、社内からの参加者や登壇者を増やすためのGoに関する取り組みなんかも始めたいなと考えています。

そして来年はもっとたくさんのGopherたちと交流したいので、Xアカウント([@magavel_dev](https://x.com/magavel_dev))をフォローしてもらえると嬉しいです！（2秒でフォロバします）
