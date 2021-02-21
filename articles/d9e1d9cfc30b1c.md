---
title: "Javascript で実行環境のエンディアン（バイトオーダー）を確認する方法"
emoji: "🐣"
type: "tech"
topics: ["javascript"]
published: true
---

# そもそもエンディアンとは？
エンディアン（endianness）はバイトオーダー（byte order）とも言い、バイトを記録する順序のことです。
例えば、数値の「1」を2バイトで書き込む場合に上位バイトに1が来るのが**リトルエンディアン**、下位バイトに1が来るのが**ビッグエンディアン**です。

|エンディアン|上位|下位|
|:-|-:|-:|
|リトルエンディアン|01|00|
|ビッグエンディアン|00|01|

# エンディアンの確認方法
型付き配列（[TypedArray](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)）を使用すれば簡単に調べることができます。

```Javascript
const [ isLittleEndian ] = new Uint8Array( Uint16Array.of( 1 ).buffer )

//1→リトルエンディアン
//0→ビッグエンディアン
console.log( isLittleEndian )
```

Javascript の基本的な仕組みしか使っていないので、Node.js はもちろん Google Apps Script (GAS) でのエンディアンを調べることもできます。
筆者が試したところ GAS はリトルエンディアンでしたが、国によって異なるのか、あるいは将来的に変更されるようなことがあるのか不明です。
いずれにしても、バイトオーダーを確認したい場合には有効でしょう。

なお、[DataView](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/DataView) を使用すれば、バイトオーダーに依存しないコードが書けるため、実行速度が欲しい等の理由がなければそちらを利用した方が良いでしょう。
