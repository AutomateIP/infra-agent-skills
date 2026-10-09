# Cisco pyATS — Run Parallel Calls

Executes an already-defined callable over supplied independent argument sets and collects results.

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [Parallel Call](https://pubhub.devnetcloud.com/media/pyats/docs/async/pcall.html).

## 1. Prepare target and argument sets

The parallel-call API receives one callable or a list of callables, plus shared and per-call
arguments. Per-call argument iterables are zipped; extra elements beyond the shortest iterable
are discarded. The documented binding is in [endpoints.md](endpoints.md). Success means each
intended invocation has a matching argument set, and its return value can be pickled.

## 2. Execute the calls

The API forks workers, waits for completion, and collects their return values.
**For callables that configure, clear, reload, delete, or otherwise change live systems: stop and
confirm with the operator before proceeding, covering every resolved target and payload.**
This gate is repository policy; the parallel-call API does not supply a device-change approval.
Success returns a tuple in invocation order, regardless of completion order.

- A child exception is re-raised in the parent as `ChildProcessException`; its cause carries the
  original failure and traceback. A successful sibling does not make the whole call successful.
- A child exceeding the requested timeout causes `SIGTERM` to be sent to the workers and raises
  `TimeoutError`. Completion of external device changes is unknown after this interruption;
  there is no documented transactional rollback or safe blanket retry.
- A non-pickleable result violates the documented API contract; the callable must return suitable
  data before the sequence can complete.

## 3. Collect the outcome

Success is the ordered tuple of worker return values. With the explicit object lifecycle,
starting is followed by joining before reading results; before start/join the results are `None`.
The operation's own return values remain authoritative; a returned value is not automatically a
network pass result. Failed calls retain the documented exception and cause in the report.

## Patterns

- Argument pairing is positional, not a Cartesian product.
- The object form supports observing workers, while the convenience form waits for them.

## Known limitations

- A worker must return pickleable data. Live sessions are not a portable return contract.
- Timeout termination cannot undo an action already accepted by an external system.
- This does not supply a concurrency limit, device ordering policy, or recovery procedure.

## Tools

| Operation | Description | Category |
|---|---|---|
| Run parallel calls | Invoke targets and return ordered values | Local Python |
| Join workers | Complete the explicit worker lifecycle | Local Python |

## Verification checklist

- [ ] Argument lengths cover every intended invocation.
- [ ] Returned values are associated with input order.
- [ ] Child failures and timeout exceptions remain visible.
- [ ] Explicit worker objects were joined before results were consumed.
