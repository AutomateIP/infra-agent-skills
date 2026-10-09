# Cisco pyATS — Run the Published Docker Image

Starts Cisco's published pyATS image and runs an existing test job inside it.

Sources: Cisco's [pyats-docker README](https://github.com/CiscoTestAutomation/pyats-docker)
and [pyats-image-builder README, Running Built Images](https://github.com/CiscoTestAutomation/pyats-image-builder),
rolling repository documentation retrieved 2026-10-09.

## 1. Select and pull the image

The published image is `ciscotestautomation/pyats`; Cisco permits a selected version tag in place
of its example's `latest`. With the requested tag substituted:

```bash
docker pull ciscotestautomation/pyats:<selected-tag>
```

Success is a completed pull of that image. A nonexistent tag or registry/network error is not a
successful deployment. Published tag availability and compatibility require the selected release;
this procedure does not infer them from the README's old terminal transcript.

## 2. Start the container

Cisco documents a shell alternative to the image's default Python session:

```bash
docker run -it ciscotestautomation/pyats:<selected-tag> /bin/bash
```

**Before startup hooks or mounted initialization code affect a live environment: stop and confirm
with the operator before proceeding.** Success is an activated pyATS workspace and shell in the
container. The documented workspace is `/pyats`. Startup failures remain container failures, not
failed network assertions.

A caller-supplied local job directory can be mounted with Docker's documented
`-v /absolute/local/path:/absolute/container/path` form. This procedure uses that binding to make
the approved job and testbed available inside the container; it does not copy secrets into an image.

## 3. Run the requested job

**Before any job, connection initialization, setup, or cleanup changes a device: stop and confirm
with the operator before proceeding.** From the mounted job directory inside the container:

```bash
pyats run job job.py --testbed-file testbed.yaml
```

Success is the intended job's actual results and logs. A launched container alone is insufficient.
Connection failures require checking the selected container-to-device path; the cited image docs
do not prescribe a universal Docker networking mode, resolver, VPN, or retry policy.

## 4. Finish the session

Cisco documents `Ctrl-D` to exit the interactive container. Completion includes the job's reported
result and its retained artifacts. This sequence does not request automatic container removal;
removing a container and its writable data is a separate destructive operation.

## Patterns

- Mounting `requirements.txt` at the documented location invokes package installation at startup.
- Mounting `workspace.init` invokes that script at creation; it is executable input, not passive data.

## Known limitations

- This covers running a published pyATS image, not building it, scaling it, or selecting a network mode.
- The README's historical Python/package versions are illustrative, not current compatibility data.
- Cisco documents workspace persistence through a Docker volume. That does not imply every arbitrary
  output path or container writable layer survives removal.

## Tools

| Mechanism | Description | Category |
|---|---|---|
| Docker CLI | Pull the image, mount inputs, and start a container shell | Local container runtime |
| Container shell | Execute the approved pyATS job and inspect results | Test execution |

## Verification checklist

- [ ] The requested image/tag was pulled successfully.
- [ ] The container has the intended job/testbed inputs and runtime credential access.
- [ ] The requested job ran and its actual results/logs were inspected.
- [ ] Result retention was accounted for before exiting.
