# Nano Events

JavaScript 用のシンプルで極小なイベントエミッタライブラリ。

* わずか **152 バイト**（ミニファイおよび gzip 圧縮時）。
  サイズの管理には [Size Limit] を使用しています。
* `on` メソッドは `unbind` 関数を返します。`removeListener` のためにコールバックを変数に保存しておく必要はありません。
* TypeScript と ES モジュールをサポート。
* エイリアスはなく、`emit` と `on` メソッドのみを提供します。
  Node.js の [EventEmitter] との互換性はありません。

```js
import { createNanoEvents } from 'nanoevents'

const emitter = createNanoEvents()

const unbind = emitter.on('tick', volume => {
  summary += volume
})

emitter.emit('tick', 2)
summary //=> 2

unbind()
emitter.emit('tick', 2)
summary //=> 2
```

[EventEmitter]: https://nodejs.org/api/events.html
[Size Limit]:   https://github.com/ai/size-limit

<a href="https://evilmartians.com/?utm_source=nanoevents">
  <img src="https://evilmartians.com/badges/sponsored-by-evil-martians.svg"
       alt="Sponsored by Evil Martians" width="236" height="54">
</a>

## 目次

* [インストール](#install)
* [TypeScript](#typescript)
* [オブジェクトへのミックスイン](#mixing-to-object)
* [リスナーの追加](#add-listener)
* [リスナーの削除](#remove-listener)
* [リスナーの実行](#execute-listeners)
* [イベント一覧](#events-list)
* [Once](#once)
* [すべてのリスナーの削除](#remove-all-listeners)

## インストール

```sh
npm install nanoevents
```

## TypeScript

Nano Events は、イベント名とリスナーの引数の型をマッピングしたインターフェースを受け付けます。

```ts
interface Events {
  set: (name: string, count: number) => void,
  tick: () => void
}

const emitter = createNanoEvents<Events>()

// 正しい呼び出し:
emitter.emit('set', 'prop', 1)
emitter.emit('tick')

// コンパイルエラー:
emitter.emit('set', 'prop', '1')
emitter.emit('tick', 2)
```

## オブジェクトへのミックスイン

Nano Events の API はたった2つのメソッドしか持たないため、クラス内にプロキシメソッドを作成したり、完全にカプセル化したりすることが簡単にできます。

```js
class Ticker {
  constructor () {
    this.emitter = createNanoEvents()
    this.internal = setInterval(() => {
      this.emitter.emit('tick')
    }, 100)
  }

  stop () {
    clearInterval(this.internal)
    this.emitter.emit('stop')
  }

  on (event, callback) {
    return this.emitter.on(event, callback)
  }
}
```

TypeScript での使用例:

```ts
import { createNanoEvents, Emitter } from "nanoevents"

interface Events {
  start: (startedAt: number) => void
}

class Ticker {
  emitter: Emitter

  constructor () {
    this.emitter = createNanoEvents<Events>()
  }

  on<E extends keyof Events>(event: E, callback: Events[E]) {
    return this.emitter.on(event, callback)
  }
}
```

## リスナーの追加

特定のイベントにリスナーを追加するには `on` メソッドを使用します。

```js
emitter.on('tick', number => {
  console.log(number)
})

emitter.emit('tick', 1)
// 1を出力
emitter.emit('tick', 5)
// 5を出力
```

リスナーが特定のコンテキストに依存している場合（内部で `this` を使用する場合）、関数をコールバックとして渡す前に、必要なコンテキストを明示的にバインドする必要があります。

```js
var app = {
  userId: 1,
  getListener () {
    return () => {
      console.log(this.userId)
    }
  }
}
emitter.on('print', app.getListener())
```

注意: `.bind()` メソッドを使用したバインディングは期待通りに動作しない可能性があるため、推奨されません。

## リスナーの削除

`on` メソッドは `unbind` 関数を返します。これを呼び出すと、このリスナーはイベントから削除されます。

```js
const unbind = emitter.on('tick', number => {
  console.log('on ' + number)
})

emitter.emit('tick', 1)
// "on 1"を出力

unbind()
emitter.emit('tick', 2)
// 何も出力しない
```

## リスナーの実行

`emit` メソッドはすべてのリスナーを実行します。最初の引数はイベント名で、それ以降の引数はリスナーに渡されます。

```js
emitter.on('tick', (a, b) => {
  console.log(a, b)
})
emitter.emit('tick', 1, 'one')
// 1, 'one'を出力
```

## イベント一覧

`events` プロパティから、使用されているイベントのリストを取得できます。

```js
const unbind = emitter.on('tick', () => { })
emitter.events //=> { tick: [ [Function] ] }
```

## Once

最初のイベント発行時にのみ実行されるリスナーを追加したい場合は、以下のスニペットを使用できます。

```js
class Ticker {
  constructor () {
    this.emitter = createNanoEvents()
  }
  …
  once (event, callback) {
    const unbind = this.emitter.on(event, (...args) => {
      unbind()
      callback(...args)
    })
    return unbind
  }
}
```

## すべてのリスナーの削除

```js
emitter.on('event1', () => { })
emitter.on('event2', () => { })

emitter.events = { }
```
