# reproducibility

## Overview

Framework and reusable tools for reproducibility testing of models

## Usage

As with most reusable workflows, you call it across repositories with the following syntax:

```yaml
# ...
call-reusable:
  name: Call Reusable Workflow
  uses: access-nri/reproducibility/.github/workflows/<workflow_name>@<commit>
  with:
    param: Test
```

## Reusable Workflows

### Reproducibility CI Checks: `checks.yml`

This workflow is a generic reusable workflow that runs reproducibility checks in a given deployment environment.

> [!NOTE]
> The `vars` and `secrets` within this workflow are inherited from the caller, NOT defined in this repository. Therefore, they must be defined by any workflow that calls this workflow, and any call to this workflow must have `secrets: inherit`.

#### Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `model-name` | `string` | The name of the model to check for reproducibility | `true` | N/A | `"access-om2"` |
| `config-tag` | `string` | A tag on an associated config branch to use for the reproducibility run | `true` | N/A | `"release-1deg_jra55_iaf-1.2"` |
| `environment-name` | `string` | The name of a GitHub Deployment Environment that is inherited from the caller | `true` | N/A | `"Gadi"` |
| `test-markers` | `string` (python-style expression) | A python expression of markers to pass to the reproducibility pytests `-m` flag. These pytests are defined in the caller | `true` | N/A | `"checksums and fast and not performance"` |

#### Outputs

| Name | Type | Description |
| ---- | ---- | ----------- |
| `artifact-name` | `string` | Name of the artifact containing the checksums and test report for this repro run |
| `experiment-location` | `string` | Location of the experiment on the target environment |

#### Usage

```yml
jobs:
  repro-ci:
    # run the given config and upload the checksums
    # NOTE: we use `main` to refer to the most recent version of the workflow, to aid
    # in the updating of our internal workflows.
    uses: access-nri/reproducibility/.github/workflows/checks.yml@main
    with:
      model-name: access-om2
      environment-name: Gadi
      config-tag: ${{ github.ref_name }}
      test-markers: checksum
    secrets: inherit
```
