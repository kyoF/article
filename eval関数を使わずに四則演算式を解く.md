# はじめに
タイトルにある通りです。
少しだけ詳しく解説します。
最初はeval関数を全く知らず、知ってる知識だけで解いてみようと思ったのがきっかけです。
プログラムが完成した後、どんなアルゴリズムがあるのかなって調べてみたらeval関数なるものを見つけて、あまりにも簡単に答えが出せてしまったので笑ってしまいました。

# 問題
以下の計算式が文字列として与えられたとき、解を求めよ。

```
'(((3+1+6+2+3+1234+234+8)/3)+2*235-442+2312)/9+6*(3)+(1+1+1*5/90)='
```

# 回答プログラム
### 割り算を掛け算に、引き算を足し算に
与えられた式を一つずつ見ていき、以下の表のように対応します。

|変換前の四則演算記号|変換後の四則演算記号|備考|
|:---:|:---:|:---|
|/|*|変換後の数字は分数にする|
|-|+|変換後の数字の前に「-」をつける|

```python
def chg_subtosum_divtomul(formula):
    tmp_list = []
    tmp_num = ''
    div_flg = False
    sub_flg = False
    for index, item in enumerate(list(formula)):
        if sub_flg == True:
            tmp_num += f'-{item}'
            sub_flg = False
            continue
        if div_flg == False:
            if item == '/':
                tmp_list.append(tmp_num)
                tmp_num = ''
                tmp_list.append('*')
                div_flg = True
            else:
                if item == '-':
                    if index != 0:
                        tmp_list.append(tmp_num)
                        tmp_num = ''
                        tmp_list.append('+')
                        sub_flg = True
                    else:
                        tmp_num += item
                    continue
                elif item == '*' or item == '+':
                    tmp_list.append(tmp_num)
                    tmp_num = ''
                    tmp_list.append(item)
                else:
                    tmp_num += item
                if len(list(formula)) == index+1:
                    tmp_list.append(tmp_num)
            continue
        else:
            if item == '+' or item == '*':
                tmp_list.append(str(1/float(tmp_num)))
                tmp_num = ''
                tmp_list.append(item)
                div_flg = False
            else:
                tmp_num += item
                if len(list(formula)) == index+1:
                    tmp_list.append(str(1/float(tmp_num)))
            continue
    return ''.join(tmp_list)
```

### 掛け算をして、足し算をする
はじめに掛け算をして、最終的に足し算をした結果を出力します

```python
def cal_sum_and_mul(formula):
    flg = False
    for index, item in enumerate(formula.split('*')):
        cal_num = ''
        number = item
        if item.find('+') != -1:
            number = item[:item.find('+')]
        if index != 0:
            cal_num = str(float(oneback_item) * float(number))
            formula = formula.replace(f'{oneback_item}*{number}', cal_num)
            if item.find('+') == -1:
                flg = True
        if flg == True:
            oneback_item = cal_num
        else:
            if item.find('+') == -1:
                oneback_item = item
            else:
                oneback_item = item[item.rfind('+')+1:]
    return str(sum(map(float, formula.split('+'))))
```

### 上記の2つの関数を呼び出す
正直ここは書かなくても大丈夫だと思います。

```python
def cal_formula(formula):
    formula = chg_subtosum_divtomul(formula)
    return cal_sum_and_mul(formula)
```

### 括弧から優先的に計算
for文で回している部分は、式の中にある括弧の中身を計算して、最終的に括弧がない状態の式を取り出します。
最後に、括弧が無くなった式を計算して、結果を出力します。

```python
formula = '(((3+1+6+2+3+1234+234+8)/3)+2*235-442+2312)/9+6*(3)+(1+1+1*5/90)='
formula = formula.replace('=','')

for _ in range(int(formula.count('('))):
    calculate_formula = formula[formula[0:formula.find(')')].rfind('('):formula.find(')')+1]
    tmp_formula = cal_formula(calculate_formula.replace('(', '').replace(')', ''))
    formula = formula.replace(calculate_formula, tmp_formula)

print(float(cal_formula(formula)))
```

# おわりに
もしかしたら他の式でやったらちゃんと答えが出ないかもしれませんが、多分大丈夫だと思います。
~~このプログラムを作成するのに時間かかってしまったので、他の式で確認なんてしたくないです。~~

三項演算子とかを使うと、もっとプログラムが簡潔で見やすくなると思います。
あとフラグとか付けてるのでこういうのも無くしていきたいです。
if文もとんでも無く多いのでこれも少なくなるのであれば少なくしたいですね。

最後にeval関数を使った場合のプログラムは下記になります。

```python
formula = '(((3+1+6+2+3+1234+234+8)/3)+2*235-442+2312)/9+6*(3)+(1+1+1*5/90)='
print(eval(formula.replace('=','')))
print('俺の努力は一体なんだったんだ...')
```

最後まで見ていただいた方、ありがとうございました。

