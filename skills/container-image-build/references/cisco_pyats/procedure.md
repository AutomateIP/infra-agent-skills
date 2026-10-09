# Cisco pyATS Image Builder — Build a Local Test Image

Builds a Docker image from a pyATS Image Builder definition and inspects its identity.

Source: [CiscoTestAutomation pyats-image-builder README](https://github.com/CiscoTestAutomation/pyats-image-builder),
rolling repository documentation retrieved 2026-10-09.

## 1. Define the build

Cisco lists a Linux environment with Docker Engine and the builder installed as prerequisites.
The definition selects an image tag, compatible Python version, package requirements, and local
files or repositories. Success is a definition containing the intended test code and dependencies.
The exact schema is in [endpoints.md](endpoints.md). The README's historical Python default is not
a promise of compatibility with a current pyATS release; the actual supported version remains a
required input from the selected package release.

## 2. Prepare the context without building

**Before overwriting files in an explicitly reused context directory: stop and confirm with the
operator before proceeding.** This procedure otherwise uses the builder's temporary context.

```bash
pyats image build build.yaml --dry-run --keep-context
```

The documented dry-run assembles the context without invoking Docker build. Success is the
prepared context, which contains the generated Dockerfile and dependency/file inputs.
Missing files, unresolved dependencies, or inaccessible repositories prevent completing this step;
the README does not enumerate fixed error codes, so the actual builder error remains evidence.

## 3. Build the image

**Before replacing an existing local image tag or running custom build commands: stop and confirm
with the operator before proceeding.** The builder invokes Docker with the prepared definition:

```bash
pyats image build build.yaml
```

Success is the resulting image identity/tag. A failed Docker build does not produce a verified
image. The standalone `pyats-image-build` entry point provides the same builder when pyATS itself
is not installed; the installed CLI help determines the available command form.

## 4. Inspect the image

The builder's image inspection returns Docker metadata, including its image ID and tags.
The Python binding is in [endpoints.md](endpoints.md). Completion means the built image has the
requested identity and dependency/code inputs; passing network tests requires a separate job run.

## Patterns

- Job discovery can use explicit job paths or matching patterns in the build definition.
- A dry-run produces build context, not a runnable image.

## Known limitations

- The builder preserves build metadata under `/pyats/installation`, including the build definition.
  Values stored in image environment variables persist in the image. Secrets must not be supplied
  as image environment settings or copied files; those are not runtime-only secret channels.
- Cisco allows raw build commands and warns about their effects. This procedure does not add any.
- SCP inputs require noninteractive authentication and absolute paths; FTP only fetches individual
  anonymously accessible files. This procedure's example uses local files instead.
- Publishing and running the image are outside the local-build outcome.

## Tools

| Operation | Description | Category |
|---|---|---|
| Local builder CLI | Assemble context and invoke Docker build | Shell |
| Inspect image | Return metadata for the built image | Local Python |

## Verification checklist

- [ ] Python and dependency versions were selected explicitly for the requested build.
- [ ] Dry-run context represents the intended non-secret inputs.
- [ ] Docker build succeeded and the image ID/tag were inspected.
- [ ] Image creation was not reported as a passing network test.
