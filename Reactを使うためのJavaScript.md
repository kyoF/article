# はじめに
この記事は、Reactを触る上で必要なJavaScriptの基本知識をいくつか紹介します。

`if`や`for`は扱える上で、JavaScript特有の書き方などをつらつら書いています。

今はJavaScriptよりTypeScriptの方が主流ですが、TypeScriptもJavaScriptから派生した言語なので、ここら辺の基礎知識はTypeScriptを扱う上でも大切になってくると思います。

これからReactを触る方が、少しでも参考になれば幸いです。

# 基本知識
## var・const・let
今までは、変数・定数はすべて`var`で宣言していたものを、`const`を定数、`let`を変数として扱えるようになりました。
ここで言いたいことは、「`var`は使わないようにしよう」です。

下記のコードでは、`const`を使用して、書き換えが出来ないようにしています。

```javascript
// varの場合
var js = true;
js = false;
console.log(js); // false

// constの場合
const js = true;
js = false; 
console.log(js); // Uncaught TypError: ...
```
下記のコードでは、`if`の中で、外側と同じ変数を`var`で二重に宣言し、意図していない動きをしています。
これは、`let`キーワードを使用することで解決できます。

```javascript
// varの場合
var js = "javascript";
if (js) {
  var js = "java";
  console.log(js); // java
}
console.log(js); // java

// letの場合
var js = "javascript";
if (js) {
  let js = "java";
  console.log(js); // java
}
console.log(js); // javascript
```

また、`var`と`let`では以下のような違いもあります。

```javascript
// varの場合
var div;
var container = document.getElementById("container");
for (var i = 1; i <= 5; i++) {
  div = document.createElement("div");
  div.onclick = function () {
    alert("i");
  };
  container.appendChild(div);
}

// letの場合
var div;
var container = document.getElementById("container");
for (let i = 1; i <= 5; i++) {
  div = document.createElement("div");
  div.onclick = function () {
    alert("i");
  };
  container.appendChild(div);
}
```
上記の違いは、`for`文で利用する`i`を、`var`と`let`どちらで宣言しているか、だけです。
このプログラムで生成される`div`をクリックすると、`var`で宣言した場合ではどの`div`要素でも`6`が、`let`で宣言された場合では生成された順番の整数値が表示されます。

## 文字列

変数と文字列を一文で表す方法は、主に二通りの方法があります。
加算演算子を用いる方法と、バッククォーテーションを用いる方法です。

```javascript
const java = "java";
const script = "script";

// 換算演算子を用いた場合
console.log("This language is " + java + script + "!!"); // This language is javascript!!

// バッククォートと${}を用いた場合
console.log(`This language is ${java}${script}!!`); // This language is javascript!!
```
下記のコードのようにバッククォートを用いると、改行も認識してくれます。
メール文面のテンプレートを作成したり、`HTML`を記述したりするのに便利です。

```javascript
const js = "javascript";
console.log(`
  This
  language
  is
  ${js}
  !!
`);
  // This
  // language
  // is
  // javascript
  // !!
```

## 関数
関数は、`function`キーワードとともに作成します。関数名を呼び出すことで、関数を実行できます。

```javascript
function test() {
  console.log("javascript");
}
test(); // javascript
```
JavaScriptでは変数の中に関数を入れることが出来ます。これを関数式と言い、下記のコードは上記のものと全く同じです。

```javascript
const test = function() {
  console.log("javascript");
};
test();  // javascript
```
また下記コードのように、関数に引数を設定する場合、デフォルト値を設定することが出来ます。

```javascript
const test = function(name="javascript") {
  console.log(`My name is ${name}.`)
};
test(); // My name is javascript.
test("ruby"); // My name is ruby.
```
関数の引数に関しては、文字列だけでなく他のデータ型も指定することが出来ます。

```javascript
const defaultPersona = {
  name: {
    firstname: "hoge",
    lastname: "fuga"
  },
  old: 22
};
function searchPersona(persona=defaultPersona) {
  console.log(`${persona.name.firstname} ${persona.name.lastname} is ${persona.old} years old.`);
  // hoge fuga is 22 years old.
}
```

