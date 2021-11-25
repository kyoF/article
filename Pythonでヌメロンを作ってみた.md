## はじめに

前回、じゃんけんプログラムを作成しました。
その記事を書きながらヌメロンを作っていました。
じゃんけんと同じく、解説しながら残しておきたいと思います。

そういえば前回のじゃんけんプログラム、pythonの有識者の方から、より洗礼されたコードによってめちゃくちゃ短くなりました。
こんな短くなるとは。
感動です。

さて、最初の語りはこれぐらいにして、早速本題です。

## ヌメロンとは

ヌメロンとは、数当てゲームです。
相手が選んだ３桁の重複していない数字と、その数字の場所（桁）まで当てるのが勝利条件です。

ですが、ただ当てずっぽうに数字を選ぶだけでは運ゲーになってしまいます。
そこで、EATとBITEという数を分析しながら、相手が選んだ数字を当てていきます。

EATとは、選択した数字と桁の位置まで同じときに1カウント
BITEとは、選択した数字が同じで、桁の位置が違うときに1カウント
となります。

例えば、相手が「123」と数字を選んだ場合、以下のようになります。

```
相手：「123」
「452」：0eat/1bite
「167」：1eat/0bite
「152」：1eat/1bite
「312」：0eat/3bite
「132」：1eat/2bite
「123」：3eat/0bite
```

3eatとなったら勝ちです。

以上が基本的なルールです。


## 環境
MacOS
Python 3.9.1

## 動かし方
前回と同じです。
1. ローカルファイル(numeron.py)を用意して、ソースコードをまるまるコピー
1. ターミナルを開いて以下のどちらかのコマンドを打つ

```
$ python numeron.py
or
$ python3 numeron.py
```

select digit
桁数を入力する（３桁か４桁か）
type ○ number
3桁か4桁の数字を入力する（同じ数字を入れたり、文字を入力したり、選択した桁数と一致しなかったら入力し直し）


## ソースコード

```numeron.py
import random

eat = 1 # eatフラグのindex番号
bite = 2 # biteフラグのindex番号
win_flg = False # 勝利フラグ
history_list = []
loop_count = 0

digit_flag = False # 桁数が正しく入力されているかのフラグ
while digit_flag == False:
    digit = input('select digit:')
    if digit == '3':
        int_digit = 3
        digit_flag = True
    elif digit == '4':
        int_digit = 4
        digit_flag = True
    else:
        print('please select 3 or 4')
        continue

# 敵の数字生成
enemy_list = []
while len(enemy_list) < int_digit:
    n = random.randint(0, 9)
    if not n in enemy_list:
        enemy_list.append(n)

# 勝つまで繰り返し
while win_flg == False:

    eat_count = 0 # EATカウント
    bite_count = 0 # BITEカウント
    i = 0 # LOOP変数
    my_list = []
    history_list.append([])
    tmp_my_list = []

    input_flg = False # 正しい数字が入力されているかのフラグ
    while input_flg == False:

        num = input(f'type {digit} number:')

        # 整数チェック
        try :
            int(num)
            tmp_my_list = list(num)
        except ValueError:
            print('type number')
            continue

        # 桁数チェック
        if not len(tmp_my_list) == int_digit:
            print('match digit')
            continue

        # 同じ数字が入力されていないかチェック
        if not len(sorted(set(tmp_my_list), key=tmp_my_list.index)) == int_digit:
            print("don't type same number")
            continue
        
        # 判定に使用するリストと履歴として使用するリストに代入
        for num in tmp_my_list:
            my_list.append([])
            my_list[i].append(int(num))
            # eat_flag
            my_list[i].append(0)
            # bite_flag
            my_list[i].append(0)
            # 記録
            history_list[loop_count].append(my_list[i][0])
            i += 1

        input_flg = True

    # EATか判定
    for (my_item, enemy_item) in zip(my_list, enemy_list):
        if my_item[0] == enemy_item:
            eat_count += 1
            my_item[eat] = 1

    # BITEか判定
    for my_item in my_list:
        for enemy_item in enemy_list:
            if my_item[eat] != 1:
                if my_item[0] == enemy_item:
                    bite_count += 1
                    my_item[bite] = 1
    
    # 履歴にEATとBITEを記録
    history_list[loop_count].append(eat_count)
    history_list[loop_count].append(bite_count)

    # EATが桁数と同じになったら終了
    if eat_count == int_digit:
        win_flg = True

    # 敵の数字
    # print('-----enemy hand----')
    # print(*enemy_list, sep='-')
    # 自分の状況
    print('-----your hand-----')
    for print_list in history_list:
        print(*print_list[0:int_digit], sep='-')
        print(f'<EAT:{print_list[int_digit]}>')
        print(f'<BITE:{print_list[int_digit+1]}>')
        print('-------------------')

    # 何回目で勝利したかカウント
    loop_count += 1

print('WIIIIIIIIIIIIN!!!')
print(f'you won in {loop_count} turn')
```

## 結果

