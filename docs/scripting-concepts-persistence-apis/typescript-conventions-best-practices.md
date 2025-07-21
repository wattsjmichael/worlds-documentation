<p align=center>
<b>TypeScript Conventions and Best Practices for Horizon Worlds</b><br>
December 2024<br>
(Updated formatting July 2025)<br>
Shards632<br>
</p>

# Introduction

TypeScript is a robust and full-featured computer programming language that is derived from JavaScript (also known as ECMAScript). Like most computer languages, it does not come with any official formatting guidelines to enforce consistency, and, due to its legacy in JavaScript, contains some language features that are best just avoided.

This document will endeavor to present a consistent formatting style for use by Horizon Worlds TypeScript developers, while listing best practices that TypeScript developers ought to follow in order to avoid falling into some of the pitfalls of the language that either are just ‘bad’ or do not translate well to the Horizon Worlds environment.

## Prerequisites and Expectations

This is not a document about ‘learning typescript’ or even about ‘learning the Horizon Worlds typescript api’. It is expected that you are coming to this document with some experience with programming languages, and some experience in writing scripts for Horizon Worlds.

Following conventions and best practices are not _required_ for writing code in Horizon Worlds. The purpose of conventions and best practices is to make your code easier for others to understand (and for you to understand other people’s code more easily), and to avoid common mistakes that are caused by formatting ambiguities or language features that are easy to use incorrectly. Some choices for conventions can seem a bit arbitrary, but the _most important_ thing about conventions is that some decisions are made and that people try to follow them whenever possible.

## Document Organization

This document has a shorter Minimal Recommendations section that, if you adopt nothing else, you should at least do, followed by an Extended Recommendations section that has a more robust set of rules.

Some of the recommendations are stronger than others and should almost always be followed, while others are perhaps a bit more difficult to apply consistently or are considered ‘controversial’.

