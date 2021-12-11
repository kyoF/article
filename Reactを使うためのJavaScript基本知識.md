この記事は、[マイナビ Advent Calendar 2021](https://qiita.com/advent-calendar/2021/mynavi) 14日目の記事です。

# はじめに
私は新卒でマイナビに入社しました。
入社した後、いくつか研修を受け、その研修の中でReact（+TypeScript）を触る機会がありました。
今までプログラミング経験はありましたが、フロントエンドのコードにはほとんど触れたことがなく、何が何だかわからずReactを触っていました。
エラーが出てはググって、それっぽいところを直して次に行くというようなことをしていたので、触ってはいるけどなんか知識として身についていないなと感じました（研修でも納期的なことがあるので、たくさん時間をかけている余裕が当時ありませんでした。。。）。
研修も終わり、Reactを業務で触る機会が無くなってしまったので、ReactとJavaScriptについて勉強をしていました。
研修とは違い、時間的制約もありませんで、もし最初からこのJavaScriptの知識が分かっていれば苦労しなかったなという学びを得ることが出来ました。
本記事では、JavaScriptを全く触ってこなかった私が、「なるほど」と思った部分についての記事になります。
簡単に言うと初心者向けです。
これからReactを触る方が少しでも参考になれば幸いです。

# 基本知識
## var・const・let
今までは、変数・定数に限らずすべて`var`で宣言していたものを`const`を定数、`let`を変数として扱えるようになった。

```javascript
// varの場合
var js = true;
js = false;
console.log(js); // false

// constの場合
const js = true;
js = false; // Uncaught TypError: ...
```
下記のコードのように、`var`では二重に宣言してしまっている状態でも動いてしまいますが、`let`キーワードを使用することでこの問題を解決できます。またこのコードから分かるように`if文`はスコープを持ちません。

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
上記では、`for`文で利用する`i`を`var`か`let`に変えたのみです。
このプログラムで生成される`div`をクリックする、`var`では、どの`div`要素でも`6`が、`let`では、生成された順番の整数値が表示されます。
これが`var`と`let`の違いです。

## 文字列

文字列を表す方法はいくつかあります。

```javascript
const java = "java";
const script = "script";

// 換算演算子を用いた場合
console.log("This language is " + java + script + "!!"); // This language is javascript!!
// バッククォートと${}を用いた場合
console.log(`This language is ${java}${script}!!`); // This language is javascript!!
```
バッククォートを用いると、改行も認識してくれます。メール文面のテンプレートを作成したり、`HTML`を記述したりするのに便利です。

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
関数式と言い、変数の中に関数を入れることが出来ます。これは、上記のコードと全く同じです。
普通に`function`で宣言するのと違い、関数を呼び出す前に、関数宣言をする必要があります。

```javascript
const test = function() {
  console.log("javascript");
};
test();  // javascript
```
また、関数に引数を設定する場合、デフォルト値を設定することが出来ます。

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

```javascript
// 関数式
const test = function(name) {
  return `My name is ${name}.`;
};
console.log(test("javascript")); // My name is javascript.

// アロー関数
const test = name => `My name is ${name}.`;
console.log(test("javascript")); // My name is javascript.
```
アロー関数を使うことで、関数を一文で記述することが出来ます。
関数の戻り値が単一の式であらわされる場合は`return`文も不要になります。

また、引数が二つの場合は、二つの引数をカッコで囲む必要があります。

```javascript
const test = (firstname, lastname) => `My name is ${firstname} ${lastname}.`;
console.log(test("hoge", "fuga")); // My name is hoge fuga.
```
アロー関数で`return`文を使うと以下のようになります。

```javascript
const test = (firstname, lastname) => {
  if (!firstname) {
    throw new Error("firstname is not entered.");
  }
  if (!lastname) {
    throw new Error("lastname is not entered.");
  }
  return `My name is ${firstname} ${lastname}.`;
};
console.log(test("hoge", "fuga"));
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
アロー関数を一文で記述するときは、戻り値のオブジェクトをカッコで囲みましょう。

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

```javascript
const menu = {
  meat: "steak",
  fish: "salmon",
  vegetable: "tomato",
};
const { meat, fish } = menu;
console.log(meat, fish); // steak salmon
```
上記のコードは、`menu`プロパティを分解して`meat`と`fish`をローカル変数に代入しています。

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
test(menu); // I ate steak and tuna  and rice!
```
### 配列に対するデストラクチャリング

```javascript
const [firstAnimal] = ["dog", "cat", "turtle"];
console.log(firstAnimal); // dog

const [, , lastAnimal] = ["dog", "cat", "turtle"]:
console.log(lastAnimal); // turtle
```
上記コードのように、不要な要素を読み飛ばして必要な要素のみを取得する（リストマッチング）ことが出来ます。
配列のデストラクチャリングはこの後紹介するスプレッド構文と組み合わせて使われることが多いです。

## オブジェクトリテラル

変数をオブジェクトのプロパティとして記述する場合、プロパティ名を省略することが出来ます。

```javascript
const name = "javascript";
const birthday = 1995;
const print = () => `javascript test`;
const programmingLang = { name, birthday, print }; // { name: name, birthday: birthday, print: print } の省略系を記載

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

// 省略した場合（変数名も省略）
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
javascriptでは、このようにオブジェクトリテラルが簡素に記述できます。

## スプレッド構文

`...`（ドット3つ）で記述されるスプレッド構文は、いくつかの異なる用途があります。
まず、配列の連結で使用されます。

```javascript
const testList1 = ["javascript", "ruby"];
const testList2 = ["python", "html"];
const unionList = [...testList1, ...testList2];
console.log(unionList.join(", ")); // javascript, ruby, python, html
```

次にイミュータブルな配列を実現するために使用されます。

```javascript
const testList = ["javascript", "ruby", "python"]
const [last] = testList.reverse();
console.log(last, testList); // python [ 'python', 'ruby', 'javascript' ]
```
上記のコードでは、元の`testList`が`reverse`メソッドによって書き換えられてしまっています（破壊的メソッド）。

これを防ぐために、スプレッド構文で元の配列のコピーを作成し、それを使用するようにします。

```javascript
const testList = ["javascript", "ruby", "python"]
const [last] = [...testList].reverse();
console.log(last, testList); // python [ 'javascript', 'ruby', 'python' ]
```
上記のようにすることで、元の配列が変更されることなく、配列の最後の要素を取得することが出来ます。

スプレッド構文はまた、配列の要素数が分かっていない場合の「残り全部」を簡単に表現することが出来ます。

```javascript
const listLang = ["javascript", "ruby", "python", "C"];
const [first, ...others] = listLang;
console.log(others.join(", ")); // ruby, python, C
```
関数の引数を配列として受け取るために、スプレッド構文が使われます（残余引数）。
以下のコードは、可変長の引数を残余引数として受け取る関数です。

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
上記のコードでは、残余引数としてだけでなく、配列の先頭と末尾の要素を変数に格納するのにもスプレッド構文を使用しているのが分かるかと思います。

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
今まで紹介したコードはすべて同期処理です。同期処理とは、実行したい命令を箇条書きにするようなイメージです。

```javascript
const header = document.getElementById("heading");
header.innerHTML = "Hello World!";
```
上記のコードを言葉で表すと、「`id`の値が`heading`である要素を選択し、それが終わったあと、要素のデータを`Hello Word!`に書き換えてください」といったように、逐次的に処理されるため、この命令が実行中は他の命令は実行されません。

非同期処理は、ある命令の処理中に他の命令も実行できるようにした実行方式です。
これを実現するために、JavaScriptには`Promise・fetch`と`async・await`があります。

### Promise・fetch

`fetch`とはHTTPのリクエストを送信してレスポンスを受信するためのJavaScript APIです。

```javascript
console.log(fetch("https://javascript.api.sample?results=1"));
```
上記では、`fetch`の戻り値をログ出力していますが、出力されるのはPending（保留中）のPromiseオブジェクトが出力されます。
ここで、Promiseとは、非同期処理の状態を表すオブジェクトで、Pending（保留中）、Resolved（成功）、Rejected（失敗）の3つの状態を表します。
ブラウザがfetch呼び出しに対する値をそのまま返すことなく、データが取得できるかできないかに限らず、結果をお伝えしますという**約束**をするようなイメージです。

fetchの戻り値のPromiseオブジェクトは、thenメソッドを使用することで扱うことが出来ます。
データの取得依頼をする際、完了したら実行してほしい処理をthenを使用して指定するようなイメージです。

```javascript
fetch("https://javascript.api.sample?results=1").then(res =>
  console.log(res.json());
);
```

thenメソッドに渡したコールバック関数は、非同期処理が成功した際に呼ばれます。このコールバック関数でさらに戻り値を返した場合、さらにthenメソッドのコールバック関数引き継ぐことが出来ます。

```javascript
fetch("https://javascript.api.sample?results=1")
  .then(res => res.json())
  .then(json => json.results)
  .then(console.log)
  .catch(console.error);
```
上記のコードでは、初めにfetch内に記載したURLに対してリクエストを送信しています。
成功した次は、レスポンスの文字列をjsonオブジェクトに変換し、
それが成功した後、jsonオブジェクトのresultsプロパティを取り出し、
最後にログ出力をします。
これらの処理のどこかでエラーが発生した場合は、catch内に設定したコールバック関数が呼ばれ、エラーオブジェクトが出力されます。

またPromiseオブジェクトは生成することも可能です。

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
上記のコードでは、Promiseオブジェクトがコールバック関数とともに生成されています。コールバック関数の引数のresolvesとrejectsは、成功した場合はresolves、失敗した場合にはrejectsにデータが渡ります。
この関数の使用方法は、Promiseオブジェクトに対して、then/catchメソッドを呼び出す必要があります。

```javascript
test(5)
  .then(testVariable => console.log(testVariable))
  .catch(error => console.error(error.message);
```

### async・await

Promiseを使う別の方法として、async関数を使う方法もあります。これを使用することで、非同期関数を同期関数のように呼び出すことが出来ます。
Promiseオブジェクトを受け取って、thenメソッドで値を取り出す代わりに、awaitキーワードを書くことで、Promiseが成功するまで処理が止まります。

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
上記のコードではasyncキーワードを使って関数宣言をしているため、非同期関数を呼び出す際にawaitを使用することが出来ます。
ここでは、awaitを使用してfetchを呼び出しているため、以降のコードはPromiseが成功するまで実行されません。
また、エラー処理に関してはtry/catchを使用することで、例外処理を実装できます。

Promise/fetchでPromiseを生成しました。
その際は、thenを使用して、非同期処理を実装しましたが、async/awaitを使用しても同じように非同期処理を実装することが出来ます。

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

JavaScriptで、モジュールは再利用可能なコードで、他のJavaScriptのファイルからインポートすることで利用できます。個々のモジュールは別々のファイルに格納されており、名前空間が独立しているため、たとえばモジュール間で変数名が重複していても衝突は起こりません。ひとつのモジュールから、複数のオブジェクトをエクスポートすることも可能ですし、単一のオブジェクトのみをエクスポートするモジュールも可能です。たとえば、以下のモジュール（text-helpers.js）は2 つの関数をエクスポートしています。

```javascript
export const print = message => log(message, date());
export const log = (message, timestamp) =>
console.log(`${timestamp.toString()}: ${message}`);
const date = () => new Date();
```

変数や関数の宣言にexport を付加することで、他のモジュールからインポートして使用することが可能になります。上記の例では print 関数と log 関数がエクスポートされています。date関数はエクスポートされていないため、そのモジュール内でのみ参照可能で、他のモジュールから
は参照できません。
一方、単一のオブジェクトをエクスポートする場合は、export default と記述します。以下のモジュール（mt-freel.js）は単一のオブジェクトをエクスポートしています。

```javascript
export default new Expedition("Mt. Freel", 2, ["water", "snack"]);
```

上記の例ではExpedition クラスのインスタンスを生成してdefault キーワードを付けてエクスポートしています。それにより、他のモジュールからこのインスタンスをインポートして使用することが可能になります。それでは次にこれらのモジュールをインポートして使用する側のコード
を見てみましょう。

```javascript
import { print, log } from "./text-helpers";
import freel from "./mt-freel";
print("printing a message");
log("logging a message");
freel.print();
```

ご覧のとおり、エクスポートする際のdefault キーワードの有無により、インポートの記述が異なります。最初の行では、デストラクチャリングを使用して複数の値を受け取っているのに対し、2 行目のdefault を使用してエクスポートされたモジュールは単一の変数を使ってインポー
トされています。
また、エクスポート側とは異なる名前でインポートすることも可能です。以下のコードでは、先のprint 関数とlog 関数をそれぞれp とl という変数で受け取っています。

```javascript
import { print as p, log as l } from "./text-helpers";
p("printing a message");
l("logging a message");
```

また、以下のように、エクスポートされたすべての値を単一のオブジェクトで受け取ることも可能です。

```javascript
import * as fns from "./text-helpers";
```

ここで紹介した import と export は ECMAScript モジュール（ESM）と呼ばれる仕様の一部です。ESM はプラットフォームによりサポートにばらつきがあり、一部の機能はまだすべてのブラウザで実装されていません。けれども、先述のとおり Babel を使えばそういった差分を吸収してくれます。
2.7.1 CommonJS モジュール
ECMAScript モジュールが登場する前から、Node.js ではCommonJS というモジュールパターンが採用されていました（https://oreil.ly/CN-gA）。Babel や webpack ではこの CommonJSがサポートされているので、それらのツールを使うのであれば、CommonJS 形式のモジュールをインポート／エクスポートすることが可能です。
CommonJS では、module.exports という特別なオブジェクト経由でモジュールをエクスポートします。たとえば、先ほどの print 関数と log 関数を CommonJS モジュールとしてエクスポートするには、以下のように記述します。

```javascript
const print = message => log(message, new Date());
const log = (message, timestamp) =>
console.log(`${timestamp.toString()}: ${message}`);
module.exports = {print, log};
```

そして、それらをインポートするにはimport 文ではなくrequire 関数を使います。

```javascript
const { log, print } = require("./txt-helpers")
```

# まとめ
今回はreactを使うために憶えておいて損はないJavaScriptの知識について書きました。
reactを触る前に、少なくともこの知識を学んでおけば、すこしは書いてあることが分かるかと思います。

以上になります。
ありがとうございました。

# 参照
https://www.oreilly.co.jp/books/9784873119380