```
$ python numeron.py

# =>
select digit:3
type 3 number:012
-----your hand-----
0-1-2
<EAT:0>
<BITE:0>
-------------------
type 3 number:345
-----your hand-----
0-1-2
<EAT:0>
<BITE:0>
-------------------
3-4-5
<EAT:0>
<BITE:2>
-------------------
```

勝つまで続き、勝ったら以下のように出力されます。

```
-----your hand-----
4-5-6
<EAT:3>
<BITE:0>
-------------------
WIIIIIIIIIIIIN!!!
you won in 6 turn
```

select digitでは、３と4を入力することができます。
3と入力すると３桁、4と入力すると4桁でヌメロンができます。

## 解説
### 初期化

```numeron.py
import random

eat = 1 # eatフラグのindex番号
bite = 2 # biteフラグのindex番号
win_flg = False # 勝ったかどうかの判定
history_list = [] # 自分が選んだ数字とeatとbiteを格納
loop_count = 0 # 自分が
```

最初に初期化をしています。
eatやbiteは、自分の数字情報を格納しているmy_listのインデックス番号です。

先に説明すると、今回用意したリストは3つです。

```
my_list
　　自分のターンで入力した数字と、EATやBITEがあるか
enemy_list
　　敵の数字
history_list
　　今まで入力した数字と、EAT、BITEの数
```

my_listとhistory_listは、2次元配列になっています。
3桁の場合は以下のような感じです。

```
my_list [
[数1, 数1に対してeatがあるか判定, 数1に対してbiteがあるか判定]
[数2, 数2に対してeatがあるか判定, 数2に対してbiteがあるか判定]
[数3, 数3に対してeatがあるか判定, 数3に対してbiteがあるか判定]
]

enemy_list
[敵の数1, 敵の数2, 敵の数3]

history_list [
[1ターン目の数1, 1ターン目の数2, 1ターン目の数3, 1ターン目のeatの数, 1ターン目のbiteの数]
[2ターン目の数1, 2ターン目の数2, 2ターン目の数3, 2ターン目のeatの数, 2ターン目のbiteの数]
:
]
```

ここでは、はじめにhisotory_listを初期化しています。

### 桁数の選択

次は、３桁か４桁のどちらでヌメロンをプレイするかを決定しています。

```numeron.py
digit_flag = False # 桁数が正しく入力されているかのフラグ
while digit_flag == False:
    digit = input('select digit:')
    if digit == '3':
        int_digit = 3
        digit_flag = True
    elif digit == '4':
        int_digit = 4
        digit_flag = True
    else:
        print('please select 3 or 4')
        continue
```

3か4という入力しか許さず、他の入力値が入ってきた場合は、もう一回3か4を入力させるようにしました。
3か4が入力された場合、int_digitにint型に変換した桁数を代入します。

### 敵の数字生成

```numeron.py
enemy_list = []
while len(enemy_list) < int_digit:
    n = random.randint(0, 9)
    if not n in enemy_list:
        enemy_list.append(n)
```

桁数の選択で入力した桁数に応じて、敵の数字を取得します。
0から9のランダム値をリストに代入し、同じ数字があった場合は、パスするようにしています。


ここの処理を書いて少し疑問に思ったのは、while文とfor文の使い分けです。
pythonだと、rangeなどで回す回数を指定しますが、回す回数のカウントアップやカウントダウンとかができないと思っています。

例えば

```python
enemy_list = []
for _ in int_digit:
    n = random.randint(0, 9)
    if n in enemy_list:
        int_digit += 1:
        continue
    enemy_list.append(n)
```

こう書いてしまうと
「int_digitはイテラブルじゃないよ」と怒られてしまいます。
for文の部分をrange(1, int_digit)とかにしてやってみましたが、あんまりうまくいきませんでした。

こういう時は普通にwhile文をどんどん使っていけばいいんですかね。
実際、for文より短くかけてますし。

ただ、他の言語では、while文よりかは、for文で書いているプログラムが結構多い印象があるので、なんか不思議な感覚なんです。

と、ちょっと引っかかった部分でした。
次にいきましょう。

### 相手の数字と一致するまで入力を繰り返す部分の処理
#### 初期化

```numeron.py
while win_flg == False:

    eat_count = 0 # EATカウント
    bite_count = 0 # BITEカウント
    i = 0 # LOOP変数
    my_list = []
    history_list.append([])
    tmp_my_list = []
```

hisotory_listに新しい配列を作成しています。
あとは普通に初期化しています。

#### 数値と形式チェック


```numeron.py
input_flg = False # 正しい数字が入力されているかのフラグ
    while input_flg == False:

        num = input(f'type {digit} number:')

        # 整数チェック
        try :
            int(num)
            tmp_my_list = list(num)
        except ValueError:
            print('type number')
            continue

        # 桁数チェック
        if not len(tmp_my_list) == int_digit:
            print('match digit')
            continue

        # 同じ数字が入力されていないかチェック
        if not len(sorted(set(tmp_my_list), key=tmp_my_list.index)) == int_digit:
            print("don't type same number")
            continue

        # 判定に使用するリストと履歴として使用するリストに代入
        for num in tmp_my_list:
            my_list.append([])
            my_list[i].append(int(num))
            # eat_flag
            my_list[i].append(0)
            # bite_flag
            my_list[i].append(0)
            # 記録
            history_list[loop_count].append(my_list[i][0])
            i += 1

        input_flg = True
```

