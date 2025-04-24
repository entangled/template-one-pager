Untitled one-pager
==================

This one pager template is configured to render nicely as a Github README. This is nice to use if you have some demo in a README and want to check that the examples actually run correctly.

For instance, let us render the famous [Rule 30 program](https://en.wikipedia.org/wiki/Rule_30).

```python
#| id: rule-30
def rule_30(a: bool, b: bool, c: bool) -> bool:
    return (30 & (1 << (a*4 + b*2 + c)) != 0)
```

To compute this cellular automaton, we need to scan a list of bits for patterns matching the rule. We'll iterate triples, padding with a given constant on both sides to conserve the with of the sequence.

```python
#| id: triples
from typing import Generator

def triples[T](lst: list[T], pad: T) -> Generator[tuple[T, T, T]]:
    yield (pad, *lst[0:2])
    for i in range(len(lst) - 2):
        yield tuple(lst[i:i+3])
    yield (*lst[-2:], pad)
```

The `run_ca` function runs the cellular automaton for `n` generations given some initial state.

```python
#| id: run-ca
from itertools import starmap

def run_ca(rule, state, n):
    yield state
    for i in range(n-1):
        state = list(starmap(rule, triples(state, False)))
        yield state
```

We can render this into an image by writing a PBM file.

```python
#| id: write-pbm
def write_pbm(lines: list[list[bool]]):
    print("P1")
    print(len(lines[0]), len(lines))
    for line in lines:
        print(" ".join(str(int(b)) for b in line))
```

We combine everything into a script.

```python
#| classes: ["task"]
#| description: Render 256 lines of Rule 30
#| stdout: rule30.pbm
#| collect: figures

<<rule-30>>
<<triples>>
<<run-ca>>
<<write-pbm>>

state = [False] * 512
state[256] = True
result = list(run_ca(rule_30, state, 256))
write_pbm(result)
```

You can run this script using:

```bash
brei figures
```

![Rule 30](./rule30.pbm)

