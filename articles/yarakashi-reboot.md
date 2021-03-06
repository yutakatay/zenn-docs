---
title: "俺TUEEE系エンジニアが本番サーバーを0.1秒で落とした話"
emoji: "☄"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["linux"]
published: true
---

***凄惨度：低***
***またやらかす確率：中***

## 俺TUEEE系エンジニアとは

***※ ここの章は独断と偏見がかなり入ってるのでネタとして読んでください***

俺TUEEE系エンジニアとは、
「まあどんなセキュリティもオレには全然関係ないけど」(出典：[BLOODY MONDAY](https://dic.pixiv.net/a/BLOODY_MONDAY))
ウィザード級ハッカー(出典：[ne0;lation](https://dic.pixiv.net/a/ne0%3Blation))
等に代表されるようなある種の厨二的価値観を捨てきれずに社会人になってしまったエンジニアです。
俺TUEEE系エンジニアは基本的に虚栄心があって傲慢です。だいたい自分の開発環境に酔いしれてます。生産性を高めて、作業のスピードを上げ、他の人に一目置かれることを生きがいとしています。
まあざっくりいうと俺TUEEE系エンジニアとは、なんとなくハッカーを気取りたい、人と同じものを使いたくないエンジニアのことです。

俺TUEEE系エンジニアは、だいたい自分の作業用PC（クライアントPC）を完全にLinuxにしていることが多いです。しかし、ただLinuxを入れてるだけだと俺TUE系なので、ArchLinuxにします。これで俺TUEE系になれます。さらにデスクトップ環境をi3(sway,awesome)等のタイル型にします。これで晴れて俺TUEEE系エンジニアになれました。俺TUトリプルEですね。
俺TUEEE系エンジニアは基本ターミナル内で生活しています。マウスはあまり使わないです。キーボードはだいたいHHKBかKinesis Advantage2かRealforceかThinkpadの外付けか自作を使ってます。

***そう私です***。

俺TUEEE系エンジニアは、いつも効率を意識してるので、最小の手数で物事を処理していきます。
そんな俺TUEEE系でもやらかすときはやらかしてしまうんですよね〜こわい世界だ。
**やらかしはあなたのすぐ後ろにいる**。

## 事案発生までの記録

俺TUEEE系エンジニアの一日はまずパッケージ更新から始まる。なぜなら新しいものが大好きだからだ。新しいものを使うとバグに出会う確率もあがる。自分の作るバグは嫌いだが、他人の作るバグは好きだ。バグったらマジかよーって言いながらもその目はラキラと輝いている。

今日は何が更新されているのか、ArchLinuxのパッケージ更新コマンドを打って `yay -Syyu` っと。
💬（おお、今日はカーネルの更新もあるようだな。もうバージョン5.8かw。Linusもがんばってるな）

その後は効率的に業務のIssueをこなしていく。本番サーバーでやらなくてはいけない作業もあったので、tmuxの新しいウィンドウを開いてsshでログインして作業を開始。
💬（シェルの起動スピードもうちょっと速くしたいな。あーあと、ここらへんは自動化したいなー）

とりあえず作業も一段落したので、ちょっと[vim-jpのslack](https://vim-jp.org/docs/chat.html)でも覗くか。
💬（今日はどのデフォルトmapを潰せるかで盛り上がってるのか。こいつらほんとこういうの好きだよな〜）

💬（あ、そういえば今日カーネルの更新きてたな。このあとの作業は長くなりそうだし、区切りもいいから一旦再起動しておくか）

i3(Linuxのデスクトップマネージャー)で終了は`Win+Shift+e`を押してメッセージが表示されてから
![i3exit](https://user-images.githubusercontent.com/8683947/100548771-fa027f80-32b1-11eb-9b4c-c9e9d2e8c90f.png)
`r` で再起動
💬（ってめんどくさいな！）

まあ普通にそこにあるターミナルから再起動しよう。
`sudo reboot`っと
ん？あれ？再起動しないぞ？？

***そう再起動したのは本番サーバーだったのです。***

## 惨劇はなぜおこってしまったのか

単純にコマンドを打つ場所を間違えたというよくありがちなしょーもないミスです。気をつけるようにはしていたのですが、集中力が切れたところでやられてしまいました。一応クライアントのプロンプトと本番プロンプトは違う見た目にはなっていましたが、目立ってはいなかったのでミスを防ぐには至りませんでした（文字を読む前に神速のタイピングでコマンドとEnterの入力が終了していた）。

原因としては**油断と慣れ**があったと思います。
特にArchLinuxの場合がカーネルが高頻度で更新されるのでクライアントPCの再起動を結構行います。しかも、なんとなく作業のキリがいいからという中途半端なタイミングで実施してしまったがために、本番サーバーとのセッションが残ったままのssh上で実行してしまうことになってしまいました。

## 復旧作業

幸いなことに再起動してしまったサーバーは、プログラムはcronのように一定時間で起動するものだったため大事には至りませんでした。
OSの再起動、サービスの起動を確認して、報告書(まあいわゆる始末書)を書いて事態は収まりました。

## 二度と惨劇を起こさないためにどうしたらいいか

### 本番サーバーのターミナルの背景色を他と変えて認識しやすくしておく(必須)

#### 本番サーバー側に軽い設定を入れるパターン

他の人がミスらないためにも、サーバー側でひと手間加えてあげるのが一番よいと思います。
どちらか一方は必ずやるようにしましょう。

##### プロンプトの見た目をド派手に変える

ssh先の~/.bashrcのPS1を変更してプロンプトをわかりやすく変更します。ちょっと変えただけだと気づかないことがあるのでめちゃくちゃ目立たせるといいでしょう。

↓もっとおしゃれなものにしても良いと思いますが目立つのが大事です！
色の付け方については、[こちらを参考のこと](https://qiita.com/hmmrjn/items/60d2a64c9e5bf7c0fe60)

```bash
PS1="[\[\e[37;41;1m\]prrrrrrrrrrrrrrrrrodunction\[\e[37;49m\] \u@\h \W]\$ "
```

サンプル
![2020-12-06_04-18_1](https://user-images.githubusercontent.com/8683947/101261377-29b1fb80-377a-11eb-8a28-86001688664e.png)

##### 背景を自動で変える

`~/.bashrc`に

```bash
echo -e "\033]11;#400000\a"
```

`~/.bash_logout`に

```bash
reset
```

を設定して、ログインしたら強制的にターミナルの背景色を変更します。

#### 本番サーバーは不可侵領域だから自分のPCだけでなんとかするパターン

サーバー側で必要ない(?)設定を入れることはまかり通らん！って環境に住んでいる人は、他の人は救えないですが、***最低限自分だけでも守りましょう***。
作業するターミナルの背景色を動的に変える方法もありますが、個人的におすすめなのはターミナルアプリ自体を変えてしまう方法です。

##### 作業するターミナルアプリ自体を変える方法

メインじゃない方のターミナルアプリの背景をドギツイ色になるように、ターミナルの背景設定から変更します。

![2020-12-06_04-25](https://user-images.githubusercontent.com/8683947/101261532-67635400-377b-11eb-94df-411d2fbea0b5.png)

##### 作業するターミナルの背景色を動的に変える方法

- tmuxで動的にpaneの色を変える方法 https://bacchi.me/linux/change-terminal-bgcolor/
- sshを関数で上書きして動的に変更する方法(~/.bashrcなどに追記してください)

```bash
function ssh () {
  echo -e "\033]11;#400000\a"
  command ssh "$@"
  reset
}
```

##### 作業する前にPS1を書き換える方法

あまりおすすめしませんが、sshしたあと作業開始する前にプロンプトを再設定する方法もあります。他の人に影響なく自分のプロンプトだけを一時的に変更します。当然ですが、ログアウトしてまたログインすれば元に戻っています。
OSのクリップボードにスニペットとして保存しておくなどしたらよいと思います。ただし、打ち忘れたら意味がないので注意が必要です。

PS1の設定は[プロンプトの見た目を変える](#プロンプトの見た目を変える)と同様です。

### 他にできること

- クライアントPCを再起動するときはGUIの機能で行う。安易に`sudo reboot`しない
- 本番サーバーでの作業時間を最小にすること。作業がなくなったらきちんとログアウトすること
- rmとかddのようにあからさまに怖いコマンドでないものの中にも、危険はコマンドがあることを理解すること

### 他にもっとやっておいてほしいこと

本番環境にsshしなくていいように、IaC(Infrastructure as code)や CD(Continuous delivery)、システムモニター環境を整えるのが必須だと思います。今まさに本番環境にsshしてるなら、できるだけ早くそこから脱却する仕組みを作り込んだほうがよいですね。
ただ、会社の規模や環境によってはそれができてなかったり、おざなりになってたりすると思いますでそのときはくれぐれもお気をつけて・・・！

## 俺TUEEE系エンジニアで居続けるために

普段の行いにもよりますが、周りから「俺TUEEEなのにこんなミスすんだ」って思われてる気がします。プライドは高いので、すごく傷つきます。俺TUEEE系エンジニアでいたいのであれば絶対に凡ミスをしないように作業環境を作り込んだほうがよいですね。。。

本来の意味で ***「俺またなんかやっちゃいましたか？」*** にならないように・・・

