# Cisco pyATS — Parallel Call Bindings

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [Parallel Call](https://pubhub.devnetcloud.com/media/pyats/docs/async/pcall.html).

## Run parallel calls

Original local-only example:

```python
from pyats.async_ import pcall

def multiply(value, factor):
    return value * factor

results = pcall(multiply, value=(2, 4), factor=(3, 5))
assert results == (6, 20)
```

| Argument | Binding |
|---|---|
| `targets` | One callable or ordered callables |
| `cargs`, `ckwargs` | Shared positional/keyword arguments |
| `iargs`, `ikwargs` | Per-invocation positional/keyword arguments |
| Other keyword iterables | Zipped values supplied as that keyword |
| `timeout` | Maximum duration of an individual worker, in seconds |

## Join workers

```python
from pyats.async_ import Pcall
workers = Pcall(multiply, value=(2, 4), factor=(3, 5))
workers.start()
workers.join()
results = workers.results
```

## Errors

`ChildProcessException` wraps a failed child, with the original error as its cause.
`TimeoutError` reports timeout termination. Neither error promises rollback of side effects.
