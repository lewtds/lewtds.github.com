---
layout: post
title: OOP-free JavaScript
description: In which I show you how to be a hispter.
---

Anyone who has worked with JavaScript on any non-trivial code base knows the
pain of navigating it, due mostly to the dynamic method dispatch mechanism:

```js
// person.js
function Person() {}
Person.prototype.kiss = function(otherPerson) {}
```

```js
// anotherfilefarfaraway.js
doThing(new Person());
```

```js
// main.js
/**
 * Do something.
 * @param  {Person} person
 */
function doThing(person) {
    person.kiss(otherPerson);
}
```

Now try to find the source code for the `kiss()` method. You'll first have to
figure out which class the `person` object belongs to, something not always
straightforward given the dynamicity of the language. It's easy in this case
because of the docstring, which few people write. Otherwise, you'll have to
trace the execution of the function all the way up to find out who passed that
person instance into `doThing()`. Grep-ing does get old...

On the other hand, we can try replacing methods with unbound functions and thus
reducing objects to being just bags of state:

```js
// person.js
export function kiss(person, otherPerson) {}
```

```js
// main.js
import * as Person from "./person";

function doThing(person) {
	Person.kiss(person, otherPerson);
}
```

It's now easy to infer immediately where that `kiss()` function comes from. In
other words, our code has become *statically analyzable*. Your favorite IDE
loves this!

Making this change means that we lose some of the goodness from the OOP world
though, the most obvious one is polymorphism and the most common way to achieve
it, inheritance. But fear not, we can always implement our own dispatching
strategy.

```js
function kiss(person) {
	switch (person.race) {
	case "white":
		kiss_white(person); break;
	case "black":
		kiss_black(person); break;
	}
}

function kiss_black() { console.log("I kiss like a black person"); }
function kiss_white() { console.log("I kiss like a white person"); }

kiss({race: "black"});
kiss({race: "white"});
```

It works but is a bit manual and not open for extension. Let's try throwing in
some metaprogramming magic. How about this?

```js
function kiss(person) {
	eval("kiss_" + person.race).call(null, person);
}

// ...
```

Great, but now we have a code injection issue to worry about. Sure we can do
better? Yup. Higher order functions to the rescue!

```js
function defPolyFunc(name, getKey) {
	function dispatcher() {
		var key = getKey.apply(null, arguments);
		var implementation = dispatcher.implementations[key];
		if (implementation) {
			implementation.apply(null, arguments);
		} else {
			throw {
				name: "NoImplementation"
				message: "No implementation of " + name + " for key " + key,
			};
		}
	}

	dispatcher.implementations = {};
	return dispatcher;
}

function addPolyFuncImpl(polyFunction, key, implementation) {
	polyFunction.implementations[key] = implementation;
	return implementation;
}
```

The idea is that each function defined by `defPolyFunc()` will have an attached
list of implementations, dispatched at runtime through a key, obtained from the
argument list by a custom `getKey()` function. Users of the polymorphic function
can then extend it by calling `addPolyFuncImpl()` with their own implementation
for a specific key. This is cooler than inheritance-based dispatching because we
can dispatch on the whole argument list, not just the type of the first
argument, i.e. the `this` parameter.


> This idea is very similar to Clojure's [multimethod feature](http://clojure.org/about/runtime_polymorphism).
> And a quick look through [their source code](https://github.com/clojure/clojure/blob/bdc752a7fefff5e63e0847836ae5e6d95f971c37/src/clj/clojure/core.clj#L1612)
> shows that they did something not far removed from this.


```js
// ---------- Usage ----------

var kiss = defPolyFunc("kiss", function getKey(person) {
	return person.race;
});


addPolyFuncImpl(kiss, "black", function kiss_black(person) {
	console.log("I kiss like a black person");
});


addPolyFuncImpl(kiss, "asian", function kiss_asian(person) {
	console.log("I kiss like an asian person");
});

kiss({race: "black", name: "etc..."});
kiss({race: "asian", name: "lol..."});
```

Take that idea one step further and apply it not to one function but a bunch at
a time, we'll end up with type class (what it is called in Haskell, also refered
to as protocol in Elixir and Clojure), which should not be confused with classes
in OOP. **@Gozala** on GitHub came up with a [pretty nice implemention](https://github.com/Gozala/protocol)
already.

Note that the ideas presented here should be applicable to other dynamically-
typed OOP languages like Python and Ruby as well. In the case of Python,
decorators allow us to build a very elegant implementation of `defPolyFunc()`.
But that is left as an excercise for the reader ; )
