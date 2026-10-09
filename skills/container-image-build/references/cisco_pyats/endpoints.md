# Cisco pyATS Image Builder — Definition and Inspection

Source: [CiscoTestAutomation pyats-image-builder README](https://github.com/CiscoTestAutomation/pyats-image-builder),
rolling repository documentation retrieved 2026-10-09.

## Build definition

Template: replace the two version placeholders with an explicitly compatible pair before building.

```yaml
tag: example-pyats:test
python: "<supported-python-version>"
packages:
  - "pyats[full]==<selected-pyats-version>"
files:
  - /absolute/path/to/job.py
  - /absolute/path/to/test.py
jobfiles:
  paths:
    - /pyats/job.py
```

`files` copies inputs into the workspace. `repositories` optionally defines a URL and `commit_id`
for source code; `env` embeds environment values in the image and is not a secret mechanism.

## Inspect image

The `Image` wrapper accepts the resulting image ID; inspection returns its metadata.

```python
from pyatsimagebuilder import Image
metadata = Image(image_id=built_image_id).inspect()
```

The input `built_image_id` is the actual builder result, not an invented identifier.
The metadata's `Id` and `RepoTags` describe the image. Registry push is deliberately excluded.
