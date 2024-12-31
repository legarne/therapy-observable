# @therapy/observable

[![JSR](https://jsr.io/badges/@therapy/observable)](https://jsr.io/@therapy/observable)

## Description

@therapy/observable is an implementation of the observer pattern for simple state management. It has zero-dependencies
and is lightwight, utilizing basic classes.

## Permissions

@therapy/observable does not require any additional permissions.

## Installation

```bash
deno add jsr:@therapy/observable
```

### Usage and Examples

Observables will call registered callbacks when their associated value changes.

#### Basic Observable

```ts
const obs = new Observable(0);
obs.listen((newVal: number) => {
  console.log(newVal); // updated value
});

obs.value = 10;
```

#### Disposal and unlistening

```ts
const foo = (val: number) => console.log(val);
const bar = (val: number) => val + 10;

const obs = new Observable(0);
obs.listen(foo);
obs.listen(bar);

obs.value = 10; // listener(s) called

obs.unlisten(foo); // removes listener from updates

obs.dispose(); // removes all listeners
```

#### Maps and Sets

`ObservableMap` and `ObservableSet` provide convenience when working with objects or arrays.

```ts
const oMap = new ObservableMap<string, number>();
const oSet = new ObservableSet<number>();

oMap.listen((update) => {
  if (!update) return; // update will be null if map is cleared

  /**
   * {
   *   key: "foo",
   *   oldValue: undefined,
   *   newValue: 10,
   * }
   */
  console.log(update);
});
oMap.set("foo", 10);

oSet.listen((update) => {
  if (!update) return; // update will be null if set is cleared

  /**
   * {
   *   idx: 0,
   *   oldValue: undefined,
   *   newValue: 20,
   * }
   */
  console.log(update);
});
oSet.add(20);
```

#### ObservableStruct

`ObservableStruct` is functionally the same as an `ObservableMap`, but enforces type safety on the initial default value.

```ts
const state = new ObservableStruct({ foo: 10, bar: "hello" });

state.listen((update) => {
  if (!update) return; // update will be null if struct is cleared

  /**
   * {
   *   key: "bar",
   *   oldValue: "hello",
   *   newValue: "world",
   * }
   */
  console.log(update);
});
oMap.set("bar", "world");

oMap.set("foo", "goodbye"); // TS Error, foo's default value was not a string

// Generics can be used for finer control of the type
new ObservableStruct<{ foo: string | number }>({ foo: "goodbye" });
```

#### Listening to multiple observables

The observe function can be used to call the same listener for multiple observable objects.

```ts
const obs1 = new Observable(0);
const obs2 = new Observable(10);

const dispose = observe(() => {
  /** handle state change when either observables are changed */
}, [obs1, obs2]);

/** clean up, will remove this listener from these observables */
dispose();
```

## License

MIT
