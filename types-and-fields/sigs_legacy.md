# Sigs

_Sigs_ are the basic building block of any model in Forge. You can think of a `sig` as analogous to a class in an object-oriented programming language. You can declare a `sig` in the following way:

```
sig <name> {
    <field>,
    <field> ...
}
```

## Fields

Each _field_ in a `sig` has:

* a name;
* a _multiplicity_ (`set`, `one`, `lone`, `pfunc`, or `func`);
* a type.

E.g. a model of a circularly-linked list might have a `sig` called `Node`. `Node` might then have a field `next: one Node` to represent the contents of every `Node`'s `next` reference. We use `one` here since every `Node` has exactly one successor.

### Field Multiplicity

Given a field `f` with type `A`, the set of possible multiplicities is:

* `set`: a set of values (this field may contain any number of objects);
* `one`: a singleton value (this field always contains a single object); and
* `lone`: either a singleton value or no value (this field contains 0 or 1 object).

Given a higher-arity field `f` with type `A -> ... -> Y -> Z`, multiplicities constrain the nature of the _tuples_ in the field. The set of possible multiplicities is:

* `set`: a set of tuples (this field may contain any number of tuples);
* `func`: the field is a total function from `A -> ... -> Y` to `Z` (every possible input tuple must have exactly one output value); and
* `pfunc`: the field is a partial function from `A -> ... -> Y` to `Z` (every possible input tuple has either one output value or is not mapped by the function).

Fields declared as `pfunc` are analogous to maps or dictionaries in an object-oriented programming language: some keys may not map to values, but if a key is mapped it is mapped to exactly one value.

### Low-Level Details

Forge's engine translates each field `f` to a relation with arity $1+arity(f)$. E.g.,

```
sig A {
    myField: set A -> B
}
```

is internally represented as a relation named `myField` of type `A -> A -> B`.

## Inheritance

Sigs may inherit from other sigs via the `extends` keyword:

```
sig Cat {
    favoriteFood: one Food
}
sig ActorCat extends Cat {}
sig ProgrammerCat extends Cat {}
```

This means that any `ProgrammerCat` object is also a `Cat` object, and so will have a `favoriteFood` field.

### Warning: Sigs are disjoint by default!

Any two sigs `A` and `B` will never contain an object in common unless one is a descendent of the other. So in this example, no `Cat` can ever be both a `ProgrammerCat` and `ActorCat`.

## Singleton and Maybe Sigs

You can tell Forge that a given `sig` is always a singleton (i.e., only ever instantiated exactly once) or singleton-if-populated by using the `one` and `lone` keywords in the definition:

```
one sig SingletonObject {}
lone sig MaybeDoesntExist {}
```
