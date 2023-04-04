# Homework 6 – Object and Classes

This is a group assignment. You need to provide full test coverage for the
submitted work. This includes explicit tests on all functions written.
Start with class-inherit.rkt, which combines class.rkt, inherit.rkt,
and inherit-parse.rkt into a single file.
You may find it nicer to work with class.rkt, inherit.rkt, and inherit-
parse.rkt as separate files, but you must combine them into a single file. To
combine the files: append them in the listed order, then remove the second and
third #lang plait lines and remove all require forms.


## Part 1 — Instantiating Object
In the starting code, {new Object} doesn’t work, even though Object is
supposed to be a built-in class with no fields and no methods. Fix the
implementation to that {new Object} produces an instance of Object.
``` (test (interp-prog (list)
`{new Object})
`object)
(test (interp-prog (list `{class Fish extends Object
{size color}})
`{new Object})
`object)
```

## Part 2 — Conditional via select
Add select:
<Exp> = ...
| {select <Exp> <Exp>}
The first expression in select should produce a number result, while the
second expression should produce an object. If the number is 0, select calls
the zero method of the object. If the number is not 0, select calls
the nonzero method of the object. In each case, select calls the method with
the argument 0.
```
(test (interp-prog (list `{class Snowball extends Object
{size}
[zero {arg} this]
[nonzero {arg}
{new Snowball {+ 1 {get this size}}}]})
`{get {select 0 {new Snowball 1}} size})
`1)
(test (interp-prog (list `{class Snowball extends Object
{size}
[zero {arg} this]
[nonzero {arg}
{new Snowball {+ 1 {get this size}}}]})
`{get {select {+ 1 2} {new Snowball 1}} size})
`2) 
```

The result is up to you when select gets a non-number result for its first
subexpression, but a “not a number” error is sensible. Similarly, the result
is up to you when the second subexpression’s result is not an object or does
not have a zero or nonzero method.
You will probably find it easiest to extend and test the Exp layer, then
then ExpI layer, and then the parse and interp-prog layer.



## Part 3 — instanceof
Add instanceof:
<Exp> = ...
| {instanceof <Exp> <Sym>}
An expression {instanceof <Exp> <Sym>} produces 0 if the result of <Exp> is an
object that is an instance of the class named by <Sym> or any of its
subclasses. It should produce 1 f the result of <Exp> is any other object.
Note that {instanceof <Exp> Object} should produce 0 if the value of <Exp> is
an object.
Note that you will have to introduce a notion of superclasses into
the Class layer, even though method inheritance remains the job of
the ClassI layer.

Example:
```
(test (interp-prog (list `{class Fish extends Object
{size color}})
`{instanceof {new Fish 1 2} Object})
`0)
(test (interp-prog (list `{class Fish extends Object
{size color}})
`{instanceof {new Object} Fish})
`1)
(test (interp-prog (list `{class Fish extends Object
{size color}})
`{instanceof {new Fish 1 2} Fish})
`0)
(test (interp-prog (list `{class Fish extends Object
{size color}}
`{class Shark extends Fish
{teeth}})
`{instanceof {new Shark 1 2 3} Fish})
`0)
(test (interp-prog (list `{class Fish extends Object
{size color}}
`{class Shark extends Fish
{teeth}}
`{class Hammerhead extends Shark
{}})
`{instanceof {new Hammerhead 1 2 3} Fish})
`0)
(test (interp-proc (list `{class PlainFish extends Object
{size}}
`{class ColorFish extends PlainFish
{color}}
`{class Bear extends Object
{size color}
[rate-food {arg}
{+ {instanceof arg ColorFish}
{get arg color}}]})
`{send {new Bear 100 5} rate-food {new ColorFish 10 3}})
`3)
```

The result is up to you if <Exp> in {instanceof <Exp> <Sym>} does not produce
an object or if <Sym> is not the name of a class.
