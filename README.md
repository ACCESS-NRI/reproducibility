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

### Initial Checksum Generation: `generate-initial-checksums.yml`

This workflow is used to create the initial checksums for a given config branch, optionally committing them to the repository that calls this workflow.

> [!NOTE]
> The `vars` and `secrets` within this workflow are inherited from the caller, NOT defined in this repository. Therefore, they must be defined by any workflow that calls this workflow, and any call to this workflow must have `secrets: inherit`.

> [!NOTE]
> If you `commit-checksums-to-target-branch`, this workflow requires `permissions.contents: write`

#### Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `model-name` | `string` | Name of the model that is having it's checksums generated | `true` | N/A | `"access-om2"` |
| `config-branch-name` | `string` | The configuration branch from the repository that will be run to generate the checksums | `true` | N/A | `"release-1deg_jra55_iaf"` |
| `commit-checksums-to-target-branch` | `boolean` | Whether to commit the checksums to the target branch once generated | `true` | N/A | `true` |
| `target-branch-name` | `string` |  Which branch to commit the generated checksums | Only if `commit-checksums-to-target-branch` is `true` | N/A | `"dev-1deg_jra55_iaf"` |
| `target-branch-checksum-location` | `string` | Where in the repository the generated checksums should be committed to | Only if `commit-checksums-to-target-branch` is `true` | `"./testing/checksums"` | `"./custom/checksum/location"` |
| `target-branch-checksum-tag` | `string` | An optional tag to attach to the committed checksums | Only if `commit-checksums-to-target-branch` is `true` | `""` | `release-1deg_jra55_iaf-1.0` |
| `environment-name` | `string` | The name of a GitHub Environment that is inherited from the caller | `true` | N/A | `"Gadi Initial Checksum"` |

#### Outputs

| Name | Type | Description |
| ---- | ---- | ----------- |
| `artifact-name` | `string` | Name of the artifact containing the checksums |
| `experiment-location` | `string` | Location of the experiment on the target environment |

#### Usage

```yml
jobs:
  gen-checksums-no-commit:
    # Despite not committing to a branch, the checksums are still accessible as an artifact or on the deployment environment (see outputs section above)
    if: inputs.commit == 'false'
    uses: access-nri/reproducibility/.github/workflows/generate-initial-checksums.yml@main
    with:
      model-name: access-om2
      config-branch-name: release-1deg_jra55_iaf
      commit-checksums-to-target-branch: false
      environment-name: "Gadi Initial Checksum"
    secrets: inherit

  gen-checksums-with-commit:
    if: inputs.commit == 'true'
    uses: access-nri/reproducibility/.github/workflows/generate-initial-checksums.yml@main
    with:
      model-name: access-om2
      config-branch-name: release-1deg_jra55_iaf
      commit-checksums-to-target-branch: true
      target-branch-name: dev-1deg_jra55_iaf
      target-branch-checksum-location: ./checksums
      target-branch-checksum-tag: release-1deg_jra55_iaf-1.0
      environment-name: "Gadi Initial Checksum"
    permissions:
      contents: write
    secrets: inherit
```
