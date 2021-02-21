---
title: "google.script.run が Promise を返すようにする"
emoji: "🐙"
type: "tech"
topics: ["googleappsscript"]
published: true
---

# 期待を返せ
`google.script.run`は`withSuccessHandler`や`withFailureHandler`でコールバックを登録する仕様となっており、`Promise`を返してくれません。
`async`/`await`ですっきり書きたいですよね。

```Javascript:理想
(async ()=> {
  const data1 = await google.script.run.myFunction()
  const data2 = await google.script.run.myFunction2( data1 )
  console.log( data1 )
  console.log( data2 )
})();
```

```Javascript:現実
google.script.run
.withSuccessHandler( data1 => {
  google.script.run
  .withSuccessHandler( data2 => {
    console.log( data1 )
    console.log( data2 )
  })
  .withFailureHandler( console.error )
  .myFunction2( data1 )
})
.withFailureHandler( console.error )
.myFunction()
```

# Promiseを返さないなら自作関数で包んでしまえ
というわけで`google.script.run`を元に`Promise`を返す関数を動的に定義します。

```Javascript:
const ServerScript = {}
for ( const methodName in google.script.run ) {
  const method = google.script.run[ methodName ]
  if ( typeof method !== "function" ) continue
  if ( methodName === method.prototype.constructor.name ) continue
  ServerScript[ methodName ] = ( ...args ) => new Promise(
    ( resolve, reject ) => {
      google.script.run
      .withSuccessHandler( resolve )
      .withFailureHandler( reject )
      [ methodName ]( ...args )
    }
  )
}

(async ()=> {
  const data1 = await ServerScript.myFunction()
  const data2 = await ServerScript.myFunction2( data1 )
  console.log( data1 )
  console.log( data2 )
})()
```

`for`文の中の2つ目の`if`は`withSuccessHandler`や`withFailureHandler`を弾くためのものなので、別に無くても問題ないです。呼ばなければ良い話ですから。

また、この条件でも`doGet`などが入ってきてしまうのですが、呼んでもエラーなどは起きずに `undefined`が返ってきます。元々の`google.script.run`からそのような動きをするようなので、こちらも特に気にしないことにします。

以上、快適なGASライフを！