Finally, if you are using an IDE like VSCode or Webstorm or similar to write your typescript code (rather than, say Notepad or something), there are freely available plugins that you can install that will handle many of these recommendations for you automatically (and probably even more\!). If you’re impatient, skip to the [TL;DR Plugins](#tldr-plugins) section, install those tools, and be on your way.

# 👉🏻 Minimal Recommendations TL;DR

If you adopt nothing else, you should at least do (or not do) the following:

| [Whitespace](#whitespace) |
| ---- |
| [✅ Use Spaces, not Tab](#use-spaces-not-tabs) |
| [✅ Use consistent indentation amounts](#use-consistent-indentation-amounts) |
| [✅ Always use curly braces on control flow blocks](#always-use-curly-braces-on-control-flow-blocks) |
| [✅ Separate adjacent methods in a class with a blank line](#separate-adjacent-methods-in-a-class-with-a-blank-line) |
| [🚫 Don’t use string continuations, instead append strings](#dont-use-string-continuations-instead-append-strings) |

| [Language Features](#language-features)
| ---- |
| [✅ Always include a default in switch statements](#always-include-a-default-in-switch-statements)
| [✅ Use ?? rather than \|\| for defaults on undefined values](#use--rather-than--for-defaults-on-undefined-values)
| [⚠️ Avoid using as](#avoid-using-as)
| [⚠️ Avoid using ! on null or undefined values](#avoid-using--on-null-or-undefined-values)
| [⚠️ Avoid using Number, Boolean, String, Object as types](#avoid-using-number-boolean-string-object-as-types)
| [⚠️ Avoid using for…in](#avoid-using-forin)
| [⚠️ Avoid using new Array()](#avoid-using-new-array)
| [🚫 Do not compare booleans to true or false](#do-not-compare-booleans-to-true-or-false)
| [🚫 Do not write non-trivial fallthrough case statements](#do-not-write-non-trivial-fallthrough-case-statements)
| [🚫 Do not use the {} type as an opaque type](#do-not-use-the--type-as-an-opaque-type)
| [❌ Never use any](#never-use-any)
| [❌ Never use == or != for equality checks, Use === and !== instead](#never-use--or--for-equality-checks-use--and--instead)
| [❌ Never use var](#never-use-var)
| [❌ Never do assignment in a conditional statement](#never-do-assignment-in-a-conditional-statement)

| [Naming](#naming) |
| ---- |
| [✅ Only use 7-bit alphanumeric characters and _ for identifiers](#only-use-7-bit-alphanumeric-characters-and-_-for-identifiers) |

| [Horizon Worlds Features](#horizon-worlds-features) |
| ---- |
✅ Define Events once and export to all users
✅ Prefer NetworkEvents and LocalEvents over CodeBlockEvents
✅ Always quote asset ids as strings
🚫 Do not use bind() when registering event handlers, use arrow functions
🚫 Don’t connect to events in start(), use preStart()
❌ Never send events in preStart(), use start() instead.
❌ Never subclass a Horizon Worlds API class other than Component or UIComponent



# 👉🏻 Extended Recommendations TL;DR

A more robust set of recommendations

| [Whitespace](#whitespace-1) |
| ---- |
| [✅ Use whitespace liberally (there is no fee for use!)] (#use-whitespace-liberally-there-is-no-fee-for-use)
| [✅ Put control flow blocks on their own line](#put-control-flow-blocks-on-their-own-line)
| [✅ Use semicolons to end statements](#use-semicolons-to-end-statements)

| [Language Features](#language-features) |
| ---- |
| ✅ [Use const rather than let on local/global variables whenever possible](#use-const-rather-than-let-on-localglobal-variables-whenever-possible) |
| ✅ [Use readonly on class variables whenever possible](#use-readonly-on-class-variables-whenever-possible) |
| ✅ [Mark class variables and methods private (or protected) whenever possible](<#mark-class-variables-and-methods-private-or-protected-whenever-possible>) |
| ✅ [Use parameter properties on constructor arguments](#use-parameter-properties-on-constructor-arguments) |
| ✅ [Use override on all class methods that are being overridden, especially start() and preStart()](#use-override-on-all-class-methods-that-are-being-overridden-especially-start-and-prestart) |
| ✅ [Use undefined rather than null](#use-undefined-rather-than-null) |
| ✅ [For simple arrays of type T, use T[] and readonly T[] rather than Array<T> or ReadonlyArray<T>](#for-simple-arrays-of-type-t-use-t-and-readonly-t-rather-than-arrayt-or-readonlyarrayt)   |
| ✅ [Use interpolated strings only when there is interpolation](#use-interpolated-strings-only-when-there-is-interpolation) |
| ✅ [Use tuples rather than returning pair objects](#use-tuples-rather-than-returning-pair-objects) |
| ✅ [Minimize escape characters in strings](#minimize-escape-characters-in-strings) |
| ✅ [Use forEach(), map(), reduce(), and filter() instead of writing for() loops](<#use-foreach-map-reduce-and-filter-instead-of-writing-for-loops>) |
| ✅ [Prefer the Map<K, V> type rather than using object or Record<K, V> types for key/value mappings](#prefer-the-mapk-v-type-rather-than-using-object-or-recordk-v-types-for-keyvalue-mappings) |
| ✅ [Use the spread operator … to concatenate arrays and objects](#use-the-spread-operator--to-concatenate-arrays-and-objects) |
| [✅ Export only what’s needed](#export-only-whats-needed)|
| [🤔 Consider destructured imports](#consider-destructured-imports) |
| [⚠️ Avoid using methods on the Object class](#avoid-using-methods-on-the-object-class) |
| [🚫 Do not assign arrow functions to class member variables](#do-not-assign-arrow-functions-to-class-member-variables) |
| [🚫 Omit obvious types when initializing variables at declaration](#omit-obvious-types-when-initializing-variables-at-declaration) |
| [🚫 Do not assign function declarations to variables or pass them as arguments](#do-not-assign-function-declarations-to-variables-or-pass-them-as-arguments) |

| [Naming](#naming-1)
| ---- |
| [✅ PascalCase for types, camelCase for variables/functions, CAPITALIZED_SNAKE_CASE for global constants](#pascalcase-for-types-camelcase-for-variablesfunctions-capitalized_snake_case-for-global-constants)
| [✅ Use descriptive names](#use-descriptive-names)
| [🚫 Don’t use _ (underline) prefix for private member variables](#dont-use-_-underline-prefix-for-private-member-variables)

| [Layout](#layout)
| ---- |
| [✅ All imports at top of file](#all-imports-at-top-of-file)
| [✅ Organize files with type definitions first, then globals, then classes](#organize-files-with-type-definitions-first-then-globals-then-classes)
| [✅ Organize classes with statics, then variables, then methods](#organize-classes-with-statics-then-variables-then-methods)

| [General](#general)
| ---- |
| [✅ Comment the important things](#comment-the-important-things)
| [✅ Write TSDoc comments on functions and on important variables/classes](#write-tsdoc-comments-on-functions-and-on-important-variablesclasses)
| [✅ Separate unrelated Components into separate files](#separate-unrelated-components-into-separate-files)
| [✅ Use line comments for internal implementation details](#use-line-comments-for-internal-implementation-details)
| [✅ Avoid clever code](#avoid-clever-code)

# Minimal Recommendations

## Whitespace

### Use spaces, not tabs

Never use tabs for indenting your code. Different people may have different tab stop settings and when they invariably get mixed in with people who indent using spacing, the formatting can become a mess.

### Use consistent indentation amounts

Use the same indentation level amounts in your code. Do not indent some levels by three spaces and others by seven spaces and others by zero spaces. The typical indentation amount for typescript code is 2 spaces per level.

**✅ Do this:**

```ts
function myFunction() {
  if (someCondition) {
    // do some things
    // do some other things
  } else {
    for (let i = 0; i < 10; i++) {
      // do something 10 times
    }
  }
}
```

**🚫 Not this:**

```ts
function myFunction() {
 if (someCondition) {
    // do some things
      // do some other things
   } else {
for (let i = 0; i < 10; i++) {
  // do something 10 times
}
 }
}
```

### Always use curly braces on control flow blocks

When you have a sub-block of code, such as with an `if`, `else`, `for`, or `while` block, always use curly braces to enclose it. Critical errors can be prevented by always enclosing blocks in curly braces rather than leaving them hanging naked where someone might erroneously add a line that executes when unexpected.

✅ Do this:
```ts
if (someBoolean) {
 return;
} 

while (keepLooping) {
 inLoop();
} 

for (let i = 0; i < 10; i++) {
 doLoop();
}
```

🚫 Not this:

```ts
if (someBoolean) 
 return;

while (keepLooping) 
 inLoop();

for (let i = 0; i < 10; i++) 
 doLoop();
```

### Separate adjacent methods in a class with a blank line

It is difficult to see the end of one method and the start of another if they run together without an empty line break in between them.

✅ Do this:

```ts
class MyClass {

  method1() {
    // do stuff
  }

  method2() {
    // do other stuff
  }
}
```

🚫 Not this:

```ts
class MyClass {
  method1() {
    // do stuff
  }
  method2() {
    // do other stuff
  }
}
```

### Don’t use string continuations, instead append strings

✅ Do this:

```ts
const string = "A very long string" +
    "crossing multiple lines."
```

🚫 Not this:

```ts
const string = "A very long string\
    crossing multiple lines."
```

## Language features

### Never use `any`

If you don’t know the type of something, use `unknown`. Use of the `any` keyword turns off typescript’s type checking, which defeats a large amount of the benefit of using typescript over javascript.

✅ Do this:

```ts
const somethingUnknown: unknown
```

🚫 Not this:

```ts
const somethingUnknown: any
```

### Never use `var`

Use `let` or `const` for all variable declarations. The use of `var` has unexpected scoping consequences that can cause difficult to track down bugs.

✅ Do this:

```ts
let value: number = 0
if (somethingTrue) {
  value = 2
}
console.log(value)
```

🚫 Not this::

```ts
if (somethingTrue)
  var value: number = 2
}
console.log(value)
```

### Avoid using `as`

Likewise, the `as` keyword is you telling typescript that the type is different than it thinks, and partially defeats the typing system. Instead, use an `if` statement to test that the object is an `instanceof` the class you expect.

Assuming these definitions:

```ts
class SomeType {
}

class SubType extends SomeType {
  doThing() {
    // subtype thing
  }
} 
```

✅ Do this:

```ts
function doIfSubType(thing: SomeType) {
  if (foo instanceof SubType) {
    foo.doThing();
  }
}
```

🚫 Not this:

```ts
function doIfSubType(thing: SomeType) {
  (thing as SubType).doThing();
}
```

There are a few rare cases where using `as` is important, such as when you mark an explicit structure `as const` to indicate the values cannot be changed.

```ts
const Events = {
   myEvent1: new LocalEvent('event1'),
   myEvent2: new LocalEvent('event2'),
} as const;
```

**Note:** This does not include a prohibition on the `Entity.as()` method, of which use is necessary to convert Horizon Worlds Entity types to their corresponding Gizmos.

### Avoid using `!` on `null` or `undefined` values

Similar to using `any`, the use of `!` tells typescript to ignore its typing checks because you know better that the variable is actually defined. Typescript is very good at tracking the types of variables, so if you find yourself forcing typescript to treat something as defined that it thinks may not be, ask yourself if you _really_ understand what’s going on.

Preferably, rewrite your code using an if statement to check for undefined or `null` (or nullish values) so that the type guard will remove the necessity of using `!`. Also, use `?`, when possible, to short circuit statements that might be undefined.

✅ Do this:

```ts
if (thing !== undefined && thing !== null) { // explicit check
  thing.callFunction();
}

if (thing) {  // check for nullish / falsy value
  thing.callFunction();
}

thing?.callFunction(); // optional chaining
```

🚫 Not this:

```ts
thing!.callFunction();
```

Unfortunately, the `props` on Components may be undefined if nothing was wired up to them. You really ought to check they are not `undefined` before you use them. At the very least, check in `start()` or `preStart()` that all the `props` are defined and if not, emit a very large `console.error()` and throw an exception before you go adding `this.props.myProp!` In other methods in the class.

Example:

```ts
class MyComponent extends Component<typeof MyComponent> {
  static propsDefinition = {
    prop1: { type: PropTypes.Entity },
    prop2: { type: PropTypes.Entity },
  }

  override preStart() {
    if (!this.props.prop1 || !this.props.prop2) {
      console.error("prop1 or prop2 are not set!");
      throw new Error("fatal config error"); // kills the script at world start
    }
    // no need for ! here because the if test above protects this statement
    this.connectLocalEvent(this.props.prop1, SomeEvent, () => { /* some action */ });
  }

  override start() {
    // safe-ish to use ! here because start() will never be called if preStart() throws an Error killing the script
    this.sendLocalEvent(this.props.prop2!, SomeEvent, {});
  }
}
```

### Avoid using `Number`, `Boolean`, `String`, `Object` as types

These capitalized versions of `number`, `boolean`, `string`, and `object` are wrappers for the primitive types, and only need to be used as types for special purposes. You almost always want to avoid the wrappers and use the primitive types instead.

✅ Do this:

```ts
const someNum: number = 3
function doThing(someString: string): boolean
```

🚫 Not this::

```ts
const someNum: Number = 3
function doThing(someString: String): Boolean
```

These classes _do_ have a lot of useful functions for manipulating `number`s, `strings`s, etc. It is ok to use those functions on the classes.

```ts
// ok
if (Number.isInteger(someNumber)) {
  // do integer things with someNumber
}

// ok
const oneHalf = String.fromCharCode(189)
```

### Never use `==` or `!=` for equality checks (Use `===` and `!==` instead) 

Never use the `==` and `!=` operators to compare values. They will sometimes do some unexpected type conversions and will return true or false when you would think they would do the opposite.

✅ Do this:

```ts
if (someNumber === 3 && someString !== "foo") {
  // do stuff
}
```

🚫 Not this:
```ts
if (someNumber == 3 && someString != "foo") {
  // do stuff
}
```

### Never do assignment in a conditional statement

Never assign a variable value using `=` in a conditional statement. It leaves people wondering whether you really meant to use an equality check rather than assignment. Also, a side effect of assigning a value within a conditional statement is unexpected and makes your code difficult to understand.

✅ Do this:
```ts
let storedValue: boolean;
function setAndCheckValue(value: boolean) {
  storedValue = value;
  if (storedValue) {
    // do thing
  }
}

```

🚫 Not this::
```ts
let storedValue: boolean;
function setAndCheckValue(value: boolean) {
  if (storedValue = value) {
    // do thing
  }
}
```

### Use `??` rather than `||` for defaults on undefined values

This is similar to using `===` and `!==`, as the automatic type conversion of `||` will sometimes convert things into a binary false value that you don’t expect. Using `??` makes sure that only undefined and null values are treated as false.

✅ Do this:

```ts
function defaultIfUnset(value?: number): number {
  return value ?? 5;
}
```

🚫 Not this:

```ts
function defaultIfUnset(value?: number): number {
  return value || 5;
}
```

### Avoid using `for…in` 

The `for…in` construct loops over the keys of an array, which is rarely what you want. Instead, use `for…of`, or `forEach()`

✅ Do this:
```ts
function printNumberMembers(values: number[]) {
  for (const value of values) {
    console.log(value);
  }
}

function printStringMembers(values: string[]) {
  values.forEach(value => {
    console.log(value);
  })
} 
```

🚫 Not this:

```ts
function printBooleanMembers(values: boolean[]) {
  for (const index in values) {
    console.log(values[index]);
  } 
}
```

### Do not compare booleans to `true` or `false`

Boolean values are already `true` or `false`, so it makes no sense to compare them to boolean literals.

✅ Do this:

```ts
if (myBoolean1 && !myBoolean2) {
  // do stuff
}
```

🚫 Not this:

```ts
if (myBoolean1 === true && myBoolean2 === false) {
}
```

### Do not write non-trivial fallthrough `case` statements 

If you have a `switch`/`case` statement, it is a common error to forget to write `break` after each `case`. The only instance where it is acceptable to not have a `break` for each `case` is if that case has zero statements, and you are just trying to group a bunch of common cases together into exactly the same action. Otherwise, always put in a `break` at the end of each `case` block (including `default`).

✅ Do this:

```ts
switch (someValue) {
  case 1:
    doThing1();
    doThing2(); // explicitly do thing2 here that also applies to 1
    break;
  case 2:
    doThing2();
    break;
  case 3: // ok to fall through because empty case
  case 4:
    doThing3And4();
    break;
  default:
    doDefault();
    break;
}
```

🚫 Not this:

```ts
switch (someValue) {
  case 1:
    doThing1();
    // yucky fall through to case 2
  case 2:
    doThing2();
    break;
  case 3:
    doThing3And4(); // possibly unnecessary duplication
    break;
  case 4:
    doThing3And4();
    break;
  default:
    doDefault();
    // missing break on default
}
```

### Always include a default in switch statements

The last case in a switch statement should always be `default`, even if empty. If it is invalid for it to be reached because all other combinations should have been handled, it must at least log a warning or error to the console, if not throw an exception.

✅ Do this:

```ts
switch (someValue) {
  case 1:
    doThing1();
    break;
  case 2:
    doThing2();
    break;
  default:
    console.warn("unhandled case", someValue)
    break;
}
```

🚫 Not this:

```ts
switch (someValue) {
  case 1:
    doThing1();
    break;
  case 2:
    doThing2();
    break;
} // if you add a new possible value later, you may miss handling the case!
```

### Do not use the `{}` type as an opaque type

The `{}` type is a bit like `any`, but for `object`s. When you don’t know the type, use `unknown`. If you mean a dictionary, use `Record<string, unknown>`. If you mean an object, use `object`.

✅ Do this:

```ts
let someUnknownThing: unknown;
let someRecordThing: Record<string, Type>;
let someObjectThing: object;
```

🚫 Not this:

```ts
let someThing: {}
```

### Avoid using `new Array()`

The constructor arguments for the `Array` class are not terribly consistent and are confusing to use correctly.

✅ Do this:

```ts
const myPrefilledArray = Array.from<number>({ length: 5 }).fill(3)
const myArray = [5, 6, 7]
```

🚫 Not this:

```ts
const myArray = new Array(true) // 1 element boolean[] array containing [ "true" ]
const myArray = new Array(5) // 5 element empty array of any[] type
const myArray = new Array(5, 6, 7) // 3 element number[] array containing [ 5, 6, 7]
```

## Naming

### Only use 7-bit alphanumeric characters and \_ for identifiers

Do not use unicode characters in identifiers, or other special characters (such as $). Some unicode characters are very difficult to distinguish from 7-bit ascii.

## Horizon Worlds Features

### Do not use `bind()` when registering event handlers, use arrow functions

Due to a design flaw in the `LocalEvent` and `NetworkEvent` types, the typescript compiler cannot always correctly type check the parameters for functions bound to event handlers if you use `bind()`. Instead, use an arrow function for registering the event. You can either explicitly pass the data object payload to a handler function, or destructure the data object so that the handler function signature is more convenient.

Given this definition:
```ts
const myEvent1 = new LocalEvent<{thing: Entity, name: string}>('myEvent1');
const myEvent2 = new NetworkEvent<{ player: Player }>('myEvent2');
```

✅ Do this:

```ts
override preStart() {
  this.connectLocalEvent(this.entity, myEvent1, data => this.onEvent1(data));
  this.connectNetworkEvent(this.entity, myEvent2, ({player}) => this.onEvent2(player));
}

onEvent1(data: {thing: Entity, name: string}) {
  // argument types will be checked in connect() call
  console.log(data.thing, data.name);
}

onEvent2(player: Player) {
  // we destructured the payload when connecting the event
  console.log(player.name.get());
}
```

🚫 Not this:

```ts
override preStart() {
  this.connectLocalEvent(this.entity, myEvent1, this.onEvent1.bind(this));
  this.connectLocalEvent(this.entity, myEvent2, this.onEvent2.bind(this));
}

onEvent1(data: {stuff: string, value: number}) {
  // this will compile but the values will be undefined
  console.log(data.stuff, data.value);
}

onEvent2(player: Player) {
  // this will compile, but player will not be a Player.
  console.log(player.name.get());
}
```

Note: This problem does not affect `CodeBlockEvents`, but for consistency, you should probably register all your event handlers the same way to avoid mistakes.

### Don’t connect to events in `start()`, use `preStart()`

To avoid race conditions, make sure you run your `connect*Event()` code in `preStart()` rather than `start()`. As, at world start, the system runs all of the `preStart()` calls in all Components before `start()` is called in any Component, you will be sure that all event listeners are registered before anyone tries to send an event to them.

✅ Do this:

```ts
override preStart() {
  this.connectLocalEvent(this.entity, someEvent, () => console.log('event received'));
}

override start() {
}
```

🚫 Not this:
```ts
override preStart() {
}

override start() {
  this.connectLocalEvent(this.entity, someEvent, () => console.log('event received'));
}
```

### Never send events in `preStart()`, use `start()` instead

Similar to the above, never call `send*Event()` in `preStart()`, as some Components may not have set up event listeners yet and will miss the event if you send it too early.

✅ Do this:

```ts
override preStart() {
}

override start() {
  this.sendLocalEvent(this.props.target, someEvent, {});
}
```

🚫 Not this:

```ts
override preStart() {
  this.sendLocalEvent(this.props.target, someEvent, {});
}

override start() {
}

```

### Define Events once and export to all users

Do not re-declare the same `CodeBlockEvents`, `LocalEvents`, or `NetworkEvents` in different places in the code. Have one ‘home’ for each event declaration, export it from there to other users, who should import that definition. This way, you can be _sure_ that everyone agrees on the string name of the event, the type of the event (CodeBlock, Network, or Local), and, most importantly, the parameters of the event. This is especially important if you are changing the event definitions during the development process.

One common pattern is to have a single Events.ts module and dump _all_ the events for the entire world in there as exported objects.

Events.ts
```ts
import { LocalEvent, NetworkEvent, CodeBlockEvent } from "horizon/core";

export const MyEvents = {
 event1: new LocalEvent("event1"),
 event2: new NetworkEvent("event2"),
 event3: new CodeBlockEvent("event3", []),
};
```

MyComponent.ts
```ts
import { Component } from "horizon/core";
import { MyEvents } from "Events";

class MyComponent extends Component<typeof MyComponent> {

   preStart() {
       this.connectCodeBlockEvent(this.entity, MyEvents.event3, () => { });
   }

   start() {
   }
}
```

Another approach is to have each event in the module of the primary ‘listener’ and have it export the event so that all senders can import the definition.

A note for people using the Horizon Hub ‘waffle-converter’: due to the fact that it doesn’t have ‘multi-file’ knowledge of your system, it places all `CodeBlockEvent` definitions locally in each module where they are sent or received. You will need to refactor this code to ensure there is only one definition of each event in your world by moving the definitions to a common location.

### Prefer NetworkEvents and LocalEvents over CodeBlockEvents 

While CodeBlockEvents continue to work in TypeScript, they are less efficient to send and receive, and have much more limited payload types than either NetworkEvents or LocalEvents.

NetworkEvents can send structured data that cannot be sent in CodeBlockEvents, and, like CodeBlockEvents, can be sent across the network between different script owners.

LocalEvents can only be sent to the same script owner, but, unlike NetworkEvents, can send practically _anything_ as a payload.

Note that NetworkEvents arrive, at earliest, in the next frame, while LocalEvents are sent immediately and are more like function calls.

### Always quote asset ids as strings

When using asset ids to construct Asset objects in code, always treat them as strings, never as numbers. The id space of assets is 2^64, but the maximum integer for a number is 2^56, so there are some asset ids that can’t be represented as numbers.

✅ Do this:
```ts
const myAsset = new Asset(BigInt("123456678901234"))
```

🚫 Not this:
```ts
const myAsset = new Asset(Bigint(12345678901234))
```

### Never subclass a Horizon Worlds API class other than `Component` or `UIComponent`

As a general rule, don’t subclass classes that are not marked `abstract`, as they may not be designed to work properly as subclasses. An abstract class _must_ be subclassed to be instantiated, so generally it is safe to do so to implement the abstracted parts of the class definition. Both `Component` and `UIComponent` are abstract classes (Component has an `abstract start()` method, and UIComponent has an `abstract initializeUI()` method).

So, do not subclass `Vec3`, `Quaternion`, `Color`, `Asset`, `Entity`, etc. as the API is probably not set up to handle your subclassed versions of these classes correctly in all circumstances or for all future releases.

# Extended Recommendations

## Whitespace

### Use whitespace liberally (there is no fee for use!) 

Insert a space after all keywords (`if`, `while`, `for`). Insert a space after commas. Insert spaces around binary operator (`+`, `-`, `*`, `/`). Use newlines to separate logical blocks of code.

✅ Do this:

```ts
for (let i = 0; i < 10; i++) {
   callFunction(arg1, i / 10 + 20);
}

while (notDone()) {
  const check: boolean = doCheck();
  if (check && !globalError) {
    console.log("status is:",status)  
}
```

🚫 Not this:

```ts
for(let i=0;i<10;++){
  callFunction(arg1,i/10+20)
}
while(notDone()){
  const check:boolean=doCheck();
  if(check&&!globalError){
    console.log("status is:",status)
  }
}
```

### Put control flow blocks on their own line

Always put sub-blocks (such as with `if`, `else`, `for`, etc) on the next line. Do not write single line `if` statements.

✅ Do this:

```ts
if (condition) {
  doAction();
}
```

🚫 Not this::

```ts
if (condition) doAction();
```

### Use semicolons to end statements

Semicolons at the end of statements are semi-optional in typescript. Automatic Semicolon Insertion (ASI) by the compiler _usually_ avoids the need to add them explicitly. However, sometimes they are needed to either make the code compile or avoid a subtle bug. Thus, it’s generally best to get in the habit of always including them to avoid problems.

## Language Features

### Use `const` rather than let on local/global variables whenever possible 

When declaring variables use `const` unless you later determine that you need to edit the value. Using `const` allows other readers of your code to know that the value can never change, so they don’t need to go hunt for places where it might change its value after its initial declaration.

### Use `readonly` on class variables whenever possible

Similar to `const` for local/global variables, this information makes it clear that the value can never change, making your code more understandable.

### Mark class variables and methods `private` (or `protected`) whenever possible

When you mark variables `private` (or `protected`), it is an indication that nothing outside of this class (or its subclasses) can alter these values. This makes it easier to understand your code, and prevents certain kinds of bugs due to ‘misuse’ of the internal state of your class by other code.

### Use parameter properties on constructor arguments

Rather than writing boilerplate code to plumb through an assignment from a constructor argument to a `readonly`/`private`/`protected` member variable, use parameter properties on the constructor.

✅ Do this:

```ts
class Foo {
  constructor(private bar: string, readonly protected foo: number, public naf: boolean)
}
```

🚫 Not this:

```ts
class Foo {
  private bar: string;
  readonly protected foo: number;
  naf: boolean;

  constructor(bar: string, foo: number, naf: boolean) {
    this.bar = bar;
    this.foo = foo;
    this.naf = naf;
  }
}
```

### Use `override` on all class methods that are being overridden, especially `start()` and `preStart()` 

If you explicitly mark a method with the `override` keyword, the compiler will guarantee that you are actually overriding a function from the base class. This can catch common spelling errors such as typing `prestart()` rather than `preStart()`.

### Use `undefined` rather than `null`

TypeScript, unfortunately, has two values that are ‘nullish’. They both are ‘falsy’, but they are not equal to one another. Try to use `undefined`, whenever possible, as the value for things that have no value set on them. The `undefined` value is what the TypeScript api returns for things like accessing an unset index on an array, or fetching an un-stored key from a Map, or for an optional argument to a method that has not been supplied.

The only time to use `null` is when you _really_ need to distinguish between ‘this value has not been set’ (`undefined`) and ‘this value has been explicitly set to no value’ (`null`).

Meta has chosen to use `null` in a bunch of Horizon Worlds apis where they probably should have used `undefined`. So, you will need to adapt to that.

### Minimize escape characters in strings

When writing non-interpolated string values, use the appropriate quotation marks to avoid unnecessary escape characters. I.e. if your string has apostrophes, use double quotes for the string. If your string has embedded double quotes, use an apostrophe for the string.

✅ Do this:

```ts
const str1 = 'This string "has quotes" and stuff'
const str2 = "This string 'has apostrophes' and stuff"
```

🚫 Not this:

```ts
const str1 = "This string \"has quotes\" and stuff"
const str2 = 'This string \'has apostrophes\' and stuff'
```

### Use interpolated strings only when there is interpolation

Don’t use backticks for quoting strings unless you are actually interpolating variable values into the string.

✅ Do this:

```ts
const num = 3
const str1 = `This string interpolates ${num} a number`
const str2 = 'This string does not'
```

🚫 Not this:

```ts
const str3 = `This string has no interpolation`
```

### Use tuples rather than returning pair objects

If you need to return two values from a function, return a tuple `[a, b]` rather than creating a pair object `{first: a, second: b}`, and then use destructuring to extract the parts of the tuple at the call site.

✅ Do this:

```ts
function splitInHalf(input: string): [string, string] {
  ... code to split string into x and y
  return [x, y]
}

const [left, right] = splitInHalf('my string');
```

🚫 Not this:

```ts
type Pair {
  first: string;
  second: string;
}

function splitInHalf(input: string): Pair {
  .. code to split string into x and y
  return {first: x, second: y};
}

const value: Pair = splitInHalf('my string');
const left = value.first;
const right = value.second;
```

### Do not assign function declarations to variables or pass them as arguments 

Just use arrow functions.

✅ Do this:

```ts
const myFunction = (arg1: bool) => { ... stuff ... }
```

🚫 Not this:

```ts
const myFunction = function(arg1: bool) { ... stuff ...}
```

### Do not assign arrow functions to class member variables

Instead, just write a method on the class.

✅ Do this:

```ts
class MyClass {
  method(arg1: boolean) {
    ... body ...
  }
}
```

🚫 Not this:

```ts
class MyClass {
  method = (arg1: boolean) => {
    ... body ...
  }
}
```

### Omit obvious types when initializing variables at declaration

If you are initializing a variable to a number of boolean or specific class or whatever, you do not need to also put a type on the variable. Also, return types for functions that have obvious outputs can be omitted. Only do this when it is 100% unambiguous, tho. If in doubt, put the type.

✅ Do this:

```ts
const foo = 3
function foo() {
  return true;
}
```

🚫 Not this:

```ts
const foo: number = 3
function foo(): boolean {
  return true;
}
```

### For simple arrays of type `T`, use `T[]` and `readonly T[]` rather than `Array<T>` or `ReadonlyArray<T>` 

It is easier to read the `T[]` form, so prefer that, even for non readonly multidimensional arrays (e.g. `T[][][]`). Only revert to the `Array<>` form if the type of the array is a compound type itself.

✅ Do this:

```ts
let myStringArray: string[];
let myMatrix: number[][];
let myReadonlyMatrix: ReadonlyArray<number[]>;
let myArrayOfStringOrNumber: Array<string | number>;
```

🚫 Not this:

```ts
let myStringArray: Array<string>;
let myMatrix: Array<Array<number>>;
let myReadonlyMatrix: readonly number[][];
let myArrayOfStringOrNumber: (string | number)[];
```

### Use `forEach()`, `map()`, `reduce()`, and `filter()` instead of writing `for()` loops

These functional programming constructs are easier to understand and less prone to error than `for` loops. However, they are somewhat slower, so don’t use them in critical performance areas. However, such critical performance areas are unlikely to exist in most Horizon Worlds creations.

✅ Do this:

```ts
myArray.forEach(entry => {
  doSomething(entry)
}

const newArray = myArray.map(entry => alterEntry(entry))

const filteredArray = myArray.filter(entry => isWantedEntry(entry))

const sum = myArray.reduce((acc, value) => acc + value, 0)
```

🚫 Not this:

```ts
for (let i = 0; i < myArray.length; i++) {
  doSomething(myArray[i])
}

const newArray = []
for (let i = 0; i < myArray.length; i++) {
  newArray.push(alterEntry(myArray[i]));
}

const filteredArray = []
for (let i = 0; i < myArray.length; i++) {
  if (isWantedEntry(myArray[i]) {
    filteredArray.push(myArray[i]);
  }
}

let sum = 0
for (let i = 0; i < myArray.length; i++) {
  sum = sum + myArray[i]
}
```

### Avoid using methods on the Object class

If possible, do not use reflection methods like `Object.prototype.hasOwnProperty()`, `Object.keys()`, `Object.values()`, `Object.entries()`, etc. These are not strongly typed and can cause runtime errors if you are not absolutely sure what you’re doing.

### Prefer the `Map<K, V>` type rather than using `object` or `Record<K, V>` types for key/value mappings

The `Map<K, V>` type gives you additional functionality over `object` types, including the use of any type as a key, the `has()` method, the `size` property, and the ability to iterate over entries in insertion order using `for..of` loops.

A `Record<K, V>` is a more strongly typed `object`, which can be useful if you _have_ to work with `object` types rather than `Map`s for some reason.

### Use the spread operator `…` to concatenate arrays and objects

When combining a number of arrays together into a new array, use the `...` operator to spread the arrays or objects into a new array/object literal rather than using `Array.concat()` or `Object.assign()` to combine them together.

✅ Do this:

```ts
View({
  style: {
    ...defaultViewStyle,
    margin: 10,
    padding: 20,
  }
})
```

🚫 Not this:

```ts
View({
  style: defaultViewStyle.concat({
    margin: 10, 
    padding: 20,
  })
})
```

### Export only what’s needed

Only export type definitions, classes, and variables that actually need to be imported elsewhere. Default to not exporting things until needed. Only use named exports, do not do `default` exports.

### Consider destructured imports

Rather than doing `import * as hz from 'horizon/core'`, import specific types/classes that you need, i.e. `import { Component } from 'horizon/core'`. Only importing what you need makes it clear to other people what the code in your file is doing, and is slightly better for compiler performance and optimization tools. VSCode will help you auto-manage your destructured import list. If you need to rename something to avoid a collision, you can use `as` to rename the imported symbol in your file.

## Naming

### PascalCase for types, camelCase for variables/functions, CAPITALIZED_SNAKE_CASE for global constants

Classes and type declarations should use PascalCase (each word capitalized). Variable and function names should use camelCase (first word lower case, remaining words capitalized). Global constants should use CAPITALIZED_SNAKE_CASE (each word capitalized, separated by an underscore). Do not use snake_case mixed with PascalCase, or PascalCase for variables/functions. It should be immediately identifiable whether something is a type or a variable/function based on the naming.

Examples:
```ts
const DEFAULT_POINTS = 3;

type UserData = {
  totalPoints: number;
}

class MySpecialClass {
  addPoints(userData: UserData, newPoints?: number) {
    userData.totalPoints += newPoints ?? DEFAULT_POINTS
  }
}
```

### Use descriptive names

Name your variables, functions, classes, etc descriptively. Avoid single letter or very short variable names in all but the most simplistic cases. You want to write ‘really obvious code’ so that someone else or a later version of you will be able to read it and understand what its doing just by the names of things.

### Don’t use `_` (underline) prefix for private member variables

It’s really not necessary, and makes the code harder to read. Most of your class member variables should be private, anyway.

## Layout

### All imports at top of file

Do not intersperse import statements throughout your file. Put them at the top. Also, keep them organized in alphabetical order and remove unnecessary import declarations. The VSCode ‘Organize Imports’ function can do this for you. Run it every so often to clean them up.

### Organize files with type definitions first, then globals, then classes

Having a standardized organization for where different sorts of things are in a file will make orienting people to your code easier when they first encounter it. Put type definitions first after imports. Then declare any global constants or variables used in the file. Then declare your classes. Register each of your Components immediately after their declaration.

### Organize classes with statics, then variables, then methods

Having a standardized organization for where different things are in your class will make orienting people to your code easier when they first encounter it. Put static variables first (e.g. propsDefinition), followed by any static method. Then put instance variables, and finally put instance methods.

```ts
import { Component, PropTypes } from "horizon/core";

type MyData = {
   info: string,
   price: number,
}

const DEFAULT_PRICE = 10;

class MyComponent extends Component<typeof MyComponent> {
   static propsDefinition = {
       prop1: { type: PropTypes.String, default: "default value" },
   };

   static dataMap: Map<string, MyData> = new Map();

   static addData(key: string, data: MyData) {
       MyComponent.dataMap.set(key, data);
   }

   readonly myKey = "key";
   myData: MyData = { info: "info", price: DEFAULT_PRICE };

   override start() {
       console.log("MyComponent started");
       MyComponent.addData(this.myKey, this.myData);
   }

   myFunction() {
       console.log("MyComponent function called");
   }
}
```

## General

### Avoid clever code

Like using descriptive names, you want to write ‘really obvious code’. Don’t use obscure language features in ‘clever ways’ or try to condense complex code into a small space such that its purpose is inscrutable at first glance. Do not try to ‘show off’ to other people by writing very convoluted code when more straightforward would do. If there are reasons to do something ‘clever’ or use esoteric language features, make sure you add _copious_ comments describing what you are doing and, more importantly, why.

### Comment the important things

A ‘future you’ will thank yourself if you write down what you were thinking when you wrote your code\! However, do not comment pedantically, and make sure you have [used descriptive names](#use-descriptive-names) for your variables and functions, as that can make extra comments unnecessary. Only comment the _important_ things, and comment things that the VSCode hover popups will pick up, such as documentation comments on functions describing its effects, parameters, and return values.

### Write TSDoc comments on functions and on important variables/classes

When you use the TSDoc comment format, the VSCode editor will be able to pick them up and display them as popups when you hover over the identifiers usage elsewhere. You do not need to include types in your comments, as they are already in the code.

Comments on functions and methods should be of the form:

```ts
/**
 * Description of what myFunction does
 * @param paramName parameter purpose
 * @return information about return value
 */
function myFunction(paramName: type): returnValue {
}
```

On variables or classes, it should just be a block of descriptive text

```ts
/**
 * Information about myVariable
 */
const myVariable: number = 3

/**
 * Information about MyClass
 */
class MyClass {
}
```

### Separate unrelated Components into separate files

If you are writing code for a bunch of Components that are closely related, sometimes it is useful to put them all in the same file for organizational reasons or so that they can share some types or global variables that don't need to be exported.

However, avoid writing really long files. Also, do not put unrelated Components in the same file, as it makes code organization less clear, encourages accidental and unnecessary coupling between Components, and reduces reusability of your code.

### Use line comments for internal implementation details

A line comment starts with two slashes // and continues to the end of the current line. Use these inline in your code to describe interesting details of the algorithm or other important information. Prefer multiple lines of single line comments using // to multi-line block comments using /\* \*/.

✅ Do this:

```ts
class MyClass {
    
  someMethod() {
    someCode();
    // someDisabledCode1();
    // someDisabeldCode2();
  }

  // someDisabledMethod() {
  //   thing1();
  //   thing2();
  //   thing3();
  // }

}
```

🚫 Not this:

```ts
class MyClass {
    
  someMethod() {
    someCode();
    /*
    someDisabledCode1();
    someDisabeldCode2();
    */
  }
/*
  someDisabledMethod() {
    thing1();
    thing2();
    thing3();
  }
*/

}
```

# TL;DR Plugins

## Coding Conventions

Coding conventions discussions can be contentious, with ‘religious wars’ about where spaces go, when to use newlines, how many statements can go on a line, etc. One way to avoid those pitfalls is to just leave it to someone else to argue about what is the ‘best’ way to format code, and just accept whatever they decide. Additionally, it requires the expending of extra mental energy to _apply_ the conventions while you are typing your code, so having something do that automatically frees up brain cycles for something else.

### Prettier

Fortunately for us, there is [Prettier](https://prettier.io), an industry leading coding conventions enforcer plugin that has made the decisions for us, and will apply them automatically. They have a good summary on [why you should use Prettier](https://prettier.io/docs/en/why-prettier). In particular, they have an [opinionated philosophy](https://prettier.io/docs/en/option-philosophy) on code formatting, so there are fairly few options to configure how it works. It’s best to just leave their options on their defaults, and accept that they have thought long and hard about _why_ their way is the best way.

#### Installation

To install prettier in VSCode, simply open VSCode, go to the Extensions tab on the right sidebar (the three cubes with the fourth cube offset), search for “Prettier”, click on “Prettier \- Code formatter”, and click Install.

![][image1]

For other editors, check out the [Editor Integrations](https://prettier.io/docs/en/editors) page.

#### Configuration

The most important configuration setting to make is to ensure that Prettier is used as the default formatter for typescript files. If you don’t currently have a formatter configured, and you are in a typescript file, and run Format Document (Shift \+ Alt \+ F), you will get a popup asking you to Configure a default formatter. Make sure you choose “Prettier \- Code Formatter”.

If you don’t get a popup when doing Format Document for the first time, it means you already have a typescript formatter configured. You will want to change that to Prettier by going to File \-\> Preferences \-\> Settings, typing `@id:editor.defaultFormatter @lang:typescript` in the search bar, and switching the Default Formatter to ‘Prettier \-\> Code Formatter’.

![][image2]

By default, Prettier won’t format anything until you tell it to via Format Document or Format Selection. However, you can also configure VSCode to Format On Save, On Paste, and On Type. Note that if you have File: Auto Save set to `afterDelay`, then Format On Save will only work on an explicit save.

To find the formatting options in File-\>Preferences-\>Settings, search for `editor:format` and tick on whichever options you prefer (below shows all of On Paste, On Save, and On Type enabled).

![][image3]

There are a number of other minor settings you can adjust, listed on the [options](https://prettier.io/docs/en/options) page and via the ‘gear’ icon on the plugin, such as Tab Width (default 2), and Print Width (default 80). Those values are a bit of an anachronism from when people didn’t have very wide monitors. However, if you make customizations, make sure you put a [configuration file](https://prettier.io/docs/en/configuration) like `.prettierrc` or `.editorconfig` in the scripts directory of _each_ of your horizon world projects so that others using your code will pick up the same settings. Thus, it’s best to just not change them at all.

If there are particular files that you need Prettier to ignore, you can add them to the `.prettierignore` file (in the [same format as .gitignore](https://www.w3schools.com/git/git_ignore.asp)).

#### Usage

If you have precious but non-standard formatting somewhere in your file that you want to preserve, you can precede that block of code with a comment of the following form:

```ts
// prettier-ignore
```

This will prevent Prettier from reformatting until the syntactically next statement is reached (which can be one line or many lines, depending on the type of statement that follows the comment).

##

## Best Practices

There are a variety of language features that are often misused, or just bad to use at all. These are more complex ‘code quality’ issues rather than formatting conventions handled by plugins like Prettier. The class of plugins that check for these code quality issues are typically called ‘linters’, as they remove [‘unwanted bits of fluff from your code’](<https://en.wikipedia.org/wiki/Lint_(software)#History>) (i.e. the ‘lint’). Linters are _very_ powerful tools and often have a bewildering number of rules that can be configured. This is a more advanced helper than coding convention formatters like Prettier, but will identify subtle programming errors in your code and make your code more robust and bug free. Reading the web pages associated with any linter errors found in your code will teach you how to be a better typescript coder.

### typescript-eslint

The ESlint linter has been used for years to check Javascript/ECMAscript code, and now has a variant called [typescript-eslint](https://typescript-eslint.io/) specifically tailored for Typescript that uses typing information provided by the typescript compiler to enhance its ability to detect coding errors. (note: there is an [older TSLint project that has been deprecated](https://typescript-eslint.io/users/what-about-tslint). Use typescript-eslint instead). (Also, note that while ESLint can do coding convention formatting as well, they [recommend leaving that to Prettier](https://typescript-eslint.io/users/what-about-formatting)).

#### Installation

Installation of typescript-eslint is significantly more complicated than installation of Prettier, which is just a VSCode plugin. Installing the ESLint plugin within VSCode is part of the process, but you will need to also have a minimal Node.js installation, and use npm (or yarn or pnpm) to locally install and configure ESLint into _each_ project you want to use it in using command line tools. You will need to know the directory of your projects scripts directory, which you can easily get from both the Desktop Editor and VSCode.

- Desktop Editor \- Open Scripts Folder in Explorer  
  ![][image4]  
  Go to the scripts menu, press on the triple dot menu, and select Open Scripts Folder in Explorer (note it actually opens the folder \_above\_ the `scripts` folder, you will need to click on `scripts` once the window opens to go into that folder)

- VSCode \- Reveal in File Explorer  
  ![][image5]  
  Right click on your `tsconfig.json` file and select Reveal in File Explorer

In the File Explorer, you can then click past the _end_ of the location bar text (don’t click on any of the text), and copy the path to the `scripts` directory to the clipboard for use with your command line terminal of choice.

![][image6] It should be something like `C:\Users\live\AppData\LocalLow\Meta\Horizon Worlds\eslint test_10160832935606146\scripts`

##### VSCode ESLint plugin (one time)

To install ESLint in VSCode, open VSCode, go to the Extensions tab on the right sidebar (the three cubes with the fourth cube offset), search for “ESLint”, and click Install (same procedure as for Prettier).

![][image7]

##### Node.js runtime (one time)

Download the latest Node.js LTS package from [https://nodejs.org/en/](https://nodejs.org/en/) (current version is v22.11.0) and install. Use all the default installation options (don’t enable or disable any options).

##### Typescript-eslint command line tools (every world)

Open a Terminal prompt (e.g. run ‘cmd’ from the system search bar), change directory to the `scripts` directory of your world you obtained above, e.g.

```bash
cd C:\\Users\\live\\AppData\\LocalLow\\Meta\\Horizon Worlds\\eslint test_10160832935606146\\scripts
```

and run:

```bash
npm install \--no-save eslint@^8.56.0 @eslint/js typescript@4.7.4 typescript-eslint@^7.18.0 eslint-config-prettier
```

(Note: if you are using the yarn or pnpm package managers, see instructions [here](https://typescript-eslint.io/getting-started/)).

(Note: we need to pin to older versions of eslint and typescript-eslint because horizon worlds is running such an old version of typescript).

(Note: due to current shortcomings of the desktop editor, you must run `npm install` after each time you open the desktop editor on the world, as the desktop editor will clobber the previous installation of eslint).

As above, you **must** install locally to the `scripts` directory for _each_ world you want to use typescript-eslint. [It cannot be installed ‘globally’](https://typescript-eslint.io/troubleshooting/faqs/typescript#should-typescript-be-installed-globally-or-locally).

##### ESLint configuration file (every world)

Create a `eslint.config.mjs` file in your world’s scripts directory. Be sure to exclude the `types/*.d.ts` files, as the code there is not well formatted and will cause _lots_ of error messages, and the `.backups` directory that contains old deleted code.

```ts
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';
import eslintConfigPrettier from 'eslint-config-prettier';

export default tseslint.config(
  eslint.configs.recommended,
  // @ts-ignore
  tseslint.configs.recommended,
  eslintConfigPrettier,
  {
    ignores: [
      'types/*.d.ts',
      '.backups/**',
    ]
  },
);
```

##### Installation Test (every world… tho can skip once you have the process sorted out)

Create a file in your `scripts` directory (e.g. `ESLintTest.ts`) with valid typescript, but an obviously inadvisable language construct, such as the following variable declaration that uses the any type.

```ts
export const foo: any = true;
```

Test that you are able to run eslint from the command line from your scripts directory, and that it detects this error

```bash
npx eslint .
```

This command should generate an error message on the above file:

```bash
C:\\Users\\name\\AppData\\LocalLow\\Meta\\Horizon Worlds\\eslint test_10160832935606146\\scripts\\ESLintTest.ts
 1:19 error Unexpected any. Specify a different type @typescript\-eslint/no\-explicit\-any
✖ 1 problem (1 error, 0 warnings)
```

Next, open VSCode on your same project, and click the same test file (e.g. `ESLintTest.ts`) and verify that the “Problems” tab of the IDE shows the same error message for this file.

If you click on the underlined error message in the Problems listing, a web browser will be launched that explains why what you have written is a bad idea (in this case linking to [@typescirpt-eslint/no-explicit-any](https://typescript-eslint.io/rules/no-explicit-any/)).

If you hover over the error in the code and click `Ctrl-.` (for Quick Fix), you will see that the first suggestion in the pop-up is to ‘use `unknown` instead’, which is a much better option than any.

#### Configuration

The installation configuration above installs the [‘recommended’](https://eslint.org/docs/latest/use/configure/configuration-files#using-predefined-configurations) javascript rules, [‘recommended’](https://typescript-eslint.io/users/configs#recommended) typescript rules, and disables any formatting rules in ESLint that replicate or would [interfere with the Prettier](https://github.com/prettier/eslint-config-prettier?tab=readme-ov-file#eslint-config-prettier) plugin. However, there are \_lots\_ of deeper configuration options you might want to consider, and a number of additional [‘shared configs’](https://typescript-eslint.io/users/configs) that you can use, such as:

- [recommendedTypeChecked](https://typescript-eslint.io/users/configs#recommended-type-checked)

  - Uses the power of the typescript typing engine to do even deeper checks on your code usage

  - Requires a minor amount of [extra configuration](#extra-configuration-for-eslint-recommendedtypechecked)

- [strict](https://typescript-eslint.io/users/configs#strict)

  - Includes everything in ‘recommended’, plus somewhat more ‘opinionated’ rules that may not be applicable to all codebases

  - Also comes in a [strictTypeChecked](https://typescript-eslint.io/users/configs#strict-type-checked) variant with extra typing checks

- [stylistic](https://typescript-eslint.io/users/configs#stylistic)

  - Additional opinionated stylistic rules that don’t impact program logic to be used alongside either ‘recommended’ or ‘strict’

  - Also comes in a [stylisticTypeChecked](https://typescript-eslint.io/users/configs#stylistic-type-checked) variant with extra typing checks

The full list of **all** supported rules can be found here:

- [ESLint](https://eslint.org/docs/latest/rules/) rules reference

- t[ypescript-eslint](https://typescript-eslint.io/rules/) rules reference

The ESLint plugin to VSCode also has configuration options as well. By default, the linter runs as you type, which is usually the best option, as you want to know as soon as possible when there is a coding problem. But, you can change that to only run on save, if you so desire.

#### Usage

If you find that some linter error is incorrect or too aggressive for your code, you can insert a comment above the line causing the problem to tell the linter to ignore it. The Quick Fix in VSCode can help you format these comments, but they are generally of the form:

```ts
/* eslint-disable-disable-next-line <rule-name-here> */
```

Where `<rule-name-here>` is the name of the eslint rule that is reporting an error. You can disable eslint errors or the entire file using the following comment at the top of the file.

```ts
/* eslint-disable <rule-name-here> */
```

You really should endeavor never to disable the alerts, and instead fix your code so that it compiles with no warnings or errors.

## Spelling

When there are misspelled words in your code, it does not only cause confusion for others reading your code and trying to understand what you were trying to accomplish. Sometimes, there are certain coding errors that can’t be caught by linters that can be caught simply by matching spelling with _well designed_ API definitions (i.e. ones that follow good spelling rules as well).

### Code Spell Checker

This popular plugin checks your code and comments for spelling mistakes. It is easy to add new words to a personal dictionary should you have some particular jargon that doesn’t appear in the standard dictionary.

#### Installation

To install Code Spell Checker in VSCode, simply open VSCode, go to the Extensions tab on the right sidebar (the three cubes with the fourth cube offset), search for “spell check”, click on “Code Spell Check”, and click Install.

![][image8]

#### Configuration

There is not a lot of additional configuration need, but there are a few additional options if you check the plugin settings page. It will highlight spelling errors in your code as ‘warnings’ of ‘Unknown words’in the ‘Problems’ view of VSCode. You can use ‘Quick Fix’ (`Ctrl-.`) to get spelling suggestions, and if you want to add a word permanently to the dictionary, you can select ‘Add Word to User Settings’ (note, don’t add to Workspace Settings, as the Horizon Desktop Editor will overwrite that file and delete them). You can also access the Spelling settings by right clicking in the editor window and selecting the ‘Spelling’ sub-menu.

# Appendix

## Extra configuration for ESLint recommendedTypeChecked

The typescript-eslint documentation explains how to [enable type checked linting](https://typescript-eslint.io/getting-started/typed-linting).In particular, you will need to use a TypeChecked version of one of the shared configs, and you need to configure ESLint to use the typescript parser on the current directory.

Additionally, though it will work without changes, to avoid errors in VSCode, we can’t use `import.meta.path`, but must use a legacy Node.js api instead. And we must prevent the typing pass from including the ESLint configuration file, as it is not part of our typescript project.

Change your `eslint.config.mjs` to the following:

```ts
import { dirname } from 'node:path';
import { fileURLToPath } from 'node:url';
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';
import eslintConfigPrettier from 'eslint-config-prettier';

export default tseslint.config(
  eslint.configs.recommended,
  tseslint.configs.recommendedTypeChecked,
  eslintConfigPrettier,
  {
    languageOptions: {
      parserOptions: {
        projectService: true,
        tsconfigRootDir: dirname(fileURLToPath(import.meta.url)),
      },
    },
  },
  {
    ignores: [
      'types/*.d.ts',
      '.backups/**',
      'eslint.config.mjs'
    ]
  },
);
```

## Other Typescript Coding Conventions

- Google  
  Very extensive with lots of good examples  
  [https://google.github.io/styleguide/tsguide.html](https://google.github.io/styleguide/tsguide.html)

- TS.dev  
  More or less the same as the google conventions but in an easier to navigate format.  
  [https://ts.dev/style/](https://ts.dev/style/)

- Typescriptlang.org  
  The official website for the typescript language. Limited advice, and mainly for some more advanced features.  
  [https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html)

- Microsoft  
  For the _contributors_ to the typescript codebase. They take pains to say ‘these are not official’.  
  [https://github.com/microsoft/TypeScript/wiki/Coding-guidelines](https://github.com/microsoft/TypeScript/wiki/Coding-guidelines)

- Angular  
  An opinionated set of conventions targeting angular developers. Some good ideas, but some parts not applicable to Horizon Worlds  
  [https://angular.dev/style-guide](https://angular.dev/style-guide)

- Mkosir  
  Another thoughtful set of conventions  
  [https://mkosir.github.io/typescript-style-guide/](https://mkosir.github.io/typescript-style-guide/)

- Basarat  
  Yet another take on conventions for typescript  
  [https://basarat.gitbook.io/typescript/styleguide](https://basarat.gitbook.io/typescript/styleguide)

- AWS  
  Amazon’s advice for typescript best practices  
  [https://docs.aws.amazon.com/prescriptive-guidance/latest/best-practices-cdk-typescript-iac/typescript-best-practices.html](https://docs.aws.amazon.com/prescriptive-guidance/latest/best-practices-cdk-typescript-iac/typescript-best-practices.html)

[image1]: <data:image/png;base64,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>

[image2]: <data:image/png;base64,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>

[image3]: <data:image/png;base64,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>

[image4]: <data:image/png;base64,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>

[image5]: <data:image/png;base64,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>

[image6]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnAAAABXCAYAAACEJXgEAAAnsUlEQVR4Xu2de7QlRXWHr1nEBCWCIBgGHB4DDAIyMA8GAggoBF8gRhEjYkDBIQgIcfFQwQDCChDECJr4iERc+AQUNIlB0ACKJgYNvlDE8NIlahLQxGjWyh+Vs0/dfbv6t6uqq/p1uu/dvdY35/SuXdXVffpWfbfOOXfmttxhpXnadjubJx/2BvM7T1ibztwaP09gVnt5Sivs7bBXIzb1YMtWedk0hzlizwIs9zGtI9lsStEWPa/PszJJqY85qdSr/9RJnTQo12LrMZhnc+Vx/GDeLCjOQ/avDJ6n/1xlu23j69MeDpjfBm77qcfC3Fx2n7L5ArtFcPMQzI2Bdeu204RndsiuU7bIoE4dy0oAy305Q2YXT8wH5Sl98LQpOzdmbsvtdzFPOuKt5okHXCwlLcScR+AWxC0scVLEKphzRS2GFLN2qCFvrnhhmQ+PrCFW3gqaC5wfO6mjmOXLVR/4ZSAG1vHjPw62ZdvD3Fzw2FX46mPMh/8cwu2G68TrhevIurIsRJ06LihXBOYgmJ+DlTcpcTFIfjCWI15Yr04bTUDZahspYhwLIYWrC1CAxowrGFiG5cosceVvKnBPPO6DZuNdTppI1roAOStvfoScVUHylixwOaCkxcHVuSgoXyhrCOYHQIGjyTK0EheKp2AnL3o+e4GTkyqWI5jvI1S3oLof/v7UAY+dS7k9vEeKe8UeC/tfnIdsK9w/zEFk+/JYFixD5PHS6vlAycJyBPNTKctbFVKycuUL831gnS5A4QpRpw6B4iSFDcH8bkDJGSuuIGCZL0eZNSxxc0/bfvLPud/2SJvD3Fqz2UZrzFabrDbP2GrC0/f2sFeQ5bVZNQL2bJ3t5sF9jPvqjJ3lW+5ptnnqKrPFRlIO5GSeLwTV+WVx8OdieRnsa4icXM4P9bMK7GNKfzEPCefj9Sr32Q/mhupgeVegnFUhBa2KsmSheLUJCpd7PIzXAYXLlTWM10EKWhVStroAJWcxIyVCmS0kcXP0+bcqgdvyyXubbbdcY7bfep3ZYdn6Mtu47ONlxyTWmR2XARRrlbXtsczBV4b7iawAMI75vjpx1syQtD7suPXqicitMk9/8h5msydIUQjLhTsBYxmCE3YZPF4If1t4rDLYRg7F8fLawz6k1JfnVT5Hme/2D/EfL15niLQhbM1otz2UsVxQtoiq8lykoMWQotUlKDqLFSkQyuyZCNxOYYGbW2eeNpE3IW1BgSuLnJS0CF3ImyM5QrKa4LZb1TbmRpCyJXN8YJ1qpDR1T/0+oCiU5cJXVgVOyj6kbPiQ9WQbCLZhwbc+Effc8Djp/c1BHsOHPL8CzLX55WOE8oZKrsAVuVKewrhthOJYVg8UshxYsEL7bqxrZiFvLig8Q4cEAGM+pDQowyEqcJtutJfZ7nc9q25ReXMEblkMEqwIKGOtsLYZywJgXlV+BBSdqvJ8pCD1R70+bL7R7h65CAtRXCBy8R+jOBbml+tiflOkJPmvBcZTKdfH8/Eh2yjA3FA9LBsy+NYpIiWrjmzF8uu0FwalrE1Qsrpk1gLHoACNHSkNltQ8pUv8Ajdn2XqzNVLaquRtImcxKgVOSFcCtdpYK1lWA2yjSVsJSCELIUUoTpO63dbfZjP6MsceQZGRkoOTrg9/3Wp5SREP2WZ4Nc2PrJ+H7FMOVedXzsNj2+On1K9Ll20jKGipVAscliOY3w4oWT5y8xGUqyrq1nMJC1ysrH1QbMaKlIUCzK3KV5Cmf0aE2ykEbm6fBXFjlm9F0uWRtxoCl7wCJ0QrgbptLCMp8tRfQApUCZS2juWNkKIWAgWpiiZ1u62//VarJwJXrMKh7KBEyEnYh6yXJx+xPNmmX+CwXrlt2UY6ss06yOvsA49tj99WH3x02TaCYpZKWMgwHkLKVxugbIXIzUdQsLomLGqheDeg2IwVKR0FmFuVryAdCFxZ3gghbZXyliNwPjyS5UOIFoD5IZaRFHnql5ASVaJjcZNilguKUoxYfqwsluOLhQjnPmUyKZTlACdxFAuciENICamuX5Ur2/Qh+1yAubnIfsljSPznUQUe23/8ENgels8aFLNUpJDlIuVLgnWq66OgYbwtULD6Qn6pQQWuD6SkKGGaChzVp3byBW5e1v7n07854bfMf9/82+a/bnqS+e4HNjd7rlgrxC1P4CbwN1J9CLkCMD/EMleSPO3kCFyHSBmzpOZVI0WpEKkYmI9gPoL51TxlMjgXcoCTLCKFop5c+Mhpr1p0hoY8FxQsCbbRBHn8NkhtFyUMyc2vQkpX2/gFDeUNy9uG5QrjblmXlCUL99uDRMZ97qOqfIxIQVHiNBc4S22B++VE3n756Y0n8vZk84ubNjE//9Sm5vFPbmYeu3Fz8583bmH+44YtzTfeu81EMNbP45G1EPgNVd+3VH2gqPnAOpVIYeoDKT0FOblxpChZMA/BfKTIvej8K8zpJ7+pFHv2Pi/y1IlTFriqyViKQXuS0KS99oWnbcrnUha1ENhGE9KvZV4f09pFwXLB3Kr8FKRwpZNWXwoaihuWdwGLFMbdsi4pJKu7FTmWmVAcyzE+ZqSgKHH6Fzh4u9T8+83G/OxGYx69zpgff9CYR6405sG3GfNvbzHm/nON+f6Zxtx3mikEzsUjba68CYlaZ7577/0ixjz645+aC89/u5S1RuLGSGGKk19XCtIskfJU4Cuvrk8CxxJHr9WHrvm4yKli07lC4GKUJ2ucwLG8LqFjVhFuoy1kX4vjVR2z3E9Z3gR5/uV+STDPR05uG6CA5SIFqx557UlZwzhKVdegYHWNfDvVh5SvrkAB8lGnTt9IMbFUlSttkSZw+Fk3ErgfvcuYH77TmIcvN+ahPzPmgQsm8nbeRN7OmsjbnxjzvVOM+e4GI+UtInE5q20OtAmB8+TlI2Wrmvz6UoIKcnLjsAxh3IcUKD9YL1z/8cd+bi46/89ryRuRKnBWFnDizRWDFIo28fg+fMfGnDbAY0hQlMJg202Q/Qgh+1ENttEVKGRESk6ecMXxt4dyloqUq65BueoaKWoWmSOlqStcCcKyGChQs0YKhaWqXGkL+BYqExG4+Zh56FJjHrxkIm5vNeYHb55I2xsnnDGRtpMnnGTMt48z5luvMFLcCoG79ZY7zYH7vHj6nKDVGeLqd3xgWkYbPVIZbfT44AOPmGuv+cSk3pHmV7/69fSRBe7WW+6Yz0MR65KQvGFZGClAs0TKUwGWY10sL6DX9bbJ62NX4mR5FTkC95pjzjI/+P7DXt539UdM3qSPshCrW+Rgn6TEyHJEHlfmIJiPYH6sDuY1AdsOUaeOfB26JCRvKGshpERZMK8qvznxFbhYWVNQsohQvC9QlrqE5AZjKaBAheJDQMqG0j4BgbMSVxI4fs4C9+BFE3n7U/t2Ka263Xf6RNxOMeY7J0zk7dXGfPPlxnzjD4wUt0LgSLpIyEjYWNKmK2nzZbSx4LkCx8LH+fzIz217KFPztLpCV0VI7CwrpljxGY7USXmyxMrj9ekzb7zyRitxp598rsipwv0MXPUEj5MukZLjA+ul1LV5/v6Gy/25SNEPrNMEeY7dHCcX7EsYvE59g+LVFlK8moBvpZYlDeNY3gcoVn2BsjREXEEKxYeAFA2lGyICR98e3Xcej8Ddf/aENxrzvVPtqtt3/mgibq+aSNvLJhxlzNefZ8zXDjVS3Ar426n0+TYSM1pRo0cSMVplY2lDgaNyEjuSARIhlDZuD4VpSm8C5woZlllYelDe+hU4KUuSOnUsJG74tmlTgauesH0Tu08KsJ6PIj/t2EU9lJEc0o7jnotsww/+F13279JhW/ntdgP2JQ28Tl2D0tUmUsLqIsUsFZSsVFiOMJ4DilYX9P0Wag4sRRj35fQBiQPGECkaSjdUCJwUtwWBu+8MK2/3nmRX3b71CmO+ebQx/3rERN6eb8zdhxjzLwcalDZ3Be5TN/z9VL7o7bXTTn7zFBY5ErKQwBG0UT6JEO9jeyhMU3oTuGpYiOz+UAQuVOaL9UeawNkyOfkXZWlgu+X2ZRzJk5CwtPiOXa7ni9UF22rankX+AeMccvpR9Btfjy5A0eoSKWO5SCnLAaUqFZIjjOWCstUFs/oyQwosRRj35QwFKRqS1DwlRljgnhAXOEQKWozMvw3nEFxZq2KQ4uZfoZOSVdBM8FCMsNwH1pkN9Bm4+OQsJ/RiYi/nxXMxP0S67Mi6MdLblf1OB+u3D67y4f9CkY5sO4ytg9e0bVCwhkpY3sKChwKVCokQxtoAZatN8IsN7pcbhihzPlCemJScLpCSIcnJXZzU+TMi2AYRFriFt1ClrPmQkuannC8FrYriM3BSjKIMTuDouRQ4KVBl6guclCKZ4wPrzAYrcDyBuhN8EcPJvJjUcQIOy0G6AMi6IWTdFPz9C4HSUwXWbx8Ut/qrcDmkv35NQVkaKlLemgucL5ekB/PaAKWrTULyVpRLYRoaKFBMSk7bSMHwk5u/OEFBqwLrEwkCFyJP3KS85SBFrhopTkNHylNdWcsBZQnLY7n9wStw8QnbLycoL3Iy7orQsUL9TMtrQhdtxkGBa0/i5Ovqu4byekowry1QprrEFbJQXIJiJwVqqKBsdQHK0tBAgSJScrqCxQLjiBQRpR4lgdukQ4Grs1pXgHKWipSkoSJlqa/PxElZkjlV+d3j+xZqMZG7k6Ysl5M+TrQ+upzkZb/8fcM++MF2YtSp0xwUuFxsO3jeFry2IbBenTbqgKLVBVLMZI4/V0obCp0vZwigbOXgW23zgTI0FFwZwrJQXpegWGA5gvlKDu6qXGQFjv6MCEpbGSlakmbixqCYpSAlaai4f04E6VbeGFeWQvHZsvlGuy9M5AhOyLGy6gkbc1Pq5CD7WO6rzE0B24qRmy9BweoSPHZB7BrYGF77WYAC1QWp8iZzpaj5QHkaAihbbTPULzUQLEIY94HyNASklCjp2M/PWYICt8ncGiFsZXljULbKNJM3lLJUpCRVU7dec2ICN1ukSM2Kp28sJ/CqibyI4aTqEqqHeW0ihcN/XH+e75xDed2AktUEbLs95PWcBShQswRFD0XNB4rTUEDhahP8bNzQZI5FCOM+UJ5mjRSSdNpoY3GxIHCbzO29wBYbrzbLtyLxkuKWtvpGOW2svqGYpSAFKY2m9dOxcsTiNkaBS81rly033sNs9hu7Td9OTYE+NxcD83Pq9gn2LdQ/zGkfuvZtg8doF7xGw2FXD1U5K+fBeApct5rNSuwyQHbumJ08yLKnjoIVDrjfNzs2oK12FhcLAoeCFkOuvLkrcm3JG4Fy1gZSqCyxsnZhacN9KUZ9gKIUK1MUvH+agu0vJVYHqCrvlp1K7L0E2QvAcl/OUFnlgPvK2MkWOPnWqX2O0tZc3lJAOctBilWcuvUkrry5MTm5dQ1OKExVubK0wfuoT7AvY0bK01DwS5wvtlRBURoqPNnjvrIYmNtieROB47gUt+4FDoWsDlKkusR9yzRW1p/M4YTCYF5KHWVpgfdFX2A/xowUp6FQlrUQKDVjhgUH4zFQlsaElAFlfGQLXFnisKwveWNIwtznTZBS1QVVAlfs48TVFTipKAreI0MC+zp2pDwNASlrPlBoxgxJDcZioBCNESkEyrhIEjgpan7kZ+NmCQpaLlKy2sJORHLFTea0iTtpYBmCk4yyNMD7oA9yj4999pGTO2akeOUipSwHlJq6dNVuV6AIjRUpBMq4UIHrQdgQn7xZ3BycuGy5jKXiDvxYhuBEoSwd8F7oEjx2W3Td/lCQQpaLlKccUGya0FW7XeBKEO6PCSkEyrhIErgU5JcbZgnKGZaH8mYldDixSXJy88BJIcyqlQcF2WseGX92Blg3Vj98zHj9op5bPxzD+uW25PEk2K8iH9vzt6u0A74uErw/8HVZbBy4yDggQFV5N+w9CvZXOgSFi9l5m73Ms1asn+as3rX+6+AI3H4OUtBidCdvKRKGoIiF6mIOIkWrK6RUITm5OUhJC7Fy+XoxIaYgJ40Qsm56/fJEXK6PbeCk7Sft+LK/qch+yT5inTrgecXAuksJvBYWfF0WGyhAY0YKVEFKTrtIURoqUgqU9th1+Rohb8Ru268VuXXwCJwUtBjuFxqkJDUlJF8xUuugsCFStLpASlWfSFELgROeDxSBvEkw3J7MRfwiIuv7Ygi2geUWfz/D4LnF6mGe0i3FtU+9RxY7KEdjQErUrJGyNHRYDEJxpQ4obwTm1AUETgpaFWWB60LiugBlzYcrWr5YPZp9jq1tpKiFwEkPkRMe7qdSSEz6RFoWIH99X8xHUb+cL9tPa69oF6+ZMjvw9Sy/hrjfNl233xYoSWNFylUfSBGyxMqGiRQHJR2Ut0EInBS3EChPbeIKF5aFQFFLRcpYLmnyxrkYbwMpZqngBIhUT0gpOSF8k2yo3Jdj42l9LedWEW+rDNZdnBwcAXNnQ/X9goTvCWwLy2PI4wwRlKExIuWqL1CGQvFhI8VBSQflbbAC5y9DgWqTHHFz66QgBawpUqhC5OTmIMUsFZx8EJ4I7/jCXeab93wnyGkbzpnm5VE12eJE6i/HvkqwHT/+c68G6ymzBV+fKvz18+6dMnj/DRGUobEhpWpWSDEaC1IclHRQ3joUuBT8okb7B65/ibesP6GzPHv9UfPPUcpykAKWwmknv9lce83Hp8+lQJXhOhivy7P3OcI8+MAjC3zqhr8zt91yh7no/CtErkWKWoiUCe+UE8+aPpYnufKE8MXbv1JD4vBY/rLQMbG8CnmMMFg3BXk+frBeKm20ocTJuUfGBQrQmJESVSY3vzlSjsaAFAclHZS3wQicC8nbqRvesrAvxa0feSNO3fDmeYlDKctBylkVJG+PP/Zz8+iPfzqVOClMZdoUOJY3N/ahSR/u+uJXIwJHSFnzUUxaYU7bcK5nonMnh4PMF269MyJwmO/GQ2VFuf+YsryK+LHKUP7DD/1QtEHQdtnFV4l4VV9y+4vUrXv7579U2qcVU8zxccKxp03PFeNN+Nh1n1x4TteQjsH7Dz/0I9FXH9R/X17o9coh5x6ph22fzoEe6TxkTheg1Eh+/vgvzGuOPXUhn7fLLn5nKY+u82UX/8XC/seuu3Eh19Y/cHpetFGbnEdlvHGMN3s9bIzrunluLkvT7Z//4kLM9sfGeaN+2rrlOO+/5tjXixhBfabN9snGqH3bXlncaKN2WIrcTQrT0Nh/co7fnj4q+aC81RG4e77+zQXceKbASXFjeatefZOi1SUkcMVKHIGCFkKKWQokb6dP4Lc/SahIoOh5IWrtyJoPPpa7zytxp5/8JpHvR4obE5KKQq6swLl55UnOxuIC56No/5wzL5gf6P05vj6V++L2J0S5rRBuHg3Y1C+3nKSDYj6BY3xy4ZLSD+bsMy8UfcgF+5MqcJLmn3tzz4Wec1/4umK+jy4Fjjhncs3pnsT7NOd1Y+S9afdZ4Kqg8/TlWnFg2aomlk9SzRvnkJS5wkb7JHK8uVLH0ubC9VnaWMCwnOHXFNthUXRF0Pbz9aU82zZJFR2Ty6xk2ba/uLBP0PFdIbP9sbkoaZRH9VHgWFzt8az4SUnK45wz/3QKxruBZAFjbln7gkfX6Jwz3yriYwTlLVfgvnTHV0SMaSxwaStv/QscUazEcQxlzYeUsyp45Q2FiFbiSKTcXMwZHlLe6ghcAQ3+RX6ewBXH5LZowiwmTZkXguu/990fnB/Ebd1f//p/p4//9OW7nbisj7jnyAJHExzGSDhonx55o/r82zvv06qSu8/HwOPGIIkjMJ4Kyo4rcL7+8YROz61UHbyQwxtLXLFv69KG18SFNlp1I6htljYWFX7OG6/YUYyuPW2uwLnHonOhGJXzhsdPxU40Fybdgz74PrBCcNBkkrermbTRvchSZkXI3rOuIFE+xXiz96DM4xju8/HtalmRb0UIfx6LNljg3BUxlCvaWOBwdY5xpY42euQVMZ/w2WOXpYzwCZz9+StWAPne4H0UOMpnyWJQ6vi5u5Ln4hM43qhtIiRwlIuxGPbeqydx7qokrwy6Pw9UzvuXT86V9imHnhO8cT94u/zid5TuKRSOVEje7HWSZWMD5S1X4GjV7ew3nOeNTwRuRSOBO+oFrzGnnPSmiMBJseqLU046d9K/40VcSlszgZMSVCYntwvwrdUyUtZ8xCYoLisETg76TJ7AEa4s2ZgduFDifPk5lNvAc4zBKzq0kSjwRGYnELvvyhCvyqEwEe5bh3WgY9aVOOwP95k2N+7KEsdYsGz5p+Yl6eBJzl0LwsRt8VuufB1852wn2x9NoXJ+5PqU465ucl/cPvHrgNfffb3wuLk0Wfl0j8/ybyf/4p70CRxLGsVYtPg88eeBNpYt2jjO9ezx/PkhipxCtliQXKGjjcWNylyJo30UNJYv208bwxU42lxJpOduPufQZqW0LHCYWxzPipbtfyFq1AavuhFczpLGq2shgePntJEoYT6Xo2ClQPIWksEquD/uvlvO5+Hu0yPJG7ZDj+4KXBNxcyGJWwyrcChvuQLHkLDd+PGbF57TY6bAhSVu950OHpTA7b7TQQF5Q5rJG0pZKD5LwgInRS0ETTI8seBExOQJXEieYvDbVrzigeUh3GPJfhfnVtTB8hgsBDyB0cYrRyxw7uYTIJ5caMP2U+lqBQ4FhfNc8XJzaGPJ4mvi5vFqGOf4BM5dMaM8aockzj2O+7k43zV1Bc6N8+vFQhh7m7uKJsKMG9+ftLFY+QSOn/OKGT0v5Kl879PmCpy7cfv8HPNDFDl21Y1FiK+zK0auwLnSxOKHK3AkOG6briASJGQsenxMt9zF9rO8WufLt/ekXVVyV+DouT2n+Aqc7Y8UOLctbpvOz83ndlCwUqB26q7A8Soa9Yfbcst5Bc7d53puHm30aHOtaJDM2biUkFR0Bc4Pixt/Hm5ui2fkCpxf5o56wQnTlbg8gasqr0d45c1HU3krRE2WoTB1R+izbhSnz+Nh3CJFzSLzaMJJESH6QSZJC/Hha683a3Y7pFQH5SnETx79WWmyxPIqsK9IuR/lc4ydNwsBQefPQsCywjKC9VhsqD4LhttWDnht6hATOH7uypZP4LAN/IIDr8bRFhM4goXAPYYrW3xM9xg+gXOvP+XyNebj01ZH4uiaYywH6r8Vm+Le4n1+tMJhhZZzQgLH0udiz83m27bkzwXnYX4I2li86JElzRUrFih31c1dTePnXId/BtzPwhEofnzNii8LSHEjSJZ49Y3bYEnj526bZfkqRI/asXWKz7+50kZ1QgLnYq+ZFTq3nOuiYFXxk0d/KmI5sIjR8VkqWdjoHGICx8/pPDjHFTiGNoylshhW3hiUt1yBe/ulVy88Z4HjlbgGAudbiTvB5MlbVU490uQN3zptLnEyjiLUHaFvoZblDUUtHZpw3IkmxJGHH2te8ZITzbvf+QHzmZv+YfqcH4liIsC6cqKQ1Kkj6+NEyhRtyjI3B2OudKFguG8T8sblvLGA0MaiNAtQvty+0LnwxjEUOHfVzM11z4/zaasSOIq71xOvDe3zxnk+geM4bdRPfr14w/PuE3eje8u9zrSfKnAEbfz2KsPn7ebQZqWm2Oc23eskf36KNnDljDaUqKLdcp6by68hC5OVK/eze+W6LGP8ViRvLF5uHOvSxityLHO02bziLVLaXDks5xaiRpsrb6kCx/u08YoeClbX8OZKGt97VqbjAsd5XM6iS5+B442eo4wsRVDecgXO/QYqylxNgZPyxqSLWUpOl6C8tSFxGJOi1SW+vwNXlEspywEnHiIkUse9/I/NJz5y03TF7cLzLl94fO2r7AeuZ4tfwsrnI8vcHIwpSp+Efu5mh1wBW3yUhaxtULCGCr6FWiDFQ7GgvOUKXIyeBM6N+8r7BqWtLYGTUjUcpJRZYmUpAoeDOZGaF6JOnVzKwpYib4oyJLr/GWkKSlCsbOhI6WoTKUSWlJw+8QucFAulAOVt0AJXSJwrTChvWN4nKGyIlLMUxitwafjkJixaUopkTow6dXLB/hXgRKkoQwDvU3lPDxEUoTEihattpBTFy4aHlAvFgvI2eIErJC4ESlWfoLAtVnGTEtaEYkCWE4udXHDgnjVVEx1Ohu0IXBttLH7wP7lvQqxNPO54Cd/HQwdlaExI0ZoFUpbGgJSNpQrK2ygELi5yKFV9guLWVOBQnIaClLAmuIMyTi52gsGBe9agmFWVtyNxTesvDVC0ugKPO1789/AYQCkaE1KmZoGUo2qwjSZt1UcKx1IE5W3GAiclLcZshQ0ljQjFpZylgvJkYzLeP1LE6rJy+T5GDs5DBqWsXF49QbYjdEXbMq6gcNUF21Xk/TwrUIrGipSheFm7SDmKg/XrttMeUj6WArsuXyPkjdht+7Uitw6ZAufKmS8maV/gWMJSQElz62JcilkqKE5YHsrrByljdZGDMxIWJklObp16ZQFD8ic8WSeVom1ZpqCM1QHbVKrv5z5ACRozKEOxsvaRQuQH6yGY3x9SPpYCKG4uq3beT+TnkilwrrDNQt7apB15qxYzXo2ryusKKWLKYgBf59TXG/OXOnh9moBth8B6Q2f1zNipNnsvIvZaJEiJUZoRETiWNB9S1pBhfN7NB668tSFws5IzFx5sMe6WKYuPnNcacxV5jeqC7bYBHmNWSLHqCylmKaAALQZQhsaIFBClGQGBc2UM5Q3L/Uhx6hr37dEYKG/NBY6RA3Cf8GCLcbdMWbzga64sDvB1ngVSrPqAhcwXi4PyQ6TkDBmUoVSa1G0bKSBKMwICx5KGsVR5cwUO92cNyttiETiCBltfTFn84OuuVIPXcIjXEvs2C6RczRIpYwjKD5aH8oYOClEKdet1gRQQCdZBMH9pAwLHEobSliZvflGLlbVF6uob54aQUpaCHHS7hgdWjLtlipID3keLETznGFi3S/DYY0CKVUFKThkSKozlIOUsBxSlIYNC44K5KXX6RApIGcwPgfWWLp4VuDYFzsb8ZW0SEjgUtCqknKUgB+Su4QES426ZouSC99JiA883BtbtEjz2WJAiZakql6jA5YBCw2BeSp0+kQJSgLkxsO7SZUHgtt56a0VRFEVRFGUEqMApiqIoiqKMDBU4RVEURVGUkaECpyiKoiiKMjJU4BRFURRFUUaGCpyiKIqiKMrIUIGbZ+XKlSKmKIqylNBxUFHGgwrcPHvssYeIKYqiLCV0HFSU8aACN48OXIqiLHV0HFSU8aACN48OXIqiLHV0HFSU8aACN48OXIqiLHV0HFSU8dCKwB122GHms5/9rPnKl79s3vOe95gXvvCFImfoNBm4/vr97zerV68WcbomGFvM/N5++5kTjj9+yvr160V5nzz3Q982L7rTTMGypcA999wzBeOKEqPJOKgoSr80FriPffSj5m8/8xmz7777mu22285seN3rpvur9txT5A6ZJgPXaaeeaj55440ift5554lY1xz0V3eZF/7j/y3IC+1vv+c+Iq9NWNxe/epXL8SOnxc5ui8wvy+GIG/P+Zv+JYp+gXrOIYdMGeMvU8rsaDIOKorSL40E7vrrrxcxhuTl5ptvFvGh0nTgIlFhaX3mrruar919t8jpg2132Ensk9BhXpuQqGGMefGRR4pY29B9tm7tWhFngaPVUfpFA8vb5JJLLjErdtyxFNthzYHTPhx+04/Nbi85SdRpkxNPPNG8+13vEnHmsksvNWeccYaId8HzDj98ev8/9OCD0+dYvli49XOfm55jFR+69lpRd6g0HQcVRemP2gJ39MteNl1pc2O02sPPn7HtttMBDuu5PPbYY9H9Pmlj4Lruuuumj/Q28hVXXCHKu4D6Tat/NFFgGdP1SlRM4PqA5W3bFSvNoR+7fyHunncbr28MV96oH/te8GHz3A9/d/ozQSJHEod12uTII47wrgIz11xzjTn2la8U8S4geSNxo3uSn2NODPrljzcsW0w88MAD09eF92n8o3OnOOb2Rdc/J4qitEdtgaPPux2w//6lGIoCvYWD9VxowHKlrU2BO/roo0UsRhsDF53v17/2tcrzbpMf3H+/WPlB8HVpGxS43V56cml/2xW7iDpdcPinf2qeeeQJU4Gi/a7P28ey5dubXX7/5SJOEofXpU3o4wt0L2CcIZFq4x6vwv3cJ/9SkSNwJDB3Z65eh8aN3DEglzZW4FxJveWWW0R5Hdzzzr0GfdwjiqK0Q22Bo8mC3zJ0PzBOHPB2O4hXiQUNvDRY88DFAzH+Bu6WU1not1ReAaPHCy64QJTHaGvguu2220SsS2Irb0zXIoMCt/vLTxU5fXDAO+yK796vu2j62PV5+1h9ml15pbeu9zn3fdM+TVfgJnKJuW0Te9s+JnddkXJv+vCtvLnjAUHjBm1f/epXRRlDY4A7JmD5EHDHsaOOOmr66I53tNEqHZcRLLhU7sYZd+zrehxct26dufLKK0uxG264obRP50N5WFdRlGbUFrirrrpq+rkbN4YTJn2hAeu5sLDRAEUDGe+7G6/S0UBF4oardkjugMXkDlw+7rj9dnPMMcdMJ5UqeW0LnCSH8Bm4WQgcrbztcuhLpxz07jvNs/7wDHE/ds3yZ66aXuu1b7zaPGOX3acx6sOq175V5HaB+3YcEnt7tSv43vR9kYNeI4whtLmPBP/su6t0sfGAqDsmVNHGChzjrr7xc3ejsY/iNA7SudN+7PWmc65z3rnj4L333jvt34YNG6b7JG+0scRRnDbKw7qKojSjtsDRpI1fUsDPwOFn5BB34HUFDt9CoYGLByvK862+NSV34HKhz10dduMjC32kz7+det2d5rDrHzSrX3+ZyG8TFLi1Z/1lSeJoFejg9/2zqNcmQxC49ecXk+TK57/S7H/pTb0L3I77HTr93Jsbo3sA87qCvqiAMSb2BYeu4Htz50NePIXjuB+Cv8VNG8f4Zz9H4IYO/2LK+6G3UunceSxkmcOcpjQZBxVF6ZfaAkfQW6i+SYPkjVahqv6UCA687oocb+5vo/RIg9aQBi53giaJ4xhN5vR8xQHPE3XaJOUzcF0za4Gjb5j6Xj8WOCqLrVa0Qew1oLI+3sKMfUmhajW8C9xfLkjY6G1kokre+K1D9xc13njfFTjKc8v6oq0VONrc+xNX4NxfbPmadPFLLOH7OVIUZZg0EjiCPltCEyjJGonbUvs7cO63C/k5PfYlcPTWGP0JC96nD7OfffbZIq9LZi1wb7vooukfrcWJkwWOyrr+VrDvz4gw9EsOrlZ3Af1BbYwx9C1VjHUNvm2buvKmzI6646CiKP3TWOAImsDpt9Hv33ff9LNxxx13nMgZOnUHLvq8E/2hXBI1es4xetuSYimf9WkC/QkNkgOWFnoNqn7jbxt8vfHbln19CxXp8y1Ueg1olQ0lkqCyPv6gMQkkfjOcoL+DV/f+VpYWep8oynhoReAWAzpwKYqy1NFxUFHGgwrcPDpwKYqy1NFxUFHGw0TgdlSB21oHLkVRFB0HFWU8qMDNowOXoihLHR0HFWU8qMDNowOXoihLHR0HFWU8qMDNs3Kl/f8zFUVRlio6DirKeJjbYlsVOEVRFEVRlDHx/7lm+BvKsAYvAAAAAElFTkSuQmCC>

[image7]: <data:image/png;base64,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>

[image8]: <data:image/png;base64,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>