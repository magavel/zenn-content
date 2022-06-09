---
title: "PhpStormからGoLandに割引を維持したまま切り替えたい"
emoji: "🪄"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["JetBrains", "PhpStorm", "GoLand", "ide", "editor"]
published: true
---
<!-- 導入部分数行 -->
Goをがっつり書きたくなって、今までプライベートで2年ぐらい契約してたPhpStormをGoLandに切り替えようとした際、継続割引を維持するためにワンステップ踏む必要があったので紹介します。

# ざっくり言うと
**継続割引を維持するには、**

**PhpStorm**
**↓**
**All Products Pack(1ヶ月だけ)**
**↓**
**GoLand**

**の順に切り替える。**

- PhpStormからGoLandに直接切り替えはできない（新規購入扱いになるので継続割引がリセットされる）
- 逆のパターンやRubyMineなども同じ方法が使える

# どれぐらいお得か
**40%の継続割引を持っている場合、1年で少なくとも2,380円以上節約できます。**

・PhpStormの更新はせず、GoLandを新規購入した場合 = **10,300円**
・All Products Pack経由でGoLandに切り替えた場合 = **7,920円**(1,720円 + 6,200円)

※継続割引なしのGoLandの1年分の価格：10,300円
※40%割引が付いたAll Products Packの1ヶ月分の価格：1,720円
※40%割引が付いたGoLandの1年分の価格：6,200円

:::message
実際には日割り計算されたり、All Products PackがGoLandの価格で比例分配（GoLandを使える期間が伸びる）されるので金額は計算通りにはならないです。
:::

[サブスクリプションオプションと価格 - JetBrains](https://www.jetbrains.com/ja-jp/store/#personal?billing=yearly)


# ライセンスと継続割引の仕組み
継続割引はライセンスID単位で付与されるみたいです。

なので、公式にサポートされている切り替えパターンだったらライセンスIDが変わらないから継続割引を維持できるけど、それ以外は新規のライセンスIDが発行されるから（新規購入扱いになるから）継続割引が失われるということだと思います。

# 公式に切り替えがサポートされているパターン
> JetBrains では、1 つの製品から別の製品にサブスクリプションを切り替えるパスを次のとおり用意しています。 これらのオプションは、個人および商用（法人）の両方のサブスクリプションでご利用いただけます：
> 
> ・IDE 単体製品または dotUltimate から All Products Pack へのアップグレード
> ・PyCharm から IntelliJ IDEA
> ・RubyMine から IntelliJ IDEA
> ・DataGrip から IntelliJ IDEA
> ・GoLand から IntelliJ IDEA
> ・PhpStormからIntelliJ IDEAへ
> ・WebStormからIntelliJ IDEAへ
> ・WebStormからGoLandへ
> ・WebStormからPhpStormへ
> ・Rider から dotUltimate
> ・ReSharper から Rider
> ・ReSharper から dotUltimate
> また、以下の製品についてはダウングレードまたは切り替えが可能です。
> 
> ・All Products Pack から IDE 単体製品または dotUltimate
> ・dotUltimate から ReSharper
> ・dotUltimate から Rider
> ・Rider から ReSharper

今回は、`IDE 単体製品または dotUltimate から All Products Pack へのアップグレード`と`All Products Pack から IDE 単体製品または dotUltimateへのダウングレード`を組み合わせたパターンです。

# まとめ
この辺りの具体的な事例が探しても見つからなかったので、サポートに連絡したら20分ぐらいで回答が来てCX最高でした。

去年にも公式から日本語パックがリリースされたり、日本市場にも力入れてる感じがあって嬉しい。

# 参考記事
[1つのIDEからの別のIDEへのアップグレード - JetBrains](https://sales.jetbrains.com/hc/ja/articles/360001409224-1%E3%81%A4%E3%81%AEIDE%E3%81%8B%E3%82%89%E3%81%AE%E5%88%A5%E3%81%AEIDE%E3%81%B8%E3%81%AE%E3%82%A2%E3%83%83%E3%83%97%E3%82%B0%E3%83%AC%E3%83%BC%E3%83%89)
[IntelliJ ベース IDE 向けの日本語言語パックが公式にリリース - JetBrains](https://blog.jetbrains.com/ja/idea/2021/07/language-packs-public-release/)