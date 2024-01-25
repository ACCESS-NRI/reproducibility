# Check Repro Action

This action checks a downloaded checksum against a 'ground truth' checksum in a config repository.

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| checksum-name | string | The name of the GitHub artifact containing the checksum that will be compared against | true | N/A | `access-om2-release-1deg_jra55_iaf-3.1` |
| checksum-location | string | Location of the GitHub artifact containing the checksum that will be compared against | true | N/A | `/opt/checksums` |

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| result | boolean | The result of the comparison between the artifact and the latest ground truth | `true` |
| ground-truth-version | string | The ground truth version compared against the input checksum | `release-1deg_jra55_iaf-2.1` |

## Example

```yaml
# ...
- uses: actions/checkout@v3
  with:
    ref: my-access-om2-configs

- id: check
  uses: access-nri/reproducibility/.github/actions/checksum-check@main
  with:
    checksum-name: access-om2-2.1
    checksum-location: /tmp/checksums

- run: echo "Result of comparison was ${{ steps.check.outputs.result }} when comparing against ${{ steps.check.outputs.ground-truth-version }}.

```
