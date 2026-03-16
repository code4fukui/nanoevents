# Nano Events

Simple and tiny event emitter library for JavaScript.

* Only **152 bytes** (minified and gzipped).
  It uses [Size Limit] to control size.
* `on` method returns `unbind` function. You don’t need to save
  callback to variable for `removeListener`.
* TypeScript and ES modules support.
* No aliases, just `emit` and `on` methods.
  No Node.js [EventEmitter] compatibility.

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

[EventEmitter]: https://nodejs.org/api/events.html
[Size Limit]:   https://github.com/ai/size-limit

<a href="https://evilmartians.com/?utm_source=nanoevents">
  <img src="https://evilmartians.com/badges/sponsored-by-evil-martians.svg"
       alt="Sponsored by Evil Martians" width="236" height="54">
</a>

## Table of Contents

* [Install](#install)
* [TypeScript](#typescript)
* [Mixing to Object](#mixing-to-object)
* [Add Listener](#add-listener)
* [Remove Listener](#remove-listener)
* [Execute Listeners](#execute-listeners)
* [Events List](#events-list)
* [Once](#once)
* [Remove All Listeners](#remove-all-listeners)

## Install

```sh
npm install nanoevents

## TypeScript

Nano Events accepts interface with event name
to listener argument types mapping.

```ts
interface Events {
  set: (name: string, count: number) => void,
  tick: () => void
}

const emitter = createNanoEvents<Events>()

// Correct calls:
emitter.emit('set', 'prop', 1)
emitter.emit('tick')

// Compilation errors:
emitter.emit('set', 'prop', '1')

## Mixing to Object

Because Nano Events API has only just 2 methods,
you could just create proxy methods in your class
or encapsulate them entirely.

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

With Typescript:

import { createNanoEvents, Emitter } from "nanoevents"

  start: (startedAt: number) => void

  emitter: Emitter

    this.emitter = createNanoEvents<Events>()

  on<E extends keyof Events>(event: E, callback: Events[E]) {

## Add Listener

Use `on` method to add listener for specific event:

emitter.on('tick', number => {
  console.log(number)

emitter.emit('tick', 1)
// Prints 1
emitter.emit('tick', 5)
// Prints 5

In case of your listener relies on some particular context
(if it uses `this` within itself) you have to bind required
context explicitly before passing function in as a callback.

var app = {
  userId: 1,
  getListener () {
    return () => {
      console.log(this.userId)
emitter.on('print', app.getListener())

Note: binding with use of the `.bind()` method won’t work as you might expect
and therefore is not recommended.

## Remove Listener

Methods `on` returns `unbind` function. Call it and this listener
will be removed from event.

const unbind = emitter.on('tick', number => {
  console.log('on ' + number)

// Prints "on 1"

// Prints nothing

## Execute Listeners

Method `emit` will execute all listeners. First argument is event name, others
will be passed to listeners.

emitter.on('tick', (a, b) => {
  console.log(a, b)
emitter.emit('tick', 1, 'one')
// Prints 1, 'one'

## Events List

You can get used events list by `events` property.

const unbind = emitter.on('tick', () => { })
emitter.events //=> { tick: [ [Function] ] }

## Once

If you need add event listener only for first event dispatch,
you can use this snippet:

  …
  once (event, callback) {
    const unbind = this.emitter.on(event, (...args) => {
      callback(...args)
    return unbind

## Remove All Listeners

emitter.on('event1', () => { })
emitter.on('event2', () => { })

emitter.events = { }

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

## Features

* Only **152 bytes** (minified and gzipped). It uses [Size Limit] to control size.
* `on` method returns `unbind` function. You don't need to save callback to variable for `removeListener`.
* No aliases, just `emit` and `on` methods. No Node.js [EventEmitter] compatibility.

## Usage

Nano Events accepts interface with event name to listener argument types mapping.

Because Nano Events API has only just 2 methods, you could just create proxy methods in your class or encapsulate them entirely.

## License
This project is licensed under the [MIT License](LICENSE).