はじめに、指定した桁数の数字を入力させます。

それをint型にできるか、list型にできるかを判定しています。
できなかった場合、「type number」と表示されて、入力画面からやり直しです。

上記がパスしたら、桁数のチェックです。
指定した桁数と同じ桁数かどうかを判定します。
違う桁数の場合は、同じくエラーメッセージを表示して、入力画面に戻ります。

最後に、同じ数字が入力されていないかのチェックです。
この部分は考えてません。Googleからコピペしました。笑
だからと言って説明しないのは自分の力にならないので、記事を書きながら調べました。

はじめに、set関数でtmp_my_listを集合にします。集合は重複した値がないオブジェクトのため、重複がないオブジェクトに変更されます。
その後、sorted関数で、keyに元のリストオブジェクトのインデックスを参照することで、重複が消えたリストが取れるわけです。

これを、桁数と比較して、桁数が同じじゃなければエラー表示して入力画面に戻ります。

ここまでチェックしたあと、ようやくmy_listやhistory_listにappendされます。
my_listは二次元配列のため、リストの中にリストを作成して、その中に数と、eat判定フラグとbite判定フラグを入れます。
history_listにもappendします。

最後にinput_flgをTrueにして、while文を抜け出します。

#### EAT・BITE判定

```python
# EATか判定
for (my_item, enemy_item) in zip(my_list, enemy_list):
    if my_item[0] == enemy_item:
        eat_count += 1
        my_item[eat] = 1

# BITEか判定
for my_item in my_list:
    for enemy_item in enemy_list:
        if my_item[eat] != 1:
            if my_item[0] == enemy_item:
                bite_count += 1
                my_item[bite] = 1

# 履歴にEATとBITEを記録
history_list[loop_count].append(eat_count)
history_list[loop_count].append(bite_count)
```

はじめにEATかどうかを判定します。
自分の数字と相手の数字の配列の長さが一致しているので一緒にfor文を回して判定します。
同じインデックスの数字が一緒ならEATカウントをプラス1し、my_itemのeatフラグを立てます。

my_listが二次元配列なのに、一次元配列のenemy_listと一緒に回しても大丈夫なのが今見ると不思議ですね。

次に、BITEかどうかを判定します。
これは、自分の一つ目の数字を、相手の数字と一つずつ見ていきます。
ここで、EATフラグがなく、同じ数があった場合は、BITEカウントをプラス1し、my_listのbiteフラグを立てます。

うまく動いていますが、EATフラグが立っていない場合は、一つ目のfor文をもう一回していることになるので、無駄な処理がある感じですね。
ここの修正はちょっと難しそうです。
あと、biteフラグは、eatフラグを作るときに一緒に作ったのですが、特に使用していないので本当は要らないはずです。
今見るとここも無駄です。

最後にhistory_listにEATカウントとBITEカウントを入れてます。

#### 出力処理とその他

```numeron.py
# 敵の数字
# print('-----enemy hand----')
# print(*enemy_list, sep='-')
# 自分の状況
print('-----your hand-----')
for print_list in history_list:
    print(*print_list[0:int_digit], sep='-')
    print(f'<EAT:{print_list[int_digit]}>')
    print(f'<BITE:{print_list[int_digit+1]}>')
    print('-------------------')

# 何回目で勝利したかカウント
loop_count += 1

# EATが桁数と同じになったら終了
if eat_count == int_digit:
    win_flg = True

```

最後に、今まで入力した数字とeatの数、biteの数を全部出力します。
コメントアウトしているところはデバッグ用です。
何ターン目かをカウントする変数をカウントアップして、次の数字を入力する画面に戻ります。

最後の処理は、eatカウントと入力した桁数が一致した場合、勝利フラグをtrueにして、while文を抜けることができます。

### 勝利したときの出力

```python
print('WIIIIIIIIIIIIN!!!')
print(f'you won in {loop_count} turn')
```

最後に勝利したときの出力です。
勝利メッセージと何ターンで勝ったかが表示されます。

## まとめ
普通に楽しかったです。
あと勉強にもなりました。

次は何を作ろうか迷っています。

今研修ではフレームワークを使ってウェブアプリケーション作成のための勉強をしているので、復讐がてら一つアプリケーションを作ってみたいのですが、どういうものを作ればいいか迷っています。

ちょっと考えているのが、スクレイピングした結果をhtml画面に出力するやつです。
特にデータを保存する必要がなく、起動したらスクレイピングを行って、最新の情報が手に入る的な処理にすれば、意外と行けるかもしれないと思っています。

あとは、勤怠管理するwebアプリです。
昔、勤怠管理システムのアルバイトをしていたので、同じようなものを作ってみたいなという思いがありました。
なので、今、ちょうどできる環境にある気がするので、その夢を叶えるチャンスかもしれません。

どちらにしよ、作成するにはちょっと時間がかかる気がしますので、次の記事はちょっと先になりそうです。

それではこの辺で。

最後まで見てくださった方は本当にありがとうございました。


