---
title: "Goのビルドタグの書き方が// +buildから//go:buildに変わった理由"
emoji: "🐷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["go"]
published: true
---

# ざっくり言うと

- `// +build`は、一般的な`&&`や`||`を使う論理式の書き方じゃないので分かりにくかった
- 複数行書くこともできたので暗黙的な論理演算になり、意図しないビルド条件になることがあった
- だから、**より身近で明確な書き方ができる`//go:build`へ置き換えることになった**（記述位置の条件とかも見直してる）

# Go1.17でビルドタグの書き方が変わることがアナウンスされた
2021年8月のGo1.17から`//go:build`が使えるようになりました。

後でも触れるのですが、もちろん後方互換性が維持されているので`// +build`も使えます。

`// +build`だけが指定されているファイルに対して`go fmt`を実行すると、同等の`//go:build`がすぐ上の行に追加されるようになっています。

# `// +build`はいくつかの問題を抱えていた

もともと`// +build`に指定できるのはOS名かCPUアーキテクチャのみ（もしくはOS名/CPUアーキテクチャの形式）だったところに、後から否定形の指定に対応することで結果的に論理式で評価できるようになったという背景があり、そのために色んな問題を抱えてしまったみたいです。

問題点の具体例は、[Bug-resistant build constraints — Draft Design](https://go.googlesource.com/proposal/+/master/design/draft-gobuild.md)に詳しく書かれているので引用しながら見ていきます。

## 暗黙的な論理演算が行われている

> For example, this line indicated that the file should build on Linux for any architecture, or on Windows only for 386:
> 
> ```go
> // +build linux windows/386
> ```
> 
> That is, each line listed a set of OR’ed conditions. Because each line applied independently, multiple lines were in effect AND’ed together.

これはLinuxではすべてのアーキテクチャ、Windowsでは386アーキテクチャのみビルドするという意味になります。（なんで386アーキテクチャが例として選ばれているのかはよくわかってない）

つまり、`// +build`の行の中では、スペースで区切った指定がOR条件で評価されるということですよね。

次の例を見てみます。

> For example,
> 
> ```go
> // +build linux windows
> // +build amd64
> ```
> and
> 
> ```go
> // +build linux/amd64 windows/amd64
> ```
> were equivalent.

これは2つの書き方が同じ意味になることを説明していて、Linuxかつamd64アーキテクチャ、もしくはWindowsかつamd64アーキテクチャの場合にビルドされるという意味になります。

つまり、`// +build`の行を複数書くと暗黙的にAND条件で評価されるということですよね。

たしかにビルド制約が複数になってくるとわかりにくいし、否定とかも考え出すと普通に混乱しそうです。

## 一般的な論理演算子が使えない
その後、OS名とアーキテクチャを連結するだけだった役割の`/`がAND条件を表現するように拡張されました。

さらに、否定を表現する`!`が追加されたタイミングで、AND条件を表現する`/`は`,`に変更され、カスタムビルドタグを指定したり、Goのバージョン指定などもできるようになりました。

少し整理すると、

- OR条件はスペースで表現
- AND条件は`,`で表現、もしくは複数行書くことで表現
- 否定は`!`で表現
- 指定対象には、OS名、CPUアーキテクチャ、カスタムビルドタグ、Goのバージョンがある

になります。

Goのコードのように`&&`や`||`が使えなかったり、ビルドタグのためだけにこのルールを覚えておくのも辛い気がしました。

## 記述位置の制約が限定的で分かりにくい

[Go1.14.15までのドキュメント](https://pkg.go.dev/go/build@go1.14.15#hdr-Build_Constraints)には、ビルドタグの記述位置について次のような記載がありました。

> Constraints may appear in any kind of source file (not just Go), but they must appear near the top of the file, preceded only by blank lines and other line comments.
> These rules mean that in Go files a build constraint must appear before the package clause.

> 訳：制約は、（Goに限らず）どのような種類のソースファイルにも現れることができますが、ファイルの先頭付近に現れ、空行や他の行コメントだけが先行しなければなりません。
> これらのルールは、Goファイルでは、ビルド制約がパッケージ句の前に現れなければならないことを意味します。

これだけ読むと、空行とコメント行はビルドタグの前に書くことができると理解できそうですが、ここに落とし穴があります。

実際には、ビルドタグの検索は最初の`//`行か空行以外で停止するので、`/* */`コメントはできません。

> The syntax even excludes C-style /* */ comments, so this is an ignored build constraint:
> 
> ```go
> /*
> Copyright ...
> */
> 
> // +build linux
> 
> package main
> ```

この例だと`// +build linux`は無視されてしまいます。

加えて、Docコメントと区別するために、ビルドタグの次の行には空行が必要という制約もあります。

> ```go
> // Doc comment.
> // +build linux
> package p
> ```

この例の`// +build linux`はビルドタグではなく、Docコメントの一部として扱われます。

# これらの問題を克服したのが`//go:build`

これまでの問題点を踏まえて、`//go:build`は次のような仕様になっています。

- 複数行に記述できないようにし、暗黙的な論理演算を排除した
- Goの論理演算子や括弧を使えるようにした
- `//go:generate`や`//go:noinline`のような書き方に合わせた
- 記述位置が空行とコメント行（`//`と`/* */`）を除いたファイルの先頭という条件に緩和された

> ```diff
> - // +build linux darwin
> - // +build amd64 arm64 mips64x ppc64x
> 
> + //go:build (linux || darwin) && (amd64 || arm64 || mips64x || ppc64x)
> ```

**このように書けるようになりました！**

否定したいときも、

> ```diff
> - //go:build (linux || darwin) && (amd64 || arm64 || mips64x || ppc64x)
> 
> + //go:build !((linux || darwin) && (amd64 || arm64 || mips64x || ppc64x))
> ```

全体を囲って`!`をつけるだけで済みます。

# 既存のエコシステムを壊さないための移行ステップがちゃんと考えられてる
移行ステップがちゃんと考えられてるなあと思ったので紹介します。

`// +build`から`//go:build`への移行期間は、Go1.16 〜 Go1.18まで段階的に設定されました。

Go1.16では、`// +build`がないのに`//go:build`があると拒否するようにしておき、ビルドタグは今まで通り`// +build`を使うようにします。

- この時点ではリリースノートなどで公表せず、もしこの変更が近づいていることを知った誰かがフライングで`//go:build`だけを書いてしまった場合に既存のエコシステムが壊れないように備えています。

Go1.17では、`go fmt`で`// +build`と同等の`//go:build`を生成するようにし、ビルドタグは`//go:build`を使うようにします。

- この段階が移行のメインステップで、新しい構文の`//go:build`を認識するようになるのですが、新旧のビルドタグが共存することになります。もし`go fmt`で置換できない複雑なビルド制約があったとき（ほぼない）は、コンパイラは`//go:build`を正として扱います。

Go1.18で、`// +build`を削除して、`//go:build`への移行を完了させます。

- Goのサポートバージョンは、最新とその1つ前までです。Go1.18がリリースされた時点でGo1.16はサポートされなくなって、サポートバージョンのGo1.17と1.18は`//go:build`を認識できるので、`go fix`で`// +build`を安全に削除できるようになります。

# まとめ

プロジェクトルートで`go fix -fix buildtag ./...`を実行して移行を完了させよう！

# 参考
- [Bug-resistant build constraints — Draft Design](https://go.googlesource.com/proposal/+/master/design/draft-gobuild.md)
- [Go 1.17 Release Notes](https://go.dev/doc/go1.17)
- [go:build design draft](https://golang.org/s/go-build-video)