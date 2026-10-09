# Cisco Unicon — Record, Replay, and Create Mock Sessions

Captures device interactions and reuses them through playback or a mock CLI process.

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [Playback](https://pubhub.devnetcloud.com/media/unicon/docs/playback/index.html).

## Common Sequences

- **Recorded replay:** Record session → Replay session.
- **Mock CLI:** Record session → Convert recording → Start mock session.

## Record session

Cisco's recording mechanism captures the job's interactions into one pickle file per device:

```bash
pyats run job job.py --testbed-file testbed.yaml --record recording
```

**Before a job or its connection initialization changes a live device: stop and confirm with the
operator before proceeding.** Recording observes execution; it is not a dry run. Success is the
recording directory and per-device recorded data. A job failure is still a failure even if its
interactions were recorded. The source documents a parallel-call limitation: only one device
connection can be recorded with Pcall.

## Replay session

```bash
pyats run job job.py --testbed-file testbed.yaml --replay recording
```

Replay uses the recorded session instead of connecting to the device. `--speed 4` is Cisco's
example for faster playback; it changes replay timing, not recorded network behavior.
Success is reproduction of the recorded interaction sequence. Missing/incompatible recording
content leaves playback unverified; the source supplies no universal error-string contract or
repair algorithm, so the actual error and missing interaction must be reported.

## Convert recording

**Before replacing existing mock output: stop and confirm with the operator before proceeding.**

```bash
python -m unicon.playback.mock --recorded-data recording/dut --output mock_data/device.yaml
```

Success is a generated mock YAML file. By default only the first response for a repeated command
is retained. `--allow-repeated-commands` keeps the response sequence with circular playback.
A conversion error leaves the mock incomplete; no guessed command responses are substituted.

## Start mock session

```bash
python -m unicon.mock.mock_device --os iosxe --mock_data_dir mock_data --state connect
```

The OS and initial state must match the recorded data; `iosxe` is only this example's selection.
Success is an interactive mock prompt that returns the captured command responses. The alternate
Unicon testbed binding is in [endpoints.md](endpoints.md). Missing command/state data is a mock
coverage gap; the cited source does not establish a universal error message for it.

## Patterns

- Record/replay reproduces session interactions. Converting to mock YAML exposes a command/response
  model suitable for a new interactive mock session.
- A circular response list repeats; it is not a live measurement of changing device state.

## Known limitations

- Recorded interactions may contain operational or identifying data. Repository examples use
  placeholders; real recordings are not contribution artifacts for this repository.
- Unicon records pickle files; only recordings from the intended trusted workflow belong in playback.
- The documented Pcall recording limitation prevents claiming complete multi-device recordings.
- Replay cannot validate current hardware or protocol convergence.

## Tools

| Mechanism | Description | Category |
|---|---|---|
| Local pyATS / Unicon CLI | Record, replay, convert, and launch mock sessions | Shell |
| Unicon session transport | Connect to the local mock CLI process | Local process |

## Verification checklist

- [ ] Recording contains the intended device's interactions.
- [ ] Playback uses the requested recording and preserves the test outcome.
- [ ] Mock initial state and OS match its data.
- [ ] Repeated-response behavior matches the chosen conversion option.
