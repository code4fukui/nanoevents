# Nano Events

シンプルで軽量(152バイト)な JavaScript イベントエミッターライブラリです。

## 機能

- 圧縮・gzip後 **152バイト** しかありません。[Size Limit] を使ってサイズを管理しています。
- `on` メソッドは `unbind` 関数を返します。リスナーを削除するために変数を保持する必要がありません。
- TypeScript と ES modules に対応しています。
- `emit` と `on` メソッドしかなく、Node.js [EventEmitter] との互換性はありません。

## 使い方

```js
import { createNanoEvents } from 'nanoevents'

const emitter = createNanoEvents()

const unbind = emitter.on('tick', volume => {
  summary += volume
})

emitter.emit('tick', 2)
summary //=> 2

unbind()
```

### TypeScript

Nano Events は、イベント名とリスナー引数型のマッピングを持つインターフェースを受け付けます。

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

### オブジェクトにミックスする

Nano Events の API は 2 つのメソッドしかないので、クラスにプロキシメソッドを作ったり、完全にカプセル化することができます。

class Ticker {
  constructor () {
    this.emitter = createNanoEvents()
    this.internal = setInterval(() => {
      this.emitter.emit('tick')
    }, 100)

  stop () {
    clearInterval(this.internal)
    this.emitter.emit('stop')

  on (event, callback) {
    return this.emitter.on(event, callback)

## インストール

```sh
npm install nanoevents

[EventEmitter]: https://nodejs.org/api/events.html
[Size Limit]:   https://github.com/ai/size-limit

<a href="https://evilmartians.com/?utm_source=nanoevents">
  <img src="https://evilmartians.com/badges/sponsored-by-evil-martians.svg"
       alt="Sponsored by Evil Martians" width="236" height="54">
</a>

- [Install](#install)
- [TypeScript](#typescript)
- [Mixing to Object](#mixing-to-object)
- [Add Listener](#add-listener)
- [Remove Listener](#remove-listener)
- [Execute Listeners](#execute-listeners)
- [Events List](#events-list)
- [Once](#once)
- [Remove All Listeners](#remove-all-listeners)
- [README.ja.md](README.ja.md)

## ライセンス
このプロジェクトは [MIT License](LICENSE) のもとで公開されています。
