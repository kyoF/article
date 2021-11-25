

## はじめに

初投稿です。

今年入社した新卒エンジニアです。

今は研修中なのですが、今までアルバイトでもプログラムをしてきたことで、研修でつまづくことがほぼ無くなっています。
そんな時間がもったいないと思い、何か空いている時間で作ろうと思い立った時に、じゃんけんプログラムでも作るかとなったのがきっかけです。

めちゃくちゃ簡単なプログラムですが、備忘録的に何かに残しておきたいと思い、ここに投稿することにしました。

他の方に少しでも力になればと思い、かなり詳しく説明して残しておきたいと思います。

## 目次
次からちゃんと用意します。

## 環境
MacOS 
Python 3.9.1

1. ローカルにファイル(janken.py)を用意して、ソースコードをまるまるコピーします。
1. ターミナルを開いて以下のどちらかのコマンドを打てばできると思います。

```
$ python janken.py
or
$ python3 janken.py
```


## ソースコード
```janken.py
import random
import sys

select = [[1,'グー'], [2, 'チョキ'], [3, 'パー']]
for i in range(len(select)):
    print('%s：%s' %(i+1, select[i][1]))

cpu = random.randint(1, 3)
try:
    myslf = int(input('1～3を選択してください：'))
except ValueError:
    print("1～3を入力してください")
    sys.exit()

def prnt():
    print('自分：' + select[myslf-1][1])
    print('相手：' + select[cpu-1][1])

def win():
    print('勝ちです')

def lose():
    print('負けです')

if myslf == cpu:
    prnt()
    print('あいこです')
else:
    if myslf == 1:
        if cpu == 2:
            prnt()
            win()
        else:
            prnt()
            lose()
    elif myslf == 2:
        if cpu == 1:
            prnt()
            lose()
        else:
            prnt()
            win()
    elif myslf == 3:
        if cpu == 1:
            prnt()
            win()
        else:
            prnt()
            lose()
    else:
        print('1～3を入力してください')

```

## 結果

```
$ python janken.py
1：グー
2：チョキ
3：パー
1〜3を選択してください：1

# => 自分：グー
相手：パー
負けです
```


## 詳しい解説

```python
import random
import sys
```

randomはじゃんけん相手の手を決める際に使います。

sysは、inputでじゃんけんに対応する数字を入力する際に、こちらが望んだ数字以外が入力された時に終了するためにimportしました。



```python
select = [[1,'グー'], [2, 'チョキ'], [3, 'パー']]
for i in range(len(select)):
    print('%s：%s' %(i+1, select[i][1]))
```

次に、selectを２次元配列として1にグー、2にチョキ、3にパーを入れました。
今改めて見ると、２次元にする必要あったのかなって思いますね。
普通にリストとか辞書型として扱っても何も問題なさそうです。
何日か研修を受けただけですが、成長するものです。

次に、for文でselectの要素ぶん回してprintさせてます。
これも

```janken.py
print(f'{str(i+1)}:{select[i][1]}')
```

とかの方がわかりやすい気もします。
エンジニアの方、どっちの方がよく使うでしょうか？笑

```python
cpu = random.randint(1, 3)
try:
    myslf = int(input('1～3を選択してください：'))
except ValueError:
    print("1～3を入力してください")
    sys.exit()
```

ここでは、変数cpuに1から３のランダムな整数を代入します。

次に、変数myslfに、ターミナル上から入力した数字をint型に変換して代入を行います。
この時、int型に変換できない文字が入力された場合、エラーメッセージを入力して処理を終了する挙動になってます。
なぜここのメッセージ表記だけダブルクォーテーションが使用されているのでしょうか。
謎ですね。

```python
def prnt():
    print('自分：' + select[myslf-1][1])
    print('相手：' + select[cpu-1][1])

def win():
    print('勝ちです')

def lose():
    print('負けです')
```

ここでは関数を定義してます。
prnt関数は、自分が出した手と相手が出した手を、selectから出力します。
win関数は勝ち、lose関数は負けを出力します。

```python
if myslf == cpu:
    prnt()
    print('あいこです')
else:
    if myslf == 1:
        if cpu == 2:
            prnt()
            win()
        else:
            prnt()
            lose()
    elif myslf == 2:
        if cpu == 1:
            prnt()
            lose()
        else:
            prnt()
            win()
    elif myslf == 3:
        if cpu == 1:
            prnt()
            win()
        else:
            prnt()
            lose()
    else:
        print('1～3を入力してください')
```

これは、以下のようになります。

```
if 自分の手と相手の手が同じ場合:
    あいこ
else:
    if 自分がグー:
        if 相手がチョキ:
            勝ち
        else（相手がパー）:
            負け
    elif 自分がチョキ:
        if 相手がグー:
            負け
        else（相手がパー）:
            勝ち
    elif 自分のパー:
        if 相手のグー:
            勝ち
        else（相手がチョキ）:
            負け
    else:
        １〜３が入力されていない場合の処理
```

以上です。


## まとめ
こんなめちゃくちゃ簡単なプログラムを、こんな詳しく解説して需要があるのかと思ったのが、記事を書いてみた正直な感想です。
でも自分の備忘録を兼ねているのでいいのです。なんでも。

次は、ヌメロンを作ったので、そのコードを記事にしたいします。
ヌメロンはこんな詳しく解説しないかもしれません。

あと、マークダウン形式で記事を書くのが初めてでした。
見づらい部分があったのは申し訳ないです。

これから、色々な人のqiitaの記事を見て、書き方を学んでもうちょっといい感じにしていきたいと思います。

最後まで読んでくださった方、本当にありがとうございました。
