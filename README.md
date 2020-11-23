# JSONPath axiomatic semantics

** WORK IN PROGRESS **

## sequences of JSON values

A JSONPath is modelled as mapping one sequence of JSON values to another sequence of JSON values.
The `<>` notation is used for sequences, to try to distinguish these from JSON arrays.

Empty sequence: `<>`.

Concatenation: `<a, b> = <a> ^ <b>`.

So, for example, the sequence `<a, b, c>` is equivalent to the concatenation of three single item
sequences `<a> ^ <b> ^ <c>`.

## sequence semantics
A selector `s` takes a sequence of values and applies to each one separately.
The results are then concatenated.

```
<> s = <>
(x^y)s = (x s)^(y s)
```

## JSON object construction

If `m` and `n` are JSON objects with no keys in common, then `m ∪ n` is the JSON object containing
all the mappings of `m` and `n` (but no others).

## compound path semantics

If JSONPath `p` is selector `s` followed by JSONPath `t`, then for all JSON values `v`:
```
<v>p = (<v>s)t
```

## root selector semantics
`$` is effectively a no-op. If `t` is a JSONPath (without a leading `$`), then:
```
<v>$t = <v>t
```
Question: do we need to use something like quasi quotes to clarify the above?

## dot child
If `o` is a JSON object which does not have key `k`, then:
```
<o>.k = <>
```
and:
```
<o ∪ {k:v}>.k = <v>
```

## union
```
<a>[u, v] = (<a>[u]) ^ (<a>[v])
```

## bracket child
If `o` is a JSON object which does not have key `k` (for certain forms of k...), then:
```
<o>[k] = <>
```
and:
```
<o ∪ {k:v}>[k] = <v>
```

## array slice
If `a` is an array, then:
```
... details! ...
```

If `a` is not an array and `sl` is a slice expression, then:
```
<a>[sl] = <>
```

## recursive descent
If `z` is a scalar:
```
<z>.. = <z>
```
If `o` is an empty object:
```
<o>.. = <>
```

If `o` is a JSON object which does not have key `k`, then:
If `o` is an object comprised of `k:v` and an object p:
```
<o ∪ {k:v}>.. = (<v>)^(<v>..)^(<o>..)
```
Note: since an object can be deconstructed to the form `o ∪ {k:v}` for arbitrary key `k` in
the original object, the ordering of the result is only partially defined.

If `a` is an empty array:
```
<a>.. = <>
```
If `h` is a JSON value and `t` is a JSON array:
```
(<h>^t).. = (<h>)^(<h>..)^(t..)
```