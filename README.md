# reproducibility

## Overview

Framework and reusable tools for reproducibility testing of models

## Usage

Specific usage instructions are within the `.github/actions` directory.

As with most custom actions and reusable workflows, you call it across repositories with the following syntax:

```yaml
# ...
call-reusable:
  name: Call Reusable Workflow
  uses: access-nri/reproducibility/.github/workflows/<workflow_name>@<commit>
  with:
    param: Test

call-custom-action:
  name: Call Custom Action
  runs-on: ubuntu-latest
  steps:
    - name: Call Action
      uses: access-nri/reproducibility/.github.actions/<action_name>@<commit>
      with:
        other-param: Test
```