### アロー関数
JavaScriptには、`function`や`return`なしで関数を記述できるアロー関数が存在します。
アロー関数を定義する際には`=>`を用い、これが矢に見えることからアロー関数と呼ばれています。

アロー関数を使うことで、関数を一文で記述することが出来ます。
関数の戻り値が単一の式であらわされる場合は`return`文も不要になります。

```javascript
// 関数式の場合
const test = function(name) {
  return `My name is ${name}.`;
};
console.log(test("javascript")); // My name is javascript.

// アロー関数の場合
const test = name => `My name is ${name}.`;
console.log(test("javascript")); // My name is javascript.
```
また、引数が二つの場合は、二つの引数をカッコで囲む必要があります。

```javascript
const test = (firstname, lastname) => `My name is ${firstname} ${lastname}.`;
console.log(test("hoge", "fuga")); // My name is hoge fuga.
```
アロー関数でオブジェクトを戻り値とする関数を作成することもできます。

```javascript
const test = (firstname, lastname) => ({
  firstname: firstname,
  lastname: lastname
});
console.log(test("hoge", "fuga")); // { firstname: 'hoge', lastname: 'fuga' }
```
気を付けるべきポイントとして、戻り値を`()`でくくる必要があります。
オブジェクトを返すアロー関数を一文で記述するときは、戻り値を`()`で囲みましょう。

最後にアロー関数のスコープについてです。実際にコードを確認したほうが分かりやすいと思います。

```javascript
// print関数とsetTimeout内の関数をfunctionで実装
const test() = {
  languageList = ["javascript", "java", "ruby", "python", "C"],
  print: function(seconds = 10) {
    setTimeout(function() {
      console.log(this.languageList.join(", ")); // TypeError: Cannot read property 'join' of undefined
    }, seconds);
  }
};

// setTimeout内の関数をアロー関数に変更
const test() = {
  languageList = ["javascript", "java", "ruby", "python", "C"],
  print: function(seconds = 10) {
    setTimeout( () => {
      console.log(this.languageList.join(", ")); // javascript, java, ruby, python, C
    }, seconds);
  }
};

// print関数もアロー関数に変更
const test() = {
  languageList = ["javascript", "java", "ruby", "python", "C"],
  print: (seconds = 10) => {
    setTimeout(function() {
      console.log(this.languageList.join(", ")); // TypeError: Cannot read property 'join' of undefined
    }, seconds);
  }
};
```
上記のコードで正しい出力が得られるのは`setTimeout`内の関数をアロー関数にした場合のみです。
理由は、アロー関数は通常の関数とは違い、独自のスコープを持たないため、アロー関数の外側と内側でスコープが保持されます。
結果的に`this`の指すものは`print`関数の呼び出しコンテキストである`test`になります。

スコープの部分に関しては、自分も詳しく分かっていないので、これから使っていく内に分かることを期待します。

## デストラクチャリング

デストラクチャリングとは、オブジェクトを変数に代入したり、必要なプロパティだけを取捨選択できる機能です。

下記のコードは、`menu`プロパティを分解して`meat`と`fish`をローカル変数に代入しています。
このように、オブジェクトから必要最低限の変数のみを宣言することができます。

```javascript
const menu = {
  meat: "steak",
  fish: "salmon",
  vegetable: "tomato",
};
const { meat, fish } = menu;
console.log(meat, fish); // steak salmon
```
また、デストラクチャリングは変数の代入だけでなく、関数の引数にも適用できます。

```javascript
const menu = {
  meat: "steak",
  fish: "salmon",
  vegetable: "tomato",
};

// デストラクチャリングを使わない場合
const test = menu => {
  console.log(`I like ${menu.meat} and ${menu.fish}!`);
};

// デストラクチャリングを使った場合
const test = ({ meat, fish }) => {
  console.log(`I like ${meat} and ${fish}!`);
};
```
オブジェクトがさらにネストされていた場合でも、デストラクチャリングは利用できます。

