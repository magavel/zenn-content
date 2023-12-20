---
title: "後方互換性のあるGoだからこそ実装依存なコードに気をつけたい話"
emoji: "🔬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["go"]
published: true
published_at: 2023-12-21 07:00
---
# ＼[スニダンを開発しているSODA inc.の Advent Calendar 2023](https://qiita.com/advent-calendar/2023/soda-inc) 21日目の記事です!!!／

そして今日はIPAデータベーススペシャリストの合格発表日でもあります！ 無事合格してました！（これを書いているときはまだ発表前なので未来の記憶です）

# はじめに
Goの大きな特徴として後方互換性が保たれているので、言語のバージョンアップのハードルが低いというメリットがありますよね。
ですが、そんなGoでも完全な互換性を保証しているわけではありません。特定のケースにおいてバージョンアップによってプログラムを壊す可能性があるのです。

実際に遭遇することは少ないかもしれませんが、どういうケースでそれが起こるのか、どうすれば回避できるのか、実際にあった過去の事例を交えて知っておくと、バージョンアップ時の漠然とした不安を減らし後方互換性の恩恵を最大化できるようになると思います。

実際にプロダクションコードで遭遇した経験と、[Go 1 and the Future of Go Programs](https://go.dev/doc/go1compat)や[Backward Compatibility, Go 1.21, and Go 2](https://go.dev/blog/compat)で紹介されている事例をもとに見ていきます。

# 実装依存についておさらい

ここで言う実装依存とは、Goの言語仕様には明記されておらず、Goの内部実装によって挙動が変わるコードのことです。

よく知られているものでいうと、mapの要素をrangeループで取り出すときの順番の挙動でしょうか。 （厳密にはGo1 Release Notesでランダムであることが明記され、順序を予測できないように修正されたので現在は実装依存になることはありません。）

初期のバージョンではmapの要素を取り出す順序については明記されておらず、実行する環境によって異なるものの、実行する度に順番が変わるわけではありませんでした。
そのため、mapの要素を特定の順序で取り出すことを意図していたコードは、特定のバージョンでは動作するものの、Go1以降では意図通りに動作しなくなるという問題がありました。

このようなバージョンアップによって壊れてしまうコードを、実装依存なコードと呼びます。

# どういうケースで互換性が破壊されるのか？

まずはどういうケースで互換性が破壊されるのかについて、[Go 1 and the Future of Go Programs](https://go.dev/doc/go1compat)にリストアップされているので、これを一部引用しながら見ていきます。

## セキュリティの問題に対処する場合

> Security. A security issue in the specification or implementation may come to light whose resolution requires breaking compatibility. We reserve the right to address such security issues.

> 訳：セキュリティ。仕様や実装におけるセキュリティ上の問題が明らかになり、その解決には互換性の破壊が必要となる場合があります。私たちは、そのようなセキュリティ問題に対処する権利を留保します。

書かれている通りなので特筆することはないのですが、セキュリティへの対応によって互換性を維持できない場合があるということですよね。

## 未定義の挙動が変更されるケース

> Unspecified behavior. The Go specification tries to be explicit about most properties of the language, but there are some aspects that are undefined. Programs that depend on such unspecified behavior may break in future releases.

> 訳：未定義の動作。Goの仕様では、言語のほとんどの特性について明示的であろうとしていますが、未定義の部分もあります。そのような未定義の動作に依存するプログラムは、将来のリリースで壊れる可能性があります。

これがまさに[実装依存についておさらい](#実装依存についておさらい)でも書いた通り、実装依存なコードを書いてしまった場合に起こりうるケースですね。

そして、先述のセキュリティや後述するバグ起因の互換性の破壊などと違って、これは実装者が意識して回避することができるケースでもあるので抑えておきたいポイントです。

## Go自体の仕様の誤りやバグがあった場合

> Specification errors. If it becomes necessary to address an inconsistency or incompleteness in the specification, resolving the issue could affect the meaning or legality of existing programs. We reserve the right to address such issues, including updating the implementations. Except for security issues, no incompatible changes to the specification would be made.
> 
> Bugs. If a compiler or library has a bug that violates the specification, a program that depends on the buggy behavior may break if the bug is fixed. We reserve the right to fix such bugs.

> 訳：仕様の誤り。仕様の矛盾や不完全性に対処する必要が生じた場合、その問題を解決することで、既存のプログラムの意味や合法性に影響を与える可能性があります。私たちは、実装の更新を含め、そのような問題に対処する権利を留保します。セキュリティ上の問題を除き、仕様に互換性のない変更を加えることはありません。
> 
> バグ。コンパイラやライブラリに仕様に違反するバグがある場合、そのバグが修正されると、バグのある動作に依存するプログラムが壊れる可能性があります。私たちは、そのようなバグを修正する権利を留保します。

仕様の誤りやバグによって成立していた挙動があった場合に、それを修正することで互換性が破壊される可能性があるということが説明されています。

ここは[未定義の挙動が変更されるケース](#未定義の挙動が変更されるケース)と似ていて、違いがわかりにくい気がするので先に該当しそうな[過去の事例](https://go.dev/blog/compat#:~:text=ParseIP.%20As,what%20it%20means.)を紹介しておきます。

ざっくり要約すると、Go1.17で互換性を犠牲にして`net.ParseIP`でIPアドレスの解析で不要なゼロを拒否するようになり、Cのライブラリや新旧Goバージョン間でのIPアドレスの解析が異なるというカオスな状況を避けたという事例です。

## ドットインポートをしているケース

> Dot imports. If a program imports a standard package using import . "path", additional names defined in the imported package in future releases may conflict with other names defined in the program. We do not recommend the use of import . outside of tests, and using it may cause a program to fail to compile in future releases.

> 訳：ドットインポート。プログラムが import ."path"のように標準パッケージをインポートした場合、将来のリリースでインポートされたパッケージで定義された追加の名前と、そのプログラムで定義された他の名前が衝突する可能性があります。テスト以外でのimport . の使用は推奨しませんし、これを使用すると将来のリリースでプログラムのコンパイルに失敗する可能性があります。

ドットインポートとは次のような書き方のことです。

```go
package main

import (
	. "fmt" // パッケージへのパスの前に.を付けると、そのパッケージの関数を呼び出すときにパッケージ名を省略できる
)

func main() {
	Println("心臓を捧げよ！") // fmt.Println()ではなくPrintln()で呼び出せる
}
```

複数の標準パッケージをドットインポートしていると、将来のリリースでそれらのパッケージの中にすでに存在している名前の関数が追加されたとき、名前衝突でどちらの関数を呼び出すか判断できなくなるのでコンパイルエラーになるということですね。

# [Backward Compatibility, Go 1.21, and Go 2](https://go.dev/blog/compat)で紹介されている過去の事例

ここからはいくつかピックアップして過去の事例を見ていきます。

## フィールド名を指定せずに宣言している構造体リテラルに新しいフィールドが追加されたケース

> Here is some code that runs fine in Go 1:
> ```go
> package main
> 
> import "net"
> 
> var myAddr = &net.TCPAddr{
> 	net.IPv4(18, 26, 4, 9),
> 	80,
> }
> ```
> 
> Package main declares a global variable myAddr, which is a composite literal of type net.TCPAddr. In Go 1, package net defines the type TCPAddr as a struct with two fields, IP and Port. Those match the fields in the composite literal, so the program compiles.
> 
> In Go 1.1, the program stopped compiling, with a compiler error that said “too few initializers in struct literal.” The problem is that we added a third field, Zone, to net.TCPAddr, and this program is missing the value for that third field. The fix is to rewrite the program using tagged literals, so that it builds in both versions of Go:
> ```go
> var myAddr = &net.TCPAddr{
>     IP:   net.IPv4(18, 26, 4, 9),
>     Port: 80,
> }
> ```

これは一つ目のサンプルコードがフィールド名を省略して`net.TCPAddr`を構造体リテラルとして宣言しています。構造体に定義されているフィールドの数と一致していれば宣言時に省略できるようになっているので、Go1の時点では何も問題ないコードでした。

しかし、Go1.1で`Zone`という新しいフィールドが追加されたことにより、フィールドの数が一致しなくなりコンパイルエラーになるという問題が発生したという説明がされています。

対処方法は明確で、二つ目のサンプルコードのように構造体リテラルとして宣言するときはフィールド名を必ず指定するようにすれば良いですね。

現在は`go vet`で`struct literal uses unkeyed fields`と検出されるようになっていて、エディタやIDEも注意してくれるので同じ問題に遭遇することは稀な気がしますが、当時はリリースノートに短いコメントを書く価値があるほど新しい発見だったみたいです。

## ソート処理の速度を改善したことで出力結果が変わってしまったケース

> Sort. Another example happened in Go 1.6, when we changed the implementation of sort to run about 10% faster. Here’s an example program that sorts a list of colors by length of name:
> 
> ```go
> colors := strings.Fields(
>     `black white red orange yellow green blue indigo violet`)
> sort.Sort(ByLen(colors))
> fmt.Println(colors)
> 
> Go 1.5:  [red blue green white black yellow orange indigo violet]
> Go 1.6:  [red blue white green black orange yellow indigo violet]
> ```

これはGo1.5からGo1.6のときの事例で、ソートアルゴリズムを変更することによってソート処理の速度を10%ほど改善したところ、出力結果が変わってしまったというものです。

コード例にもあるように、greenとwhite、yellowとorangeの順番が逆になってしまっていますね。 なので、もしソート結果が特定の順序になることを期待していたコードがあった場合は壊れてしまいます。

ちなみにサンプルコードでは`ByLen`の定義は省略されていますが、sort.Sortの引数に渡すために`sort.Interface`を実装した型を定義しているだけだと思われます。 （なんで`sort.StringSlice`を使っていないのかはよくわかっていない）

## 関数が受け入れる引数の解釈が変更されたケース

> ParseInt. For example, Go 1.13 added support for underscores in large numbers for readability. Along with the language change, we made strconv.ParseInt accept the new syntax. This change didn’t break anything inside Google, but much later we heard from an external user whose code did break. Their program used numbers separated by underscores as a data format. It tried ParseInt first and only fell back to checking for underscores if ParseInt failed. When ParseInt stopped failing, the underscore-handling code stopped running.
> 
> 訳：例えば、Go 1.13では、可読性を高めるために、大きな数字のアンダースコアがサポートされた。言語の変更とともに、strconv.ParseIntも新しい構文を受け入れるようにしました。この変更によってGoogle内部では何も壊れませんでしたが、ずっと後になって、コードが壊れたという外部のユーザーから連絡がありました。彼らのプログラムはアンダースコアで区切られた数字をデータフォーマットとして使っていました。最初にParseIntを試し、ParseIntが失敗したときだけアンダースコアのチェックに戻った。ParseIntが失敗しなくなると、アンダースコアを処理するコードは実行されなくなった。

これを最初読んだとき、大きい数字の可読性をあげるためにアンダースコアを使う文化に馴染みがなかったので、どういうことなんだ？と思ったのですが、どうやら[桁数が多い数値を書くとき、大抵の言語で桁区切り文字が使える](https://zenn.dev/nyancat/articles/20220602-numeric-literal-separator)という背景があるみたいでした。（網羅的にまとまっていて良い記事大感謝）

この事例は説明だけでイメージがしにくいと思うので、具体的なシナリオを想定してサンプルコードを書いてみます。

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

// Product は製品を表す構造体
type Product struct {
	ModelNumber int64 // 品番
	LotNumber   int64 // ロット番号（初期ロットはロット番号を持たない）
}

func main() {
	fmt.Printf("%+v\n", parseProductCode("150")) // 品番が150の初期ロットの製品
	fmt.Printf("%+v\n", parseProductCode("200_3")) // 品番が200の第3ロットの製品
}

// プロダクトコードをパースしてProduct構造体を返す
func parseProductCode(productCode string) Product {
	// 第2引数に0を指定すると_を含む数字をパースする
	// 10を指定すると_を含む数字をパースしない（擬似的にGo1.12の挙動を再現できる）
	modelNumber, err := strconv.ParseInt(productCode, 0, 64) 

	// ロット番号がある場合は、ロット番号もパースしてProduct構造体を返す
	if err != nil {
		s := strings.Split(productCode, "_")

		modelNumber, _ = strconv.ParseInt(s[0], 0, 64) // エラーチェック省略
		lotNumber, _ := strconv.ParseInt(s[1], 0, 64)    // エラーチェック省略

		return Product{ModelNumber: modelNumber, LotNumber: lotNumber}
	}

	// 初期ロットの場合は、ロット番号を持たないProduct構造体を返す
	return Product{ModelNumber: modelNumber}
}
```

このコードは、`_`で区切られた品番とロット番号からなる`productCode`をパースして`Product`構造体を返す処理です。

ここでは初期ロットの製品はロット番号を持たないというドメイン要件があったと仮定していて、`parseProductCode()`には`productCode`に`_`が含まれているパターンと含まれていないパターンの2種類の処理をこなす必要があり、上記のような実装になっています。

Go1.12までは問題なく動作するのですが、Go1.13からは`strconv.ParseInt`がアンダースコアを含む数字をパースできるようになったことで、`productCode`に`_`が含まれている場合でも`strconv.ParseInt`がエラーを返さなくなり、ロット番号のパース処理が実行されなくなってしまいます。

実行結果は次のようになります。

```bash
# Go1.12で実行した場合
{ModelNumber:150 LotNumber:0}
{ModelNumber:200 LotNumber:3}

# Go1.13で実行した場合
{ModelNumber:150 LotNumber:0}
{ModelNumber:2003 LotNumber:0} # 意図しない品番の初期ロットとしてパースされてしまっている
```

サンプルコードで表現するために深夜に雑に考えたシナリオなので、洗練されていないかもですが、互換性の破壊によって不具合に繋がるケースを想像してもらえたでしょうか。

# まとめ

今回はあえて互換性が破壊されるケースに着目しましたが、大前提としてGoは最大限の後方互換性を提供してくれているので、実装依存なコードに気をつけてGoのバージョンアップに乗り遅れないようにしたいですね。

互換性の破壊を見つけるための最善の方法は、テストコードを十分に書くことであることも忘れずに！（ちなみにこの記事では触れていませんがGODEBUG設定による互換性問題の回避策も用意されていたりいます）

# 参考
- [Go 1 and the Future of Go Programs](https://go.dev/doc/go1compat)
- [Backward Compatibility, Go 1.21, and Go 2](https://go.dev/blog/compat)
- [proposal: permit blank (_) separator in integer number literals #28493](https://github.com/golang/go/issues/28493)
- [Go, Backwards Compatibility, and GODEBUG](https://tip.golang.org/doc/godebug)

# さいごに

アドベントカレンダーの宣伝です！

昨日は[@koh7](https://zenn.dev/koh7)さんによる「[MySQLのトランザクション分離レベルとアノマリーについて調べてみた]()」でした！

明日は[@okauchiwani](https://zenn.dev/okauchiwani)さんによる「Datadogブラウザテストを触ってみた」です！

お楽しみに！