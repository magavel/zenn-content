---
title: "AWS EC2インスタンスのディスク容量（EBSボリュームサイズ）を増やす方法"
emoji: "🎃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["AWS", "EC2", "Linux"]
published: true
---

業務で使っているテスト環境がディスクフルになって落ちた際に、ディスク容量を増やして対処したので、その手順を残しておきます。

# ざっくり言うと
- AWSコンソール上でボリュームサイズを変更したあと、SSHでサーバに入ってファイルシステムを拡張する。
- EC2インスタンスは起動したままでOK
- EBSボリュームのデタッチ/アタッチも不要

# 前提条件
1. 現行世代のインスタンスタイプであること
2. OSがLinux系であること（今回Windowsは対象外）
3. ファイルシステムが**xfs**または**ext4**であること（確認方法は後述します）

# 手順

## 【事前準備】対象のEBSボリュームを特定しておく

![](/images/aws-ebs-increase-volume/aws-ebs-increase-volume-1.png)

1. EC2コンソールから対象のEC2インスタンスをチェックする
2. 「ストレージ」タブを選択する
3. 対象のEBSのボリュームIDをクリックする

:::message
ボリュームIDの右横にある**デバイス名**をメモしておいてください。
手順3でファイルシステムを拡張する際に必要になります。
:::

---

## 【手順1(スキップ可)】スナップショットを作成する
AWS公式のベストプラクティスで、ロールバックできるようにスナップショットを作成しておくように書かれているので念のため。

![](/images/aws-ebs-increase-volume/aws-ebs-increase-volume-2.png)

1. 対象のボリュームをチェックする
2. 画面右上のアクションから「スナップショットの作成」をクリックする

---

![](/images/aws-ebs-increase-volume/aws-ebs-increase-volume-3.png)

3. 任意の項目を入力し「スナップショットの作成」をクリックする

![](/images/aws-ebs-increase-volume/aws-ebs-increase-volume-4.png)

↑のようなバナーが出てきたらOKです。

---

## 【手順2】コンソールからボリュームサイズを変更する

![](/images/aws-ebs-increase-volume/aws-ebs-increase-volume-5.png)

1. 対象のボリュームをチェックする
2. 画面右上のアクションから「ボリュームの変更」をクリックする

---

![](/images/aws-ebs-increase-volume/aws-ebs-increase-volume-6.png)


3. サイズ(GiB)項目を変更後のボリュームサイズに変更する
4. 画面右下の変更をクリックする

---

![](/images/aws-ebs-increase-volume/aws-ebs-increase-volume-7.png)

5. ボリュームサイズ変更のアラートが表示されるので変更をクリックする

![](/images/aws-ebs-increase-volume/aws-ebs-increase-volume-8.png)

↑ボリュームサイズの変更が完了するまで待つ（完了すると緑色の使用中になる）

---

## 【手順3】ファイルシステムを拡張する
SSHでサーバに接続し、ファイルシステムを拡張します。

### まずはファイルシステムが何か確認する
```bash
$ df -hT

# 実行結果
ファイルシス   タイプ   サイズ  使用  残り 使用% マウント位置
devtmpfs       devtmpfs   474M     0  474M    0% /dev
tmpfs          tmpfs      483M     0  483M    0% /dev/shm
tmpfs          tmpfs      483M  404K  482M    1% /run
tmpfs          tmpfs      483M     0  483M    0% /sys/fs/cgroup
/dev/xvda1     xfs        8.0G  1.6G  6.5G   20% /
tmpfs          tmpfs       97M     0   97M    0% /run/user/1000
```

今回は`/dev/xvda1`が対象で、ファイルシステムが**xfs**であることがわかります。
※対象は、[事前準備](#事前準備対象のebsボリュームを特定しておく)でメモしておいたデバイス名から判断します。（デバイス名 + 数字の形式）

### パーティションを確認する
```bash
$ lsblk

# 実行結果
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  16G  0 disk
└─xvda1 202:1    0   8G  0 part /
```

`/dev/xvda`に`/dev/xvda1`というパーティションがあることがわかります。
パーティションの容量が8GBのままなので、拡張していきます。

### パーティションを拡張する

`growpart`コマンドでパーティションを拡張します。
※デバイス名とパーティション番号の間にスペースを入れることに注意

```bash
# sudo growpart デバイス名 パーティション番号
$ sudo growpart /dev/xvda 1

# 実行結果
CHANGED: partition=1 start=4096 old: size=16773087 end=16777183 new: size=33550303 end=33554399
```

CHANGEDと返ってきたらOKです。
念のため、もう一度`lsblk`コマンドでパーティションの容量を確認します。

```bash
$ lsblk

# 実行結果
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  16G  0 disk
└─xvda1 202:1    0  16G  0 part /
```

16GBに増えてるので問題なさそうです。


### ファイルシステムを拡張する
パーティションを拡張してもファイルシステムは自動で拡張されないので、
ファイルシステムも拡張していきます。

まずは確認してみましょう。
```bash
$ df -hT

# 実行結果
ファイルシス   タイプ   サイズ  使用  残り 使用% マウント位置
devtmpfs       devtmpfs   474M     0  474M    0% /dev
tmpfs          tmpfs      483M     0  483M    0% /dev/shm
tmpfs          tmpfs      483M  404K  482M    1% /run
tmpfs          tmpfs      483M     0  483M    0% /sys/fs/cgroup
/dev/xvda1     xfs        8.0G  1.6G  6.5G   20% /
tmpfs          tmpfs       97M     0   97M    0% /run/user/1000
```

サイズが8GBのままですね。
ファイルシステムがxfsなら`xfs_growfs`コマンド、ext4なら`resize2fs`コマンドを使って拡張します。
ちなみにAmazon Linux2はxfsです。

#### ファイルシステムがxfsの場合（今回はこっち）
```bash
# sudo xfs_growfs -d マウント位置
$ sudo xfs_growfs -d /

# 実行結果
meta-data=/dev/xvda1             isize=512    agcount=4, agsize=524159 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1 spinodes=0
data     =                       bsize=4096   blocks=2096635, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 2096635 to 4193787
```

#### ファイルシステムがext4の場合
```bash
# sudo resize2fs ファイルシステム名
$ sudo resize2fs /dev/xvda1
```

### 最後に確認
```bash
$ df -hT

# 実行結果
ファイルシス   タイプ   サイズ  使用  残り 使用% マウント位置
devtmpfs       devtmpfs   474M     0  474M    0% /dev
tmpfs          tmpfs      483M     0  483M    0% /dev/shm
tmpfs          tmpfs      483M  404K  482M    1% /run
tmpfs          tmpfs      483M     0  483M    0% /sys/fs/cgroup
/dev/xvda1     xfs         16G  1.6G   15G   10% /
tmpfs          tmpfs       97M     0   97M    0% /run/user/1000
```
ちゃんと16GBに増えましたね。
これで作業は以上です。お疲れ様でした。

# まとめ

手順3のサーバでの作業は、Linuxのデバイスとファイルシステムの深い知識がないとなかなか理解するのが難しいと思いました。
この辺りはLPICレベル2を勉強すれば身につくかもしれません。（あずき本に論理ボリュームの記載あり）

# 参考記事

- [ご存知でしたか？EC2インスタンスは再起動なしにディスクサイズ(EBSボリュームサイズ)を増やせます](https://dev.classmethod.jp/articles/extending-disk-size-without-reboot/)
- [ボリュームサイズ変更後の Linux ファイルシステムの拡張](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html)