```javascript
const menu = {
  meat: "steak",
  fish: "salmon",
  vegetable: "tomato",
  mealSet: {
    meatSet: "steak and bread",
    fishSet: "tuna and rice",
  }
};
const test = ({ meat,  mealSet: { fishSet } }) => {
  console.log(`I ate ${meat} and ${fishSet}!`);
};
test(menu); // I ate steak and tuna and rice!
```
### 配列に対するデストラクチャリング

下記コードのように、不要な要素を読み飛ばして必要な要素のみを取得する（リストマッチング）ことが出来ます。
配列のデストラクチャリングは、この後紹介する[スプレッド構文](#スプレッド構文)と組み合わせて使われることが多いです。

```javascript
const [firstAnimal] = ["dog", "cat", "turtle"];
console.log(firstAnimal); // dog

const [, , lastAnimal] = ["dog", "cat", "turtle"]:
console.log(lastAnimal); // turtle
```


## オブジェクトリテラル

変数をオブジェクトのプロパティとして記述する場合、プロパティ名を省略することが出来ます。

```javascript
const name = "javascript";
const birthday = 1995;
const print = () => `javascript test`;
const programmingLang = { name, birthday, print }; 
// プロパティ名を書くと { name: name, birthday: birthday, print: print } のようになる

console.log(programmingLang); // { name: 'javascript', birthday: 1995, print: [Function: print] }
console.log(programmingLang.print()); // javascript test
```
また、オブジェクトリテラル内に関数を記述する際の`function`キーワードを省略することが出来ます。

```javascript
// 省略しなかった場合
const persona = {
  firstname: firstname,
  lastname: lastname,
  age: age,
  gender: gender,
  words: function() {
    console.log("I like javascript!");
  }
};

// 省略した場合（プロパティ名も省略）
const persona = {
  firstname,
  lastname,
  age,
  gender,
  words() {
    console.log("I like javascript!");
  }
};
```
javascriptでは、このようにオブジェクトが簡素に記述できます。

## スプレッド構文

`...`（ドット3つ）で記述されるスプレッド構文は、いくつかの異なる用途があります。
まず、配列の連結で使用されます。

```javascript
const langList1 = ["javascript", "ruby"];
const langList2 = ["python", "html"];
const unionList = [...langList1, ...langList2];
console.log(unionList.join(", ")); // javascript, ruby, python, html
```

次に、イミュータブルな配列を実現するためにスプレッド構文が使用されます。
下記のコードでは、元の`testList`が`reverse`メソッドによって書き換えられてしまっています（破壊的メソッド）。

```javascript
const testList = ["javascript", "ruby", "python"]
const [last] = testList.reverse();
console.log(last, testList); // python [ 'python', 'ruby', 'javascript' ]
```
これを防ぐために、スプレッド構文で元の配列のコピーを作成し、それを使用するようにします。
そうすることで、元の配列が変更されることなく、配列の最後の要素を取得することが出来ます。

```javascript
const testList = ["javascript", "ruby", "python"]
const [last] = [...testList].reverse();
console.log(last, testList); // python [ 'javascript', 'ruby', 'python' ]
```
スプレッド構文はまた、配列の要素数が分かっていない場合の「残り全部」を簡単に表現することが出来ます。

```javascript
const listLang = ["javascript", "ruby", "python", "C"];
const [first, ...others] = listLang;
console.log(others.join(", ")); // ruby, python, C
```
関数の引数を配列として受け取るにも、スプレッド構文が使われます（残余引数）。

以下のコードは、可変長の引数を残余引数として受け取る関数です。
このコードでは、残余引数としてだけでなく、配列の先頭と末尾の要素を変数に格納するのにもスプレッド構文を使用しています。

```javascript
function searchList(...args) {
  let [start, ...remain] = args;
  let [last, ...others] = remain.reverse();
  console.log(`The length of the array of arguments is ${args.length}.`);
  console.log(`The first element is ${start}.`);
  console.log(`The last element is ${last}.`);
  console.log(`The length of the array of remaining is ${others.length}.`);
};
searchList("first", "second", "third", "forth", "fifth");
```
最後に、スプレッド構文は配列だけでなく、オブジェクトにも使用できます。

```javascript
const morningAndLunch = {
  breakfast: "bread",
  lunch: "tuna and rice",
};
const dinner = "ramen";

const mealOfTheDay = {
  ...morningAndLunch,
  dinner
};

console.log(mealOfTheDay); // { breakfast: 'bread', lunch: 'tuna and rice', dinner: 'ramen' }
```

## 非同期処理

非同期処理とは、一つのタスクが実行中であっても他のタスクを実行できる実行方式です。

今まで紹介したコードはすべて同期処理です。
同期処理とは、実行したい命令を箇条書きにするようなイメージです。

```javascript
const header = document.getElementById("heading");
header.innerHTML = "Hello World!";
```
上記のコードを言葉で表すと、「`id`の値が`heading`である要素を選択し、それが終わったあと、要素のデータを`Hello Word!`に書き換えてください」となります。
これは逐次的に処理されるため、この命令が実行中は他の命令は実行されません。

非同期処理は、ある命令の処理中に他の命令も実行できるようにした実行方式です。
これを実現するために、JavaScriptには`Promise・fetch`と`async・await`があります。

### Promise・fetch

`fetch`とは、HTTPのリクエストを送信してレスポンスを受信するためのJavaScript APIです。
ここで、`https://javascript.api.sample?results=1`は、サンプルのURLとします。

```javascript
console.log(fetch("https://javascript.api.sample?results=1"));
```
上記では、`fetch`の戻り値をログ出力していますが、出力されるのは`Pending（保留中）`の`Promise`オブジェクトです。

ここで、`Promise`とは、非同期処理の状態を表すオブジェクトで、`Pending（保留中）`、`Resolved（成功）`、`Rejected（失敗）`の3つの状態を表します。
ブラウザが`fetch`呼び出しに対する値をそのまま返さずに、データが取得できてもできなくても結果をお伝えします、という**約束**をするようなイメージです。

`fetch`の戻り値の`Promise`オブジェクトは、`then`メソッドを使用することで扱うことが出来ます。
`fetch`の取得処理が完了したら実行してほしい処理を`then`を使用して指定するようなイメージです。

```javascript
fetch("https://javascript.api.sample?results=1").then(res =>
  console.log(res.json());
);
```
`then`メソッドに記載した関数（コールバック関数）は、非同期処理が成功した際に呼ばれます。
このコールバック関数がさらに戻り値を返した場合、さらに`then`メソッドのコールバック関数に引き継ぐことが出来ます。

```javascript
fetch("https://javascript.api.sample?results=1")
  .then(res => res.json())
  .then(json => json.results)
  .then(console.log)
  .catch(console.error);
```
上記のコードでは、初めに`fetch`内に記載した`URL`に対してリクエストを送信しています。
成功した次は、レスポンスの文字列を`json`オブジェクトに変換します。
それが成功した後、`json`オブジェクトの`results`プロパティを取り出します。
最後にログ出力をします。

これらの処理のどこかでエラーが発生した場合は、`catch`内に設定したコールバック関数が呼ばれ、エラーオブジェクトが出力されます。

また`Promise`オブジェクトは、自分で生成することも可能です。

下記コードでは、`Promise`オブジェクトがコールバック関数とともに生成されています。
コールバック関数の引数では`resolves`と`rejects`は、成功した場合は`resolves`、失敗した場合には`rejects`にデータが渡ります。

```javascript
const test = count =>
  new Promise((resolves, rejects) => {
    const api = `https://javascript.api.sample?results=${count}`;
    const request = new XMLHttpRequest();
    request.open("GET", api);
    request.onload = () =>
      request.status === 200
        ? resolves(JSON.parse(request.response).results)
        : reject(Error(request.statusText));
    request.onerror = err => rejects(err);
    request.send();
  });
```
この関数の使用方法は、`Promise`オブジェクトに対して、下記コードのように`then/catch`メソッドを呼び出す必要があります。

```javascript
test(5)
  .then(testVariable => console.log(testVariable))
  .catch(error => console.error(error.message);
```

### async・await

`Promise`を扱う別の方法として、`async`関数を使う方法もあります。これを使用することで、非同期関数を同期関数のように呼び出すことが出来ます。

`Promise`オブジェクトを受け取って、`then`メソッドで値を取り出す代わりに、`await`キーワードを書くことで、`Promise`が成功するまで処理が止まります。

```javascript
const test = async () => {
  try {
    const res = await fetch("https://javascript.api.sample?results=1");
    const { results } = await res.json();
    console.log(results);
  } catch (error) {
    console.log(error);
  }
};
test();
```
上記のコードでは`async`キーワードを使って関数宣言をしているため、非同期関数を呼び出す際に`await`を使用することが出来ます。

ここでは、`await`を使用して`fetch`を呼び出しているため、以降のコードは`Promise`が成功するまで実行されません。
また、エラー処理に関しては`try/catch`を使用することで、例外処理を実装できます。

[Promise/fetch](#promisefetch)で、自ら`Promise`を生成しました。
その際は、`then`を使用して、非同期処理を実装しましたが、`async/await`を使用しても同じように非同期処理を実装することが出来ます。

```javascript
const test = count =>
  new Promise((resolves, rejects) => {
    const api = `https://javascript.api.sample?results=${count}`;
    const request = new XMLHttpRequest();
    request.open("GET", api);
    request.onload = () =>
      request.status === 200
        ? resolves(JSON.parse(request.response).results)
        : reject(Error(request.statusText));
    request.onerror = err => rejects(err);
    request.send();
  });

async function testUseAsyncAndAwait() {
  try {
    const members = await test(5);
    console.log(members);
  } catch (error) {
    console.error(error.message);
  }
}
```

## モジュール

JavaScriptにおいて、モジュールとは再利用可能なコードで、他のJavaScriptのファイルからインポートすることで利用できます。
たとえば、以下のモジュールは`print`と`log`の2つの関数をエクスポートしています。

```javascript:testExport.js
export const print = message => log(message, date());
export const log = (message, timestamp) =>
  console.log(`${timestamp.toString()}: ${message}`);
const date = () => new Date();
```
変数や関数の宣言に`export`をつけることで、他のモジュールからインポートすることが出来るようになります。

一方、単一のオブジェクトをエクスポートする場合は、`export default`と記述します。

下記のコードは、`Test`クラスのインスタンスを生成して、`default`キーワードを付けてエクスポートしています。
それにより、他のモジュールからこのインスタンスをインポートして使用することが可能になります。

```javascript:testDefaultExport.js
export default new Test("javascript", 1, ["ruby", "python"]);
```

これらのモジュールをインポートして使用する場合は以下のようにします。

```javascript
import { print, log } from "./testExport";
import test from "./testDefaultExport";
print("printing a message");
log("logging a message");
test.print();
```
上記コードのとおり、エクスポートする際の`default`キーワードの有無により、インポートの記述が異なります。

最初の行では、デストラクチャリングを使用して複数の値を受け取っているのに対し、2行目の`default`を使用してエクスポートされたモジュールは単一の変数を使ってインポートされています。

また、エクスポート側とは異なる名前でインポートすることも可能です。

```javascript
import { print as p, log as l } from "./testExport";
p("printing a message");
l("logging a message");
```
また、以下のように、エクスポートされたすべての値を単一のオブジェクトで受け取ることも可能です。

```javascript
import * as exportObject from "./testExport";
```

別のモジュール使用方法として`CommonJS`があります。

`CommonJS`では、`module.exports`という特別なオブジェクト経由でモジュールをエクスポートします。

```javascript:testExport.js
const print = message => log(message, new Date());
const log = (message, timestamp) =>
console.log(`${timestamp.toString()}: ${message}`);
module.exports = { print, log };
```

そして、それらをインポートするには`import`文ではなく`require`関数を使います。

```javascript
const { print, log } = require("./testExport")
```

# まとめ
今回はreactを使うために憶えておいて損はないJavaScriptの知識について書きました。
reactを触る前に、少なくともこの知識を学んでおけば、すこしは書いてあることが分かるかと思います。

以上になります。
ありがとうございました。

# 参照
https://www.oreilly.co.jp/books/9784873119380